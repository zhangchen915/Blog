---
title: "【译】当你调用 console.log 时发生了什么"
categories: ["前端"]
tags: []
draft: false
slug: "808"
date: "2020-05-27 20:24:00"
---

> 原文地址 https://keleshev.com/standard-io-under-the-hood 

以V8为例，首先调用一个通用方法WriteToFile：

```cpp
void D8Console::Log(const debug::ConsoleCallArguments& args,
                    const v8::debug::ConsoleContext&) {
  WriteToFile(nullptr, stdout, isolate_, args);
}
```
https://github.com/v8/v8/blob/4b9b23521e/src/d8-console.cc#L52-L55

在计算出Javascript的值之后，最终会调用fwrite。
```cpp
void WriteToFile(const char* prefix, FILE* file, Isolate* isolate,
                 const debug::ConsoleCallArguments& args) {
  if (prefix) fprintf(file, "%s: "⁠, prefix);
  for (int i = 0; i < args.Length(); i++) {
    HandleScope handle_scope(isolate);
    if (i > 0) fprintf(file, " ");

    Local arg = args[i];
    Local str_obj;

    if (arg->IsSymbol()) arg = Local::Cast(arg)->Name();
    if (!arg->ToString(isolate->GetCurrentContext()).ToLocal(&str_obj)) return;

    v8::String::Utf8Value str(isolate, str_obj);
      int n = static_cast<int>(fwrite(*str, sizeof(**str), str.length(), file));

    if (n != str.length()) {
      printf("Error in fwrite\n");
      base::OS::ExitProcess(1);
    }
  }
  fprintf(file, "\n");
}
```
https://github.com/v8/v8/blob/4b9b23521e/src/d8-console.cc#L26

函数fwrite是C标准库（也称为libc）的一部分。在不同平台上有几种libc实现。在Linux上，流行的是glibc和musl。让我们用 musl。在那里，fwrite用C实现如下：
```cpp
size_t fwrite(const void *restrict src, size_t size, size_t nmemb, FILE *restrict f)
{
    size_t k, l = size*nmemb;
    if (!size) nmemb = 0;
    FLOCK(f);
    k = __fwritex(src, l, f);
    FUNLOCK(f);
    return k==l ? nmemb : k/size;
}
```
https://github.com/bminor/musl/blob/05ac345f89/src/stdio/fwrite.c#L28-L36

进行一些间接操作后，这将调用一个实用程序函数__stdio_write，然后将进行（操作系统）系统调用 writev。

```cpp
size_t __stdio_write(FILE *f, const unsigned char *buf, size_t len)
{
    struct iovec iovs[2] = {
        { .iov_base = f->wbase, .iov_len = f->wpos-f->wbase },
        { .iov_base = (void *)buf, .iov_len = len }
    };
    struct iovec *iov = iovs;
    size_t rem = iov[0].iov_len + iov[1].iov_len;
    int iovcnt = 2;
    ssize_t cnt;
    for (;;) {
          cnt = syscall(SYS_writev, f->fd, iov, iovcnt);

        /* … */
    }
}
```
https://github.com/bminor/musl/blob/05ac345f89/src/stdio/__stdio_write.c#L15

syscall这里的符号是一个宏，经过一些预处理程序后，它将扩展为__syscall3。

操作系统之间的系统调用不同，并且处理器体系结构之间的执行方式也不同。它通常需要编写（或生成）一些汇编。在x86-64上，musl定义__syscall3如下：

```cpp
static __inline long __syscall3(long n, long a1, long a2, long a3)
{
    unsigned long ret;
    __asm__ __volatile__ ("syscall" : "=a"(ret) : "a"(n), "D"(a1), "S"(a2),
                          "d"(a3) : "rcx"⁠, "r11"⁠, "memory");
    return ret;
}
```
https://github.com/bminor/musl/blob/593caa4563/arch/x86_64/syscall_arch.h#L26-L32

这将设置系统调用号和参数。在x86-64上，调用进行系统调用的指令syscall。

进行系统调用后，控制将转移到（在这种情况下为Linux）内核。这就是另一码事了...
