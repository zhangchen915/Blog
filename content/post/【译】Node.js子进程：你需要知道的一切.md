---
title: "【译】Node.js子进程：你需要知道的一切"
categories: ["Node.js"]
tags: ["Node.js"]
draft: false
slug: "360"
date: "2017-07-23 20:06:00"
---

[原文地址][1]
如何使用 spawn(), exec(), execFile(), and fork()

Node.js中的单线程，非阻特性非常适合单个进程。但是，一个CPU中的一个进程终归不足以应付应用程序日益增加的工作量。无论您的服务器可能有多强大，单个线程只能支持有限的负载。Node.js在单个线程中运行的事实并不意味着我们不能利用多个进程，当然也可以利用多个机器。

使用多个进程是扩展Node应用程序的最佳方式。 Node.js设计用于构建具有许多节点的分布式应用程序。这就是为什么它被命名为Node。可扩展性被放入平台，而不是在应用程序的生命周期中开始考虑的事情。

请注意，在阅读本文之前，您需要对Node.js事件和流的理解很好。如果你还没有，我建议你阅读这两篇文章之前阅读这篇文章：
[了解Node.js事件驱动架构][2]
[Node.js Streams：你需要知道的一切][3]

## 子过程模块
我们可以使用Node的`child_process`模块​​轻松地转换子进程，这些子进程可以通过消息传递系统轻松地进行通信。我们可以控制子进程输入流，并监听其输出流。我们还可以控制要传递给底层操作系统命令的参数，我们可以使用该命令的输出来做任何我们想要的操作。例如，我们可以将一个命令的输出管道作为另一个命令的输入（就像我们在Linux中一样），因为这些命令的所有输入和输出都可以使用Node.js流呈现给我们。

请注意，本文中将使用的示例都是基于Linux的。在Windows上，您需要使用Windows替代方案切换我使用的命令。

在Node中创建子进程有四种不同的方法：spawn(), exec(), execFile(), and fork()。我们将看到这四个功能之间的差异以及何时使用它们。

## Spawned Child Processes
spawn函数在新进程中启动命令，我们可以使用它来传递该命令任何参数。例如，这里是生成一个将执行pwd命令的新进程的代码。
```
const { spawn } = require('child_process');
const child = spawn('pwd');
```
我们简单地从child_process模块​​中重新构建spawn函数，并使用OS命令作为第一个参数来执行。
执行spawn函数（上面的子对象）的结果是一个ChildProcess实例，它实现EventEmitter API。这意味着我们可以直接在此子对象上注册事件的处理程序。例如，当子进程退出时，可以通过注册退出事件的处理程序来执行某些操作：
```
child.on('exit', function (code, signal) {
  console.log('child process exited with ' +
              `code ${code} and signal ${signal}`);
});
```
上面的处理程序给出了子进程的退出代码以及用于终止子进程的信号（如果有的话）。当子进程正常退出时，该信号变量为空。我们可以使用ChildProcess实例注册处理程序的其他事件是 disconnect, error, close, 和 message。

- 当父进程手动调用child.disconnect函数时，将触发 disconnect 事件。
- 如果进程无法生成或被杀死，则会发出error 事件。
- 当子进程的stdio流关闭时，关闭事件被发出。
- message 事件是最重要的事件。当子进程使用process.send（）函数发送消息时，它被发出。这是父/子进程可以相互通信的方式。我们将在下面看到一个例子。

每个子进程也可以获取三个标准的stdio流，我们可以使用child.stdin，child.stdout和child.stderr访问它们。

当这些流被关闭时，正在使用它们的子进程将发出关闭事件。此关闭事件与退出事件不同，因为多个子进程可能共享相同的stdio流，因此一个子进程退出并不意味着流已关闭。

由于所有流都是事件发射器，因此我们可以收听附加到每个子进程的stdio流上的不同事件。与正常进程不同的是，在子进程中，stdout / stderr流是可读流，而stdin流是可写的。这基本上是在主流程中发现的那些类型的倒数。我们可以使用这些流的事件是标准的。我们可以使用这些流的事件是标准的。最重要的是，在可读流中，我们可以监听数据事件，这将在命令的输出或执行命令时遇到任何错误：
```
child.stdout.on('data', (data) => {
  console.log(`child stdout:\n${data}`);
});

child.stderr.on('data', (data) => {
  console.error(`child stderr:\n${data}`);
});
```
上面的两个处理程序将会将两个案例记录到主进程stdout和stderr。当我们执行上面的spawn函数时，pwd命令的输出被打印，子进程退出代码0，这意味着没有发生错误。

我们可以使用spawn函数的第二个参数传递由spawn函数执行的命令的参数，该函数是要传递给命令的所有参数的数组。 例如，要使用-type f参数在当前目录下执行find命令（仅列出文件），我们可以做：

const child = spawn('find', ['.', '-type', 'f']);

如果在命令执行期间发生错误，例如，如果我们给出上面找到一个无效的目标，则会触发child.stderr data 事件处理程序，并且exit 事件处理程序将报告1的退出代码，这表示一个 发生错误 错误值实际上取决于主机操作系统和错误类型。

子进程stdin是一个可写的流。 我们可以使用它来发送命令一些输入。 就像任何可写的流一样，消耗它的最简单的方法是使用管道（pipe）功能。 我们只需将一条可读的流管理到一个可写的流中。 因为主进程stdin是一个可读的流，我们可以将它管道到一个子进程stdin流中。 例如：
```
const { spawn } = require('child_process');

const child = spawn('wc');

process.stdin.pipe(child.stdin)

child.stdout.on('data', (data) => {
  console.log(`child stdout:\n${data}`);
});
```
在上面的例子中，子进程调用wc命令，它在Linux中排列行，字和字符。 然后我们将主进程stdin（这是一个可读流）管道进入子进程stdin（这是一个可写的流）。 这种组合的结果是我们得到一个标准的输入模式，我们可以输入一些东西，当我们按Ctrl + D时，我们输入的内容将被用作wc命令的输入。

我们也可以管理多个进程的标准输入/输出，就像我们可以用Linux命令一样。 例如，我们可以将find命令的stdout引导到wc命令的stdin来计数当前目录中的所有文件：
```
const { spawn } = require('child_process');

const find = spawn('find', ['.', '-type', 'f']);
const wc = spawn('wc', ['-l']);

find.stdout.pipe(wc.stdin);

wc.stdout.on('data', (data) => {
  console.log(`Number of files ${data}`);
});
```
我添加了-l参数到wc命令，使它只计算行。执行时，上述代码将输出当前所有目录下所有文件的计数。

## Shell语法和exec函数
默认情况下，spawn函数不会创建一个shell来执行我们传入的命令。 这使得它比exec函数稍微高效一点，它创建一个shell。 exec函数有另外一个主要区别。 它缓冲命令的生成输出，并将整个输出值传递给一个回调函数（而不是使用流，这是什么spawn）。
这是以前的find | wc示例使用exec函数实现。
```
const { exec } = require('child_process');

exec('find . -type f | wc -l', (err, stdout, stderr) => {
  if (err) {
    console.error(`exec error: ${err}`);
    return;
  }

  console.log(`Number of files ${stdout}`);
});
```
由于exec函数使用shell来执行命令，所以我们可以直接使用shell语法来利用shell管道功能。
请注意，如果您正在执行外部提供的任何种类的动态输入，那么使用shell语法会带来安全隐患。 用户可以使用shell语法字符简单地执行命令注入攻击，如 ; 和 $（例如`command + ’; rm -rf ~’`）

exec函数缓冲输出，并将其传递给回调函数（exec的第二个参数）作为stdout参数。 这个stdout参数是我们要打印输出的命令的输出。
如果需要使用shell语法，并且从命令中预期的数据的大小很小，那么exec函数是一个很好的选择。 （记住，exec将在返回之前缓冲内存中的整个数据。）
当命令的预期数据大小时，生成函数是一个更好的选择，因为该数据将与标准IO对象一起流式传输。

如果我们想要，我们可以使生成的子进程继承其父进程的标准IO对象，更重要的是，我们可以使spawn函数也使用shell语法。这是使用spawn函数实现的相同的`find | wc`命令：
```
const child = spawn('find . -type f', {
  stdio: 'inherit',
  shell: true
});
```
由于上述stdio：'inherit'选项，当我们执行代码时，子进程继承主进程stdin，stdout和stderr。这将导致子进程数据事件处理程序在主process.stdout流中被触发，从而使脚本立即输出结果。
由于上面的shell：true选项，我们可以在传递的命令中使用shell语法，就像我们用exec一样。但是使用这段代码，我们仍然可以获得产生函数给我们的数据流的优势。这真的是两个世界最好的。
除了shell和stdio之外，还有一些其他很好的选择，我们可以在child_process函数的最后一个参数中使用。例如，我们可以使用cwd选项来更改脚本的工作目录。例如，这里使用一个使用外壳程序的spawn函数，并将工作目录设置为“我的下载”文件夹，这是完全相同的计数全文件示例。这个cwd选项将使脚本计数我在`~/Downloads`中的所有文件：
```
const child = spawn('find . -type f | wc -l', {
  stdio: 'inherit',
  shell: true,
  cwd: '/Users/samer/Downloads'
});
```
我们可以使用的另一个选项是env选项来指定新子进程可见的环境变量。 该选项的默认值为process.env，它允许任何命令访问当前进程环境。 如果我们想重写这个行为，我们可以简单地传递一个空对象作为env选项或者新的值被认为是唯一的环境变量：
```
const child = spawn('echo $ANSWER', {
  stdio: 'inherit',
  shell: true,
  env: { ANSWER: 42 },
});
```
上面的echo命令无法访问父进程的环境变量。 例如，它不能访问$HOME，但它可以访问$ANSWER，因为它通过env选项作为自定义环境变量传递。
这里解释的最后一个重要的子进程选项是分离选项，这使得子进程独立于其父进程运行。
假设我们有一个保持事件循环繁忙的timer.js文件：
```
setTimeout(() => {  
  // keep the event loop busy
}, 20000);
```
We can execute it in the background using the detached option:
```
const { spawn } = require('child_process');

const child = spawn('node', ['timer.js'], {
  detached: true,
  stdio: 'ignore'
});

child.unref();
```
The exact behavior of detached child processes depends on the OS. On Windows, the detached child process will have its own console window while on Linux the detached child process will be made the leader of a new process group and session.
If the unref function is called on the detached process, the parent process can exit independently of the child. This can be useful if the child is executing a long-running process, but to keep it running in the background the child’s stdio configurations also have to be independent of the parent.
The example above will run a node script (timer.js) in the background by detaching and also ignoring its parent stdio file descriptors so that the parent can terminate while the child keeps running in the background.

Gif captured from my Pluralsight course — Advanced Node.js
The execFile function
If you need to execute a file without using a shell, the execFile function is what you need. It behaves exactly like the exec function, but does not use a shell, which makes it a bit more efficient. On Windows, some files cannot be executed on their own, like .bat or .cmd files. Those files cannot be executed with execFile and either exec or spawn with shell set to true is required to execute them.
The *Sync function
The functions spawn, exec, and execFile from the child_process module also have synchronous blocking versions that will wait until the child process exits.
```
const { 
  spawnSync, 
  execSync, 
  execFileSync,
} = require('child_process');
```
Those synchronous versions are potentially useful when trying to simplify scripting tasks or any startup processing tasks, but they should be avoided otherwise.
The fork() function
The fork function is a variation of the spawn function for spawning node processes. The biggest difference between spawn and fork is that a communication channel is established to the child process when using fork, so we can use the send function on the forked process along with the global process object itself to exchange messages between the parent and forked processes. We do this through the EventEmitter module interface. Here’s an example:
The parent file, parent.js:
```
const { fork } = require('child_process');

const forked = fork('child.js');

forked.on('message', (msg) => {
  console.log('Message from child', msg);
});

forked.send({ hello: 'world' });
The child file, child.js:
process.on('message', (msg) => {
  console.log('Message from parent:', msg);
});

let counter = 0;

setInterval(() => {
  process.send({ counter: counter++ });
}, 1000);
```
In the parent file above, we fork child.js (which will execute the file with the node command) and then we listen for the message event. The message event will be emitted whenever the child uses process.send, which we’re doing every second.
To pass down messages from the parent to the child, we can execute the send function on the forked object itself, and then, in the child script, we can listen to the message event on the global process object.
When executing the parent.js file above, it’ll first send down the { hello: 'world' } object to be printed by the forked child process and then the forked child process will send an incremented counter value every second to be printed by the parent process.

Screenshot captured from my Pluralsight course — Advanced Node.js
Let’s do a more practical example about the fork function.
Let’s say we have an http server that handles two endpoints. One of these endpoints (/compute below) is computationally expensive and will take a few seconds to complete. We can use a long for loop to simulate that:
```
const http = require('http');
const longComputation = () => {
  let sum = 0;
  for (let i = 0; i < 1e9; i++) {
    sum += i;
  };
  return sum;
};
const server = http.createServer();
server.on('request', (req, res) => {
  if (req.url === '/compute') {
    const sum = longComputation();
    return res.end(`Sum is ${sum}`);
  } else {
    res.end('Ok')
  }
});

server.listen(3000);
```
This program has a big problem; when the the /compute endpoint is requested, the server will not be able to handle any other requests because the event loop is busy with the long for loop operation.
There are a few ways with which we can solve this problem depending on the nature of the long operation but one solution that works for all operations is to just move the computational operation into another process using fork.
We first move the whole longComputation function into its own file and make it invoke that function when instructed via a message from the main process:
In a new compute.js file:
```
const longComputation = () => {
  let sum = 0;
  for (let i = 0; i < 1e9; i++) {
    sum += i;
  };
  return sum;
};

process.on('message', (msg) => {
  const sum = longComputation();
  process.send(sum);
});
```
Now, instead of doing the long operation in the main process event loop, we can fork the compute.js file and use the messages interface to communicate messages between the server and the forked process.
```
const http = require('http');
const { fork } = require('child_process');

const server = http.createServer();

server.on('request', (req, res) => {
  if (req.url === '/compute') {
    const compute = fork('compute.js');
    compute.send('start');
    compute.on('message', sum => {
      res.end(`Sum is ${sum}`);
    });
  } else {
    res.end('Ok')
  }
});

server.listen(3000);
```
When a request to /compute happens now with the above code, we simply send a message to the forked process to start executing the long operation. The main process’s event loop will not be blocked.
Once the forked process is done with that long operation, it can send its result back to the parent process using process.send.
In the parent process, we listen to the message event on the forked child process itself. When we get that event, we’ll have a sum value ready for us to send to the requesting user over http.
The code above is, of course, limited by the number of processes we can fork, but when we execute it and request the long computation endpoint over http, the main server is not blocked at all and can take further requests.
Node’s cluster module, which is the topic of my next article, is based on this idea of child process forking and load balancing the requests among the many forks that we can create on any system.
That’s all I have for this topic. Thanks for reading! Until next time!
If you found this article helpful, please click the
