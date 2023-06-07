## 事件循环

> 了解 JavaScript 执行机制。它是单线程语言。

那么接下来，就看看 js 是怎么执行的。

### 宏任务 宏队列，macrotask，这些异步任务包括：

- setTimeout
- setInterval
- requesetAnimation
- UI rendering
  ..

### 微任务 ,microtask 这些微任务包括

- Promise
- Object.observe
- process.nextTick (Node)

## 代码执行步骤

- 执行全局 Script 同步代码，这些同步代码有一些是同步语句，有一些是异步语句（比如 setTimeout 等）
- 全局 Script 代码执行完毕后，调用栈 Stack 会清空
- 从微队列 microtask queue 中取出位于队首的回调任务，放入调用栈 Stack 中执行，执行完后 microtask queue 长度减 1
- 继续取出位于队首的任务，放入调用栈 Stack 中执行，以此类推，直到直到把 microtask queue 中的所有任务都执行完毕。- 注意，如果在执行 microtask 的过程中，又产生了 microtask，那么会加入到队列的末尾，也会在这个周期被调用执行
- microtask queue 中的所有任务都执行完毕，此时 microtask queue 为空队列，调用栈 Stack 也为空
- 取出宏队列 macrotask queue 中位于队首的任务，放入 Stack 中执行
- 执行完毕后，调用栈 Stack 为空
- 重复第 3-7 个步骤

简单点概括就是同步代码顺序执行，碰到微任务进微队列，碰到宏任务进宏队列。同步代码执行完先将微队列中的所有任务执行完，微队列执行过程中碰到的微任务放到队列尾部这次一并执行。微队列执行完毕后执行宏队列头部的任务，执行完成之后再进微队列，执行完所有的微任务，依次循环，直到所有任务执行完毕

案例检测

```javascript
console.log(1);

setTimeout(() => {
  console.log(2);
  Promise.resolve().then(() => {
    console.log(3);
  });
}, 0);

new Promise((resolve, reject) => {
  console.log(4);
  resolve(5);
}).then((res) => {
  console.log(res);
});

setTimeout(() => {
  console.log(6);
}, 0);

console.log(7);
// 输出结果1 4 7 5 2 3 6


```
以上代码的输出结果你猜对了吗？我们来逐行分析

- 第一行，console.log直接输出1
- 然后碰到第一个setTimeout,setTimeout属于宏任务，进入宏队列，继续往下执行
- 碰到一个promise（注意：promise只有resolve/reject才算异步）,所以这里4直接输出，resolve进入微队列
- 碰到第二个setTimeout，进宏队列
- 7直接输出
- 然后看微队列，之前进队列的依次出, 队列里面只有一个promise输出5，然后队列为空
- 然后执行宏队列第一个，输出2，promise进微队列
- 执行微队列，输出3，微队列为空
- 执行宏队列第一个，输出6，到这里所有队列为空执行完毕

```javascript
Promise.resolve()
  .then(() => {
    console.log("mm");
    Promise.resolve()
      .then(() => {
        console.log("xx");
      })
      .then(() => {
        console.log("yy");
      });
  })
  .then(() => {
    console.log("nn");
  });

// 输出 "mm  xx nn  yy"
```

这段代码的输出结果你猜对了吗？我们再来分析：

- 首先看代码中只有一个大promise，先执行，mm直接输出，promise进微队列，内部执行完毕
- 然后外层.then进微队列
- 执行微队列，输出xx，碰到内部.then加入队列尾部
- 输出nn
- 输出yy
