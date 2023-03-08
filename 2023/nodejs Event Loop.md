---
order: 27
tags: nodejs
---

# NodeJS Event Loop

事件循环是在 libuv 中实现的，libuv 对系统异步任务（network io -> 基于 epoll、kqueue...）和同步任务(file io、crypto、dns... -> 基于线程池) 进行了封装，用于执行多任务，任务执行结束后结果推入事件队列中（每个阶段有自己的队列），再被事件循环取出执行。

有几个特殊的地方需要留意：

1、启动时不一定先执行 timer phase，因为 timer 最小不是 0，所以进入 eventloop 时 timer 是否到期取决于机器性能。

```js
// 机器性能好先 check，性能差先 timer
setTimeout(() => {
  console.log("timeout");
});
setImmediate(() => {
  console.log("immediate");
});
```

2、node 10 之前执行完每个阶段的宏任务后才清空微任务，12 及以后改成和浏览器一样一个宏任务就接着全部微任务，然后继续取当前阶段队列的宏任务执行

```js
// node 10 immediate 1 -> immediate 2 -> micro-task
// node 12 及以后 immediate 1 -> micro-task -> immediate 2
setImmediate(() => {
  console.log("immediate 1");
  Promise.resolve().then(() => {
    console.log("micro-task");
  });
});
setImmediate(() => {
  console.log("immediate 2");
});
```

3、迭代注册测试，一个阶段任务没执行完，不会进入下一个阶段

```js
// 不可用递归测试，递归不是同步注册完一堆事件，混入了其他因素
const fs = require("fs");
// 从 poll 开始
fs.readFile(__filename, () => {
  for (let i = 0; i < 1e6; i++) {
    setTimeout(() => {
      console.log("timeout");
    });
  }
  for (let i = 0; i < 1e6; i++) {
    setImmediate(() => {
      console.log("immediate");
    });
  }
});
for (let i = 0; i < 1e9; i++) {} // 消耗时间 hit timer
```

4、nextTickQueue is implemented in node，microTaskQueue is implemented in V8，[no of them belong to EventLoop](<https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick/#:~:text=This%20is%20because%20process.nextTick()%20is%20not%20technically%20part%20of%20the%20event%20loop>)，nextTickQueue has a higher priority.

```js
Promise.resolve().then(() => console.log("promise"));
process.nextTick(() => console.log("nextTick"));
```

```js
// 若当前已经在 microTaskQueu 时，会先执行当前队列而不是切换到 nextTickQueue
queueMicrotask(() => {
  queueMicrotask(() => {
    console.log("microTask");
  });
  process.nextTick(() => {
    console.log("nextTick");
  });
});
```

提示：代码逻辑最好不要和事件循环执行优先级耦合，起码在对 node 的底层逻辑有充分的掌控自信之前，因为不同 node 版本，操作系统，机器性能等因素都可能都会影响代码的执行结果

📎 附一个相关 [issue](https://github.com/stackblitz/webcontainer-core/issues/980)
