## WebWorker

JavaScript 语言采用的是单线程模型，也就是说，所有任务只能在一个线程上完成，一次只能做一件事。前面的任务没做完，后面的任务只能等着。随着电脑计算能力的增强，尤其是多核 CPU 的出现，单线程带来很大的不便，无法充分发挥计算机的计算能力。

Web Worker 并不是 JavaScript 的一部分，它是通过 JavaScript 可以访问的浏览器特性。然而，Web Worker 并没有在 Node.js 中实现

**Web Worker 的作用，就是为 JavaScript 创造多线程环境，允许主线程创建 Worker 线程，将一些任务分配给后者运行**

在主线程运行的同时，Worker 线程在后台运行，两者互不干扰。等到 Worker 线程完成计算任务，再把结果返回给主线程。这样的好处是，一些计算密集型或高延迟的任务，被 Worker 线程负担了，主线程（通常负责 UI 交互）就会很流畅，不会被阻塞或拖慢。

Worker 线程一旦新建成功，就会始终运行，不会被主线程上的活动（比如用户点击按钮、提交表单）打断。这样有利于随时响应主线程的通信。但是，这也造成了 Worker 比较耗费资源，不应该过度使用，而且一旦使用完毕，就应该关闭。

### WebWorker 的限制

-   **同源限制**
    分配给 Worker 线程运行的脚本文件，必须与主线程的脚本**文件同源**。

-   **DOM 限制**
    Worker 线程所在的全局对象，与主线程不一样，**无法读取主线程所在网页的 DOM 对象**，也无法使用 document、window、parent 这些对象。但是，Worker 线程可以 navigator 对象和 location 对象。

-   **通信联系**
    Worker **线程和主线程不在同一个上下文环境**，它们不能直接通信，必须通过消息完成。

-   **脚本限制**
    Worker 线程不能执行会中断的方法，例如**alert()方法和 confirm()**方法，但可以使用 XMLHttpRequest 对象发出 AJAX 请求。

-   **文件限制**
    Worker 线程**无法读取本地文件**，即不能打开本机的文件系统（file://），它所加载的脚本，必须来自网络。

### 主线程

Worker()构造函数的参数是一个脚本文件，该文件就是 Worker 线程所要执行的任务。由于 Worker 不能读取本地文件，所以这个脚本必须来自网络。如果下载没有成功（比如 404 错误），Worker 就会默默地失败。

然后，主线程调用 worker.postMessage()方法，向 Worker 发消息。

```javascript
var worker = new Worker("work.js")

worker.postMessage("Hello World")
worker.postMessage({ method: "echo", args: ["Work"] })

worker.onmessage = function(event) {
    console.log("Received message " + event.data)
    doSomething()
}

function doSomething() {
    worker.postMessage("Work done!")
}
```

主线程传给 Worker 的数据。它可以是各种数据类型，包括二进制数据。
接着，主线程通过 worker.onmessage 指定监听函数，接收子线程发回来的消息。

### 子线程

Worker 线程内部需要有一个监听函数，监听 message 事件。

```javascript
self.addEventListener(
    "message",
    function(e) {
        self.postMessage("You said: " + e.data)
    },
    false
)

// 写法二
this.addEventListener(
    "message",
    function(e) {
        this.postMessage("You said: " + e.data)
    },
    false
)

// 写法三
addEventListener(
    "message",
    function(e) {
        postMessage("You said: " + e.data)
    },
    false
)
```

### Worker 加载脚本

Worker 内部如果要加载其他脚本，有一个专门的方法 importScripts()，也可以同时加载多个脚本

```javascript
importScripts("script1.js", "script2.js")
```

### 错误处理

主线程可以监听 Worker 是否发生错误。如果发生错误，Worker 会触发主线程的 error 事件。

```javascript
worker.onerror(function(event) {
    console.log(["ERROR: Line ", e.lineno, " in ", e.filename, ": ", e.message].join(""))
})

worker.addEventListener("error", function(event) {
    // ...
})
```

### 关闭 Worker

```javascript
// 主线程
worker.terminate()
// Worker 线程
self.close()
```

### 数据通信

主线程和子线程数据传输是拷贝关系，即传的是值不是地址。
可以传二进制数据即 File、Blob、ArrayBuffer 等类型

Transferable Objects 可以防止生成的拷贝造成的性能问题，但是一旦转移，主线程就不能再使用这个数据了，因为要**防止多个线程同时修改数据导致的问题**

**总结一下使用 Web Workers 的场景的共同点：**

延迟甚至不需要 DOM 操作的情况
短时间使用产生大量计算，会阻塞 UI 线程，导致卡顿的场景
预处理，异步处理的部分场景

这对于影像处理、声音处理、3D 运算很方便

格式如下：

```javascript
worker.postMessage(arrayBuffer, [arrayBuffer])
var ab = new ArrayBuffer(1)
worker.postMessage(ab, [ab])
```

#### Referer

-   http://www.ruanyifeng.com/blog/2018/07/web-worker.html
-   https://juejin.im/post/5cdd2416e51d453a51433093
