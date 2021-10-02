# FE-knowledge
前端面试知识点/常见问题

# js基础

+ **谈谈js作用域**
> **作用域是指程序源代码中定义变量的区域，规定了如何查找变量，也就是确定当前执行代码对变量的访问权限**
> 
> javascript采用的是词法作用域（也就是：静态作用域）函数的作用域在函数定义的时候就决定了。

+ **谈谈原型、原型链**
> **构造函数创建对象**
> ```javascript
> // 构造函数Person
> function Person(){
>   
> }
> 
> // 实例对象xiaoming
> var xiaoming = new Person()
> ```
> 
> **prototype**
> 
> 每个函数都有一个prototype属性。
> 
> 函数的prototype属性指向了一个对象，这个对象正是调用***该构造函数创建的实例的原型***
> 
> 那什么是原型呢？你可以这样理解：每一个JavaScript对象(null除外)在创建的时候就会与之关联另一个对象，这个对象就是我们所说的原型，每一个对象都会从原型"继承"属性。
> 
参照资料：
[https://github.com/mqyqingfeng/Blog/issues/2](https://github.com/mqyqingfeng/Blog/issues/2)


# React

+ **setState更新什么时候是同步更新，什么时候是异步更新？**
> 由React控制的事件处理程序，以及生命周期函数内调用setState不会同步更新state;
> 
> React控制之外的事件调用setState是同步更新的。
> 比如：原生js绑定的事件、setTimeout/setInterval等
> 
> **大部分开发中用到的都是React封装的事件，比如onChange、onClick、onTouchMove等，这些事件处理程序中的setState都是异步处理的。**


+ **React是怎样控制异步和同步的呢？**
> 在 React 的 setState 函数实现中，会根据一个变量 isBatchingUpdates 判断是直接更新 this.state 还是放到队列中延时更新，而 isBatchingUpdates 默认是 false，表示 setState 会同步更新 this.state；但是，有一个函数 batchedUpdates，该函数会把 isBatchingUpdates 修改为 true，**而当 React 在调用事件处理函数之前就会先调用这个 batchedUpdates将isBatchingUpdates修改为true，** 这样由 React 控制的事件处理过程 setState 不会同步更新 this.state。

+ **useLayoutEffect与useEffect的区别**
> useEffect会在渲染的内容更新到dom上之后执行，不会阻塞dom的更新。（render之后）
> 
> useLayoutEffect会在渲染的内容到dom上之前执行，会阻塞dom的更新。（dom更新后，render之前）
> 
> ***如果我们希望在某些操作发生之后再更新dom，那么应该将这个操作放在useLayoutEffect***

+ **useMemo的实现原理/手写一个简易版的useMemo**

函数返回一个值，支持缓存，减少不需要的更新
```javascript
let hookStates = []
let hookIndex = 0
function useMemo(callback, dependencies) {
  if (hookStates[hookIndex]) {
    let [lastMemo, lastDependencies] = hookStates[hookIndex]
    // 判断前后的依赖项，数值是否变化
    let same = dependencies.every(
      (item, index) => item === lastDependencies[index]
    )
    if (same) {
      // 依赖未变化
      hookIndex++
      return lastMemo
    }
  }
  const nextValue = callback()
  // 首次渲染或者依赖项存在数值变化
  hookStates[hookIndex++] = [nextValue, dependencies]
  return nextValue
}
```
+ **useCallback的实现原理/手写一个简易版的useCallback**

其实useCallback的实现方式与useMemo一致，不同的只是一个返回的缓存的值，一个返回的是缓存的函数
```javascript
let hookStates = []
let hookIndex = 0
function useCallback(callback, dependencies) {
  if (hookStates[hookIndex]) {
    let [lastCallback, lastDependencies] = hookStates[hookIndex]
    // 判断前后的依赖项，数值是否变化
    let same = dependencies.every(
      (item, index) => item === lastDependencies[index]
    )
    if (same) {
      // 依赖未变化
      hookIndex++
      return lastCallback
    }
  }
  // 首次渲染或者依赖项存在数值变化
  hookStates[hookIndex++] = [callback, dependencies]
  return callback
}
```

# 网络

+ **get请求和post请求对比；为什么post比get请求安全一些?**
> **GET：**
> 
> get请求的数据会附加在url之后用问号分割，多个参数用&进行连接；
> 
> get请求的数据会暴露在地址栏中
> 
> get相对安全性较差，会被浏览器主动缓存
> 
> get请求产生一个TCP数据包，head和data一起发送
> 
> **POST：**
> 
> post请求会吧数据放在http请求包的包体重，不会直接暴露给用户。
> 
> post请求相对get更安全，因为参数不会保存在浏览器里或者web服务器日志中
> 
> post产生两个TCP数据包，headr先发送，服务器响应100状态码后继续，发送data，服务器响应200状态码后返回数据。
> 

# 设计模式

+ **责任链模式**

我们可以先看一个开发中时长会遇到的场景，一份数据可能需要经手很多环节的业务处理，最常见的代码编写方式大概会是下面这样
```javascript
function A() {
  console.log('A业务处理')
  B()
}
function B() {
  console.log('B业务处理')
  C()
}
function C() {
  console.log('C业务处理')
  D()
}
function D() {
  console.log('D业务处理')
  console.log('end')
}
```
上面的代码可以实现基本的业务需求，但并不优雅，一旦我们需要在B与C中间再添加一个业务逻辑，那我们就需要去修改B函数里调用C方法的代码。

所以，责任链设计模式就可以用于优化这样的编码场景

首先，我们先创建一个责任链的工具类
```javascript
class Chain {
  constructor(handler) {
    this.handler = handler
  }
  // 设置链式关系
  setNextChain(nextChain) {
    this.nextChain = nextChain
  }
  // 执行当前业务
  handleExec() {
    let result = this.handler.apply(this, arguments)
    if (result === 'next') {
      this.nextChain.handleExec.apply(this, arguments)
    }
  }
}
```
根据上述类的实现方式，我们修改一下原先的ABCD四个业务方法
```javascript
function A() {
  console.log('A业务处理')
  return 'next'
}
function B() {
  console.log('B业务处理')
  return 'next'
}
function C() {
  console.log('C业务处理')
  return 'next'
}
function D() {
  console.log('D业务处理')
  console.log('end')
}
```
接下来我们就可以通过创建责任链实例，将业务调用链，连接起来。
```javascript
// 创建调用链节点
const chainA = new Chain(A)
const chainB = new Chain(B)
const chainC = new Chain(C)
const chainD = new Chain(D)

// 关联调用顺序关系
chainA.setNextChain(chainB)
chainB.setNextChain(chainC)
chainC.setNextChain(chainD)

// 执行第一个调用节点
chainA.handleExec()
```
根据上面的简单代码实现，哪怕需要改变ABCD的调用顺序，也不需要去具体函数里面调整执行调用了，在我们维护的责任链里更新链式关系即可。

+ **单例模式**

传统单例
```javascript
// 传统单例
var Single = function () {
  this.instance = null
}
Single.prototype.getInstance = function () {
  if (!this.instance) {
    this.instance = new Single()
  }
  return this.instance
}

var fnA = Single.getInstance()
var fnB = Single.getInstance()
```

闭包函数实现单例
```javascript
// 闭包函数单例
var Single = function () {
  // todo
}
var SingleProxy = (function () {
  // 匿名自执行函数
  var instance
  return function () {
    if (!instance) {
      instance = new Single()
      return instance
    }
    return instance
  }
})()
var fnA = new SingleProxy()
var fnB = new SingleProxy()
```
