# Promise/A+ 规范理解
##  1. 术语
1. promise是一个对象或者函数，其then方法的行为符合此规范。
1. thenable是定义then方法的函数。
2. value为承诺的值
3. reason为拒绝的原因
4. exception是使用throw抛出的值

## 2. 要求
### 2.1 状态(state)
state必须处于等待(pending)、满足(fulfilled)、拒绝(rejected)这三种状态之一。

pending可以过渡到fulfilled或rejected。而fulfilled与rejected可不可变(实际上是一个有限状态机)。

### 2.2 then方法
then方法接受两个参数
```
promise.then(onFulfilled, onRejected)
```
1. onFulfilled与onRejected都是可选的参数，如果不是函数，则忽略
2. 如果onFulfilled是一个函数，那么在Promise完成之后调用，Promise的值为第一个参数，且只能调用一次(怎么控制)
3. 如果onRejected是一个函数，那么在Promise拒绝之后调用，Promise的原因为第一个参数，且只能调用一次(怎么控制)
4. onFulfilled, onRejected在执行上下文堆栈仅包含平台代码之前不得调用,即必须在一个宏任务之后运行，then方法在浏览器环境下是将其放在微任务队列中。
5. then可以在同一个承诺上多次调用(怎么注册?)

## 3. 思考
### 3.1 如何调用
```
const demo = new Promise((resolve, reject) => {
  resolve(true)
}).then(resolve => {
  console.log(resolve)
}, err => {
  console.log(err)
})
```
### 3.2 分析
1. 创建新的Promise对象，执行其中的代码。
2. then函数，在微任务队列中注册回调。

### 3.3 代码实现
[Promise](https://github.com/flycsqaq/promise-ts)