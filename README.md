# FE-knowledge
前端面试知识点/常见问题

# js基础

+ **谈谈变量提升**
> 为什么会出现变量提示？
> 
> 因为js与其他语言一样，都要经历编译和执行阶段。而在js编译阶段的时候，就会搜集所有的变量声明并且提前声明变量，而其他的语句都不会改变他们的顺序，因此，在编译阶段的时候，第一步（声明）就已经执行了，而第二步（赋值）则是在执行阶段执行到该语句的时候才执行。
> 
> *当有多个同名变量的时候，函数声明会覆盖其他的声明；如果有多个函数声明，则由最后一个函数声明覆盖之前的所有声明*

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

+ **手动实现apply、call、bind**
> 如何显式地绑定this？
> 
> **如果调用者函数，被某一个对象所拥有，那么该函数在调用时，内部的this指向该对象。**
> 
> 手写实现apply：
>
> ```javascript
> 
> Function.prototype.myApply = function (context, args) {
>     //这里默认不传就是给window,也可以用es6给参数设置默认参数
>     var cxt = context || window
>     args = args ? args : []
>     cxt.fn = this
>     //通过隐式绑定的方式调用函数
>     const result = cxt.fn(...args)
>     //删除添加的属性
>     delete cxt.fn
>     //返回函数调用的返回值
>     return result
> }
> 
> ```
>
> 手写实现call：
>
> ```javascript
> //传递参数从一个数组变成逐个传参了,不用...扩展运算符的也可以用arguments代替
> Function.prototype.myCall = function (context, ...args) {
>     //这里默认不传就是给window,也可以用es6给参数设置默认参数
>     var cxt = context || window
>     args = args ? args : []
>     cxt.fn = this
>     //通过隐式绑定的方式调用函数
>     const result = cxt.fn(...args)
>     //删除添加的属性
>     delete cxt.fn
>     //返回函数调用的返回值
>     return result
> }
> ```
>
> 手写实现bind：
>
> ```javascript
> Function.prototype.myBind = function (context, ...args) {
>     const fn = this
>     args = args ? args : []
>     return function newFn(...newFnArgs) {
>         if (this instanceof newFn) {
>             return new fn(...args, ...newFnArgs)
>         }
>         return fn.apply(context, [...args,...newFnArgs])
>     }
> }
> ```
>

+ **ES6的拓展符（...）和Object.assign()有何差异？**
> 拓展运算符和 Object.assign() 的表现基本是一致的
> 不过，Object.assign() 使用源对象的 [[Get]] 和目标对象的 [[Set]] ，会调用相关 getter 和 setter。因此，它分配属性，而不仅仅是复制或定义新的属性。而拓展运算符不会。

+ **async函数**
> 无论async函数有无await操作，其总是返回一个promise。
> 1. 没有显示return，相当于return Promise.resolve(undefined)
> 2. return非Promise的数据data，相当于return Promise.resolve(data)
> 3. return Promise,会得到Promise对象本身
> 
> async函数总是返回Promise，因此，其后面可以直接调用then方法，函数内部return返回的值，会成为then回调函数的参数；函数内部抛出的错误，会被then的第二个函数或catch方法捕获到；

+ **前端错误监控**
> **前端常见的错误类型**
> 
> 语法错误：
> - SyntaxError（解析时发生语法错误）
> - TypeError（数值类型错误）
> - ReferenceError（引用未声明的变量）
> 
> 网络错误：
> - ResourceError（资源加载错误）
> - HttpError（http请求错误）
> 
> **如何采集错误**
> ```javascript
> // 常规运行时错误、异步错误
> window.onerror = function(message, source, lineno, colno, error) {
>  console.log('捕获到异常：',{message, source, lineno, colno, error});
> }
> // 图片、script、css加载错误，都能被捕获
> <script>
>   window.addEventListener('error', (error) => {
>      console.log('捕获到异常：', error);
>   }, true)
> </script>
> ```
> 
> **为什么选择使用new Image()实现数据上报？**
> ```javascript
> (new Image()).src="http://post.error.com?data=xxx"
> ```
> - 可以进行跨域
> - 不会携带cookie
> - 不需要等待服务器返回数据



+ **Redux核心思想**
> **唯一数据源（state）**：数据来源均直接或者间接地来自于state
> 
> **数据源（state）只读**：不可直接修改state数据，而是基于原state更新返回一份新的数据作为数据源
> 
> **通过纯函数（reducer）改变数据源**：pure function：无其他api调用，无异步操作


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

+ **useState的简易实现**

useState是内部是利用数组来存储值的，而且是利用数组的下标来区分不同的值得。如果把useState用在条件语句中，数组的下标就会产生混乱。

```javascript
const CreateUseState = () => {
  const state = []
  let index = 0
  return (initialValue) => {
    state[index] = state[index] || initialValue
    const currentIndex = index
    const dispatch = (newState) => {
      state[currentIndex] = newState
      //
      index = 0
      // 触发react执行重新更新渲染的方法
      render()
    }
    index++
    return [state[currentIndex], dispatch]
  }
}
const useState = CreateUseState()
const [data, setData] = useState(0)
```

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

# Vue

+ **谈谈Vue2.0响应式原理**

借用一下掘金上看到的一张流程图：

![图片来源 https://juejin.cn/post/6844904096382582797](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/3/18/170edf7e0a7291d7~tplv-t2oaga2asx-watermark.awebp)

(图片来源 https://juejin.cn/post/6844904096382582797 若侵删)
```javascript
function defineReactive(obj: Object, key: string, ...) {
	const dep = new Dep()

	Object.defineProperty(obj, key, {
		enumerable: true,
		configurable: true,
		get: function reactiveGetter() {
        ....
			dep.depend()
        return value
        ....
      },
		set: function reactiveSetter(newVal) {
        ...
		val = newVal
        dep.notify()
        ...
      }
    })
  }

class Dep {
	static target: ?Watcher;
	subs: Array<Watcher>;

	depend() {
		if (Dep.target) {
			Dep.target.addDep(this)
		}
	}

	notify() {
		const subs = this.subs.slice()
		for (let i = 0, l = subs.length; i < l; i++) {
			subs[i].update()
		}
	}
```
> 1. 组件初始化的时候，会先给data的每一个属性都注册getter、setter，也就是reactive化（此处通过Object.defineProperty()实现）
> 组件会new一个自己的watcher对象，此时watcher会立即调用组件的render函数去生成虚拟dom。在调用render的时候，就会需要用到data的属性值，此时会触发getter函数，将当前的watcher函数注册进sub里。
> 2. 当data属性发生改变之后，就会遍历sub里所有的watcher对象，通知他们去重新渲染组件。

+ **Vue中的`vm.$set(target,key,value)`这个api都作了些什么？**
> + 当target为数组时，直接调用数组方法splice实现
> + 如果target是对象，会先判断属性是否存在，对象是否是响应式
> 	- 最终如果要对属性进行响应式处理，则是用过调用`defineReactive`方法进行响应式处理 

+ **怎么给Vue定义全局方法？**
> + 将方法挂载在Vue.prototype上面（原型链方式实现）
> + 利用全局混入（mixin）
> ```javascript
> // 在项目入口的main.js里配置
> import Vue from 'vue'
> import mixin from '@/mixin'
> Vue.mixin(mixin)
> ```
> + 使用Plugin方式（本质和原型链一致）
> ```javascript
> // 在项目入口的main.js里配置
> import Vue from 'vue'
> import plugin from '@/plugin'
> Vue.use(plugin)
> ```
> + 全局函数

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

+ **webSocket与传统的http有什么优势**
> - 客户端与服务端只需要一个TCP连接，比http长轮询使用更少的连接
> - webSocket服务端可以推送数据到客户端
> - 更轻量的协议头，减少数据传输量

+ **谈谈svg格式的图片在前端性能优化中的优势**
> svg是一种矢量图形格式，它遵从xml语法，并用文本格式的描述语言来描述图像内容，因此是一种和图像分辨率无关的图形格式；
> 
> 而gif/jpge/png等都是位图，改变其尺寸大小图像会变形或出现马赛克模糊情况。
> 
> **svg优势**
> 1. 任务缩放
> 2. 文本独立：svg图像中的文字独立于图像，文字保留可编辑和可搜寻的状态。
> 3. 较小文件体积：总体来讲，svg文件比那些gif/png格式的文件要小很多，因而下载也更快。
> 4. 超强显示效果：svg图像在屏幕上总是边缘清晰
> 5. 超级颜色控制：svg图像提供1600万种颜色的调色板，支持ICC颜色描述文件标准、RGB、线性填充、渐变和蒙版
> 6. 交互性和智能化：
> 

+ **从输入一个url谈起**
1. DNS查询
> 与服务器交互首先要进行DNS查询，得到服务器的IP地址。浏览器会首先查询自己的缓存，之后会查询本地host，如果仍然没有找到会发起向DNS服务器的查询请求。
> 
> 优化点：**使用`dns-prefetch`进行DNS预查询**
> 
> 我们可以在html头部插入`<link rel="dns-prefetch" href="https://fonts.googleapis.com/">`来告诉浏览器，我们简要从这个地址（通常会是存放静态资源的CDN地址）拉取数据，浏览器会在加载到该标签的时候进行DNS预查询，在实际加载对应域名下的静态资源时，则可以直接拿到对应的IP了。
2. 建立http（TCP）连接
> 优化点：**http1.x可开启`keep-alive`**
> 
> 由于TCP的可靠性，每条独立的TCP 连接都会进行三次握手，从network的分析中可以得到：握手往往会消耗大部分的时间，真正的数据传输反而会少一些。而开启`keep-alive`之后，TCP连接会保持一段时间不断开，后续的请求都会复用这条TCP连接，不过由于管道化的原因，也会发生【队头阻塞】的问题
> 
> **HTTP/1.x的 keep-alive与HTTP/2的多路复用 区别是什么？**
> 
> - http1.x是基于文本传输，只能整体去传；http2.0是基于二进制流，可以分解为独立的帧，交错发送；
> - http1.x keep-alive必须按照请求发送的顺序返回响应；http2.0多路复用，不用按序响应；
> - http1.x keep-alive单个TCP连接在同一个时刻只能处理一个请求（两个请求的生命周期不能重叠）；http2.0单个TCP同一时刻可以发送多个请求和响应；
> 
> 优化点：**升级HTTP2.0**
> - 多路复用，通过更小的二进制帧构成多条数据流，交错的请求和响应可以【并行传输】而不被阻塞；
> - 首部压缩，如果两个请求首部（headers）相同，那么会省去这一部分，只传输不同的首部字段，减少请求的体积；
> 
> 优化点：**利用缓存**
> 
> - 强缓存：根据请求头`Expires`和`Cache-Control`判断是否命中强缓存，命中缓存的资源直接从本地加载，不会发起任何网络请求。
> ```javascript
> // 可选值
> Cache-Control: max-age=<seconds>	// 是资源从响应开始计时的最大新鲜时间
> Cache-Control: max-stale[=<seconds>]
> Cache-Control: min-fresh=<seconds>
> Cache-control: no-cache		// 会让浏览器缓存这个文件到本地但是不用
> Cache-control: no-store		// 会完全放弃缓存这个文件
> Cache-control: no-transform
> Cache-control: only-if-cached
> ```
> - 协商缓存：协商缓存一般会在缓存新鲜度过期后发起，想服务器确认是否需要更新本地的缓存文件，如果不更新，服务器会返回304，否则会重新返回整个文件。
> ```javascript
> Etag	// 就像是一个指纹，资源变化都会导致ETag变化，跟最后修改时间没有关系，Etag可以保证每一个资源是唯一的。
> Last-Modified	// 表示本地文件最后修改日期
> If-None-Match // 
> ```
> 优化点：**CDN**
> 
3. 进行页面渲染
> 优化点：**预加载/预连接内容**
> - preload （优先级较高，告知浏览器页面**必定需要**的资源，适合当前页面资源预加载）
> - prefetch (优先级较低，通常用在这个资源**可能会**在用户接下来访问的页面中出现的时候)
> 
> 优化点：**script加标记**
> - async模式：并行加载，尽快执行
> - defer模式：并行加载，待整体html解析完才执行
> 
> 优化点：**视图外的内容懒加载**
> 
> 优化点：**合并dom操作，减少无意义的回流(重排)**
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
