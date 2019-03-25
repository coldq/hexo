---
title: Js杂记
date: 2017-05-17 20:35:24
tags: 
  - JS
categories: 杂记
---

### w3c 制定的 javascript 标准事件模型
#### 触发顺序

事件捕获->事件处理->事件冒泡

#### 绑定方法：
1. 直接Dom绑定
2. js的onXXX绑定
3. 添加事件监听 addEventListener(eventName,Handle,useCaptrue)  IE9.0及以上版本
               attachEvent(eventName,Handle) (IE9.0以下版本)

### 图片服务器几个原因：

1、负载均衡，减少本服务器负担，降低运行成本。
2、通过专业服务器和加速技术，实现图片访问加速。
3、防止图片上传携带病毒感染主网站服务器。
4、减少并发数限制。把图片存储在多台静态服务器，减少http请求数 IIS 链接数 ，保证更多的用户能同时并更快的打开页面。
5、将图片等非主要信息外放并限制搜索引擎跟踪，有利于搜索引擎收录主体内容，促进网站排名

### 何时应该使用etag? 何时应该使用last-modified?

- 如获取最后修改时间代价太高 (如不经常变动的动态内容), 使用etag, 然后用哈希或crc算法返回校验值；
- 如很容易最后修改时间 (如图片/js/css ), 那么毫无疑问返回最后修改时间将是消耗最小的。
- 两个都返回也是可以的.
- Etag 算法由服务器自己决定，可以是md5，可以是时间，它比 last-modified(只有秒级） 更精确。
- 分布式系统里多台机器间文件的last-modified必须保持一致，以免负载均衡到不同机器导致比对失败
- 分布式系统尽量关闭掉Etag(每台机器生成的etag都会不一样)

### Last-Modified,Etag,Expire 混合

- 通常 Last-Modified,Etag,Expire 是一起混合使用的，特别是 Last-Modified 和 Expire 经常一起使用，因为 Expire 可以让浏览器完全不发起 Http 请求，而当浏览器强制 F5 的时候又有 Last-Modified ，这样就很好的达到了浏览器段缓存的效果。
- Etag 和 Expire 一起使用时，先判断 Expire ，如果已经过期，再发起 Http 请求，如果 Etag 也过期，则返回 200 响应。如果 Etag 没有过期则返回 304 响应。
- Last-Modified,Etag,Expires 三个同时使用时。先判断 Expire ，然后发送 Http 请求，服务器先判断 last-modified ，再判断 Etag ，必须都没有过期，才能返回 304 响应。


#### addEventListener 的 useCaptrue
- fasle 默认值，目标接收冒泡，忽略捕获
- true 目标接收捕获

```js
var outDiv = document.getElementById("outDiv");
var middleDiv = document.getElementById("middleDiv");
var inDiv = document.getElementById("inDiv");
var info = document.getElementById("info");
outDiv.addEventListener("click", function () { info.innerHTML += "outDiv" + "<br>"; }, false);
middleDiv.addEventListener("click", function () { info.innerHTML += "middleDiv" + "<br>"; }, false);
inDiv.addEventListener("click", function () { info.innerHTML += "inDiv" + "<br>"; }, false);
```
- 全为 false 时，触发顺序为：inDiv、middleDiv、outDiv；
- 全为 true 时，触发顺序为：outDiv、middleDiv、inDiv；
- outDiv 为 true，其他为 false 时，触发顺序为：outDiv、inDiv、middleDiv；
- middleDiv 为 true，其他为 false 时，触发顺序为：middleDiv、inDiv、outDiv；

#### 事件类型
[MDN event](https://developer.mozilla.org/zh-CN/docs/Web/Events)
- 资源事件(资源加载)
- 网络事件(online offline)
- 焦点事件(focus blur) 不会冒泡
- Websocket事件
- 视图事件(resize scroll)
- 鼠标事件
- 键盘事件(keydown 、keypress)
- 拖放事件
- 传感器事件 (手机端可以较好利用，deviceorientation方向传感器，devicemotion加速度)

### 在 javascript 中，用于阻止默认事件的默认操作的方法是

w3c的方法是e.preventDefault()，IE则是使用e.returnValue = false;

### 闭包的应用

1. 词法作用域 ： 创建内部变量不能被随意改变，同时又可以用指定的函数接口来操作；
2. 把代码封装成闭包形式，时机成熟时再使用，比如实现柯里化和反柯里化
3. 闭包可以给对象设置私有属性并利用特权(Privileged)方法访问私有属性。
4. 面向对象，封装。

### 变量对象、活动对象
变量对象：包含函数中的所有函数和变量

变量对象有多个，其中有活动对象，活动对象就是作用域链上正在被执行和引用的变量对象。

### react-router原理

[参考](https://github.com/joeyguo/blog/issues/2)
可以将 react-router 的整个包装闭环总结为
- 回调函数：含有能够更新 react UI 的 react setState 方法。
- 注册回调：在 Router componentWillMount 中使用 history.listen 注册的回调函数，最终放在 history 模块的 回调函数数组 changeListeners 中。
- 触发回调：Link 点击触发 history 中回调函数数组 changeListeners 的执行，从而触发原来 listen 中的 setState 方法，更新了页面


### for in
1. js中for in 可以遍历对象或数组的显性属性，也就是说我们自己定义的属性是可以遍历的，那些原型上默认已有的属性，例如：Object.prototype.toString、Object.prototype.hasOwnProperty 是遍历不出来的。

2. for in循环出的值不一定是按顺序的。

3. 在原型上加扩展方法，会被for in 出来。

### JavaScript 数据类型

字符串、数字、布尔、数组、对象、Null、Undefined、Symbol

### web worker 

web worker 是运行在后台的 JavaScript，独立于其他脚本，不会影响页面的性能。您可以继续做任何愿意做的事情：点击、选取内容等等，而此时 web worker 在后台运行。

```js
w=new Worker("demo_workers.js");
```

### 减少重绘（repaint）和重排（reflow） [网页性能优化]

提高网页性能，就是要降低"重排"和"重绘"的频率和成本，尽量少触发重新渲染。
1. Dom 读写操作分离
2. 样式统一改变：不要一条条地改变样式，而要通过改变class，或者csstext属性，一次性地改变样式。
3. Document Fragment ： 尽量使用离线DOM，而不是真实的网面DOM，来改变元素样式。EG:操作Document Fragment对象;使用 cloneNode() 方法，在克隆的节点上进行操作，然后再用克隆的节点替换原始节点。
4. 先将元素设为display: none;
5. 使用 window.requestAnimationFrame()、window.requestIdleCallback() 这两个方法调节重新渲染。

[网页性能优化](http://www.ruanyifeng.com/blog/2015/09/web-page-performance-in-depth.html)

###　window.requestAnimationFrame() 方法

 方法告诉浏览器您希望执行动画，并请求浏览器调用指定的函数在下一次重绘之前更新动画。该方法将在重绘之前调用的回调作为参数。

 如果你想做逐帧动画的时候，你应该用这个方法。这就要求你的动画函数执行会先于浏览器重绘动作。通常来说，被调用的频率是每秒60次，但是一般会遵循W3C标准规定的频率。如果是后台标签页面，重绘频率则会大大降低。

#### 用法
```
requestID = window.requestAnimationFrame(callback);               // Firefox 23 / IE10 / Chrome / Safari 7 (incl. iOS)
requestID = window.mozRequestAnimationFrame(callback);                // Firefox < 23
requestID = window.webkitRequestAnimationFrame(callback); // Older versions Chrome/Webkit
```
#### 参数Edit

- callback
在每次需要重新绘制动画时,会调用这个参数所指定的函数。这个回调函数会收到一个参数，这个 DOMHighResTimeStamp 类型的参数指示当前时间距离开始触发 requestAnimationFrame 的回调的时间。

### window.requestIdleCallback()

window.requestIdleCallback() 会在浏览器空闲时期依次调用函数， 这就可以让开发者在主事件循环中执行后台或低优先级的任务，而且不会对像动画和用户交互这样延迟触发而且关键的事件产生影响。函数一般会按先进先调用的顺序执行，除非函数在浏览器调用它之前就到了它的超时时间。

> `var handle = window.requestIdleCallback(callback[, options])`
返回值
一个无符号长整数，可以用它来结束回调，使用 Window.cancelIdleCallback() 方法.

参数
`callback`
一个即将被调用的函数的引用。函数会接收到一个名为 deadline 的参数，它具有如下属性 :

- timeRemaining： 一个返回 `DOMHighResTimeStamp` 的函数的引用。(DOMHighResTimeStamp 是一个double类型，用于存储时间值。该值可以是离散的时间点或两个离散时间点之间的时间差。T单位为毫秒 ms (milliseconds) ，应准确至5微秒 µs )

- didTimeout： 布尔型，如果 callback 在空闲时间被客户端执行，它的值为 false，其他情况它的值为 true (例如：options 中给了超时时间，二期在超时过期时仍没有足够的空闲时间)。

- options 可选
包括可选的配置参数。具有如下属性：
timeout：浏览器调用 callback 的最后期限。它的单位是毫秒。

### AMD CMD

AMD 是 RequireJS 在推广过程中对模块定义的规范化产出。CMD 是 SeaJS 在推广过程中对模块定义的规范化产出。类似的还有 CommonJS Modules/2.0 规范，是 BravoJS 在推广过程中对模块定义的规范化产出。还有不少⋯⋯

这些规范的目的都是为了 JavaScript 的模块化开发，特别是在浏览器端的。目前这些规范的实现都能达成浏览器端模块化开发的目的。

区别：
1. 对于依赖的模块，AMD 是提前执行，CMD 是延迟执行。不过 RequireJS 从 2.0 开始，也改成可以延迟执行（根据写法不同，处理方式不同）。CMD 推崇 as lazy as possible.
2. CMD 推崇依赖就近，AMD 推崇依赖前置。看代码：

CMD
```
define(function(require, exports, module) { 
    var a = require('./a')  
    a.doSomething()  
    // 此处略去 100 行  
    var b = require('./b') // 依赖可以就近书写  
     b.doSomething()   
     // ...
     })
```
AMD 默认推荐的是
```
define(['./a', './b'], function(a, b) {  // 依赖必须一开始就写好  
    a.doSomething()   
    // 此处略去 100 行  
    b.doSomething()    
    ...
}) 
```
虽然 AMD 也支持 CMD 的写法，同时还支持将 require 作为依赖项传递，但 RequireJS 的作者默认是最喜欢上面的写法，也是官方文档里默认的模块定义写法。
3. AMD 的 API 默认是一个当多个用，CMD 的 API 严格区分，推崇职责单一。比如 AMD 里，require 分全局 require 和局部 require，都叫 require。CMD 里，没有全局 require，而是根据模块系统的完备性，提供 seajs.use 来实现模块系统的加载启动。CMD 里，每个 API 都简单纯粹。
4. 还有一些细节差异，具体看这个规范的定义就好，就不多说了。另外，SeaJS 和 RequireJS 的差异，可以参考:https://github.com/seajs/seajs/issues/277


### 柯里化：提高函数复用，绑定固定参数
js柯里化是逐步传参，逐步缩小函数的适用范围，逐步求解的过程。
[JavaScript的函数currying 柯里化](http://www.haorooms.com/post/js_currying)

反柯里化是 函数的借用，是函数能够接受处理其他对象，通过借用泛化、扩大了函数的使用范围。

