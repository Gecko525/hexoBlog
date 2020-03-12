---
title: javascript之宏任务与微任务
date: 2020-10-12 21:45:26
tags: javascript
---

### 事件循环

JavaScript 语言的一大特点就是单线程，也就是说，同一个时间只能做一件事。为了协调事件、用户交互、脚本、UI 渲染和网络处理等行为，防止主线程的不阻塞，Event Loop 的方案应用而生。

<!-- More -->

### 任务队列

根据规范，事件循环是通过任务队列的机制来进行协调的。一个 Event Loop 中，可以有一个或者多个任务队列(task queue)，一个任务队列便是一系列有序任务(task)的集合；每个任务都有一个任务源(task source)，源自同一个任务源的 task 必须放到同一个任务队列，从不同源来的则被添加到不同队列。`setTimeout/Promise` 等API便是任务源，而进入任务队列的是他们指定的具体执行任务。

### 宏任务

(macro)task，可以理解是每次执行栈执行的代码就是一个宏任务（**包括每次从事件队列中获取一个事件回调并放到执行栈中执行**）。

浏览器为了能够使得JS内部(macro)task与DOM任务能够有序的执行，会在一个(macro)task执行结束后，在下一个(macro)task 执行开始前，对页面进行重新渲染，流程如下：

> (macro)task->渲染->(macro)task->...

##### 宏任务包含：

```js
script(整体代码)
setTimeout
setInterval
I/O
UI交互事件
postMessage
MessageChannel
setImmediate(Node.js 环境)
```

### 微任务

microtask,可以理解是在当前 task 执行结束后立即执行的任务。也就是说，在当前task任务后，下一个task之前，在渲染之前。

所以它的响应速度相比`setTimeout`（`setTimeout`是task）会更快，因为无需等渲染。也就是说，在某一个macrotask执行完后，就会将在它执行期间产生的所有microtask都执行完毕（在渲染前）。

##### 微任务包含：

```js
Promise.then
Object.observe
MutaionObserver
process.nextTick(Node.js 环境)
```

### 运行机制

在事件循环中，每进行一次循环操作称为 tick，每一次 tick 的任务处理模型是比较复杂的，但关键步骤如下：

- 执行一个宏任务（栈中没有就从事件队列中获取）
- 执行过程中如果遇到微任务，就将它添加到微任务的任务队列中
- 宏任务执行完毕后，立即执行当前微任务队列中的所有微任务（依次执行）
- 当前宏任务执行完毕，开始检查渲染，然后GUI线程接管渲染
- 渲染完毕后，JS线程继续接管，开始下一个宏任务（从事件队列中获取）

如图：

![](运行机制.png)

### async/await

在`async` 函数中，遇到 `await` 会跳出当前函数，并让出线程，再将`await`后面的代码放到 **微任务（microtask）队列**中。

看这道面试题：

```js
async function async1() {
    console.log('async1 start');
    await async2();
    console.log('async1 end');
}
async function async2() {
    console.log('async2');
}
 
console.log('script start');
 
setTimeout(function() {
    console.log('setTimeout');
}, 0)
 
async1();
 
new Promise(function(resolve) {
    console.log('promise1');
    resolve();
}).then(function() {
    console.log('promise2');
});
console.log('script end');
```

输出顺序为：

```js
script start
async1 start
async2
promise1
script end
async1 end
promise2
setTimeout
```

执行过程解析：

1. 同步执行, 输出`script start`
2. 遇到`setTimeout`，将里面代码放到**宏任务队列**
3. 继续往下，输出 `async1 start`，然后执行 `async2`函数，输出 `async2`
4. `async2`执行完毕，将`await async2` 后面的代码加入到 **微任务队列**
5. 继续执行`new Promise`, 同步输出`promise1`
6. 遇到 `promise.then`，加入到**微任务队列**
7. 然后同步输出 `script end`
8. 当前**宏任务**执行完毕，查看**微任务队列**，按照**先进先出原则**，依次输出`async1 end`、`promise2`
9. 执行下一个**宏任务**，里面只有一个`setTimeout`，所以最后输出 `setTimeout`



同步执行的：`script start` `async1 start` `async2`  `promise1` `script end`
微任务队列：`async1 end` `promise2`
宏任务队列：`setTimeout`



