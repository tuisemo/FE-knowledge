# FE-knowledge
前端面试知识点/常见问题

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
