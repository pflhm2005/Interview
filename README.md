# 面试题答案



### 摘抄某草榴大神的github面试题目，将答案整理如下。

来源：https://github.com/jawil/blog/issues/22

````

## 一



#### 1.说一下你了解CSS盒模型。

​	content-box/border-box两种盒模型，影响width的计算方式

#### 2.说一下box-sizing的应用场景。

​	样式格式化

#### 3.说一下你了解的弹性FLEX布局.

​	http://blog.csdn.net/magneto7/article/details/70854472

#### 4.说一下一个未知宽高元素怎么上下左右垂直居中。

​	1、table-cell

​	2、transform

​	3、flex

#### 5.说一下原型链，对象，构造函数之间的一些联系。

​	原型链主要是JS属性值获取的特性，如果在本对象查到不到，会在原型上继续查找，直到遍历完整条原型。

​	构造函数的本质也是普通函数，特点在于new操作符，这里简述new操作符的工作：

​	1、创建一个全新的对象。

　　2、这个新对象会被执行【原型】连接。

　　3、这个新对象会绑定到函数调用的this。

　　4、如果函数没有返回其他对象，那么new表达式中的函数调用会自动返回这个新对象。

​	推荐阅读《你不知道的Javascript》系列书籍。

#### 6.DOM事件的绑定的几种方式

​	addEventListener?

#### 7.说一下你项目中用到的技术栈，以及觉得得意和出色的点，以及让你头疼的点，怎么解决的。

​	略

#### 8.有没有了解http2.0,websocket,https，说一下你的理解以及你所了解的特性。

​	http2.0：https://www.zhihu.com/question/34074946

​	websocket：https://www.zhihu.com/question/20215561

​	https在http的外面包了一个SSL层，主要目的是数据传输安全 ，见：

​	http://www.cnblogs.com/wqhwe/p/5407468.html



## 二



#### 1.webpack的入口文件怎么配置，多个入口怎么分割啥的，我也没太听清楚。

​	webpack相关知识：http://www.jqhtml.com/7626.html

#### 2.我看到你的项目用到了Babel的一个插件：transform-runtime以及stage-2，你说一下他们的作用。

​	transform-runtime：https://segmentfault.com/q/1010000005596587?from=singlemessage&isappinstalled=1

​	stage-2：http://www.cnblogs.com/chris-oil/p/5717544.html

#### 3.我看到你的webpack配置用到webpack.optimize.UglifyJsPlugin这个插件，有没有觉得压缩速度很慢，有什么办法提升速度。

​	指令webpack-p默认调用该压缩插件，开发模式下一般不进行压缩，另外可以利用vendor来分离公共框架JS。

#### 4.简历上看见你了解http协议。说一下200和304的理解和区别

​	强缓存与协商缓存：http://www.cnblogs.com/wonyun/p/5524617.html

#### 5.DOM事件中target和currentTarget的区别

	>target指向事件触发源

> currentTatget指向事件绑定DOM

​	vue中事件修饰符self的内部实现即通过判断event.target === event.currentTarget来实现。

#### 6.说一下你平时怎么解决跨域的。以及后续JSONP的原理和实现以及cors怎么设置。

​	1、后台解决

​	2、用node写个反向代理

​	JSONP原理是利用浏览器默认允许的跨服方式script标签进行请求。

#### 7.说一下深拷贝的实现原理。

​	1、使用JSON.stringify与JSON.parse实现

​	优点：简单方便

​	缺点：需要严格符合JSON格式，并且无法拷贝循环引用

​	循环引用有一个插件：https://github.com/douglascrockford/JSON-js/blob/master/cycle.js

​	内部实现原理是用ES6的WeakMap保存对象，键为对象，值为对象的路径，如果遇到重复的，使用特殊符号$+路径替换掉。

​	2、尾递归

​	代码可见jQuery源码中的extend函数，第一个参数为true即为深拷贝。

```javascript
jQuery.extend = function() {
  // 参数修正及变量定义
  for (; i < length; i++) {
    if ((options = arguments[i]) != null) {
      for (name in options) {
        src = target[name];
        copy = options[name];
        if (target === copy) {
          continue;
        }
        if (deep && copy && (jQuery.isPlainObject(copy) ||
                             (copyIsArray = Array.isArray(copy)))) {
          if (copyIsArray) {
            copyIsArray = false;
            clone = src && Array.isArray(src) ? src : [];
          } else {
            clone = src && jQuery.isPlainObject(src) ? src : {};
          }
          target[name] = jQuery.extend(deep, clone, copy);
        } else if (copy !== undefined) {
          target[name] = copy;
        }
      }
    }
  }
  return target;
};
```

#### 8.说一下项目中觉得可以改进的地方以及做的很优秀的地方？

​	略



## 三



#### 1.有没有自己写过webpack的loader,他的原理以及啥的，记得也不太清楚。

​	不懂

#### 2.有没有去研究webpack的一些原理和机制，怎么实现的。

​	https://github.com/youngwind/blog/issues/99

​	学习中

#### 3.babel把ES6转成ES5或者ES3之类的原理是什么，有没有去研究

​	https://zhuanlan.zhihu.com/p/27289600

​	学习中

#### 4.git大型项目的团队合作，以及持续集成啥的。

​	不懂

#### 5.什么是函数柯里化？以及说一下JS的API有哪些应用到了函数柯里化的实现？

	> 柯里化其实本身是固定一个可以预期的参数，并返回一个特定的函数，处理批特定的需求

​	可参考vue中bind函数的实现：

```javascript
function bind(fn, ctx) {
  function boundFn(a) {
    var l = arguments.length;
    return l ?
      l > 1 ?
      fn.apply(ctx, arguments) :
    fn.call(ctx, a) :
    fn.call(ctx)
  }
  // record original fn length
  boundFn._length = fn.length;
  return boundFn
}
```

#### 6.ES6的箭头函数this问题，以及拓展运算符。

	>函数体内的`this`对象，就是定义时所在的对象，而不是使用时所在的对象。

​	拓展运算符可见阮一峰的书：http://es6.ruanyifeng.com/

#### 7.JS模块化Commonjs,UMD,CMD规范的了解，以及ES6的模块化跟其他几种的区别，以及出现的意义。

​	http://blog.csdn.net/Real_Bird/article/details/54869066

#### 8.说一下Vue实现双向数据绑定的原理，以及vue.js和react.js异同点，如果让你选框架，你怎么怎么权衡这两个框架，分析一下。

​	重新定义对象的setter/getter，并劫持所有破坏性数组方法(push、pop等)。

​	react源码没看过，虚拟DOM方面是相同的

#### 9.我看你也写博客，说一下草稿的交互细节以及实现原理。

​	略







​	
