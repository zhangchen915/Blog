---
title: "使用 AbortController 中断DOM请求"
categories: ["前端"]
tags: ["javascript"]
draft: false
slug: "520"
date: "2018-04-13 10:38:00"
---

AbortController 是DOM规范的一部分（注意不是JS），用来中止一个或多个DOM请求。
DOM请求可以是Promise或fetch等。

注意：Chrome 66开始支持`AbortController`。

## 中断Promise
创建`AbortController`的一个实例，并将其`signal`传递给`Promise`。

```js
const controller = new AbortController();
const signal = controller.signal;

doSomethingAsync({ signal })
	.then(result => {
		console.log(result);
	})
	.catch(err => {
		if (err.name === 'AbortError') {
			console.log('Promise Aborted');
		} else {
			console.log('Promise Rejected');
		}
	});
```

在实际的`Promise`中，你会监听到该`abort`事件被取消的信号，而`Promise`将被取消。

```js
function doSomethingAsync({signal}) {
	if (signal.aborted) return Promise.reject(new DOMException('Aborted', 'AbortError'));

	return new Promise((resolve, reject) => {
		console.log('Promise Started');
		const timeout = window.setTimeout(resolve, 2500, 'Promise Resolved')

		// Listen for abort event on signal
		signal.addEventListener('abort', () => {
			window.clearTimeout(timeout);
			reject(new DOMException('Aborted', 'AbortError'));
		});
	});
}
```

我们可以通过调用`controller.abort();`在任何地方中止Promise。

## 中断Fetch
将`signal`传递给`fetch`，同样可以通过`controller.abort();`中止`fetch`。

```js
fetch(url, {signal})
    .then((response) => {
        // Do something with the response
        updateAutocompleteMenu()
    })
    .catch((err) => {
        if (err.name === 'AbortError') {
            console.log('Fetch aborted');
        } else {
            console.error('error:', err);
        }
    })
});
```

**参考文章：**
[Abortable fetch][1]
[Cancel a JavaScript Promise with AbortController][2]


  [1]: https://developers.google.com/web/updates/2017/09/abortable-fetch
  [2]: https://www.bram.us/2017/12/13/cancel-a-javascript-promise-with-abortcontroller/
