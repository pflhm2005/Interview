- H5性能优化
1. 大图片压缩 1x/2x/3x => 压缩3x、安卓替换成webp、ios替换成jpg
2. 异步加载非首屏数据，懒加载非首屏图片 => stateCallback
3. package.json移除无用包(线上编译通过eslint检测unused)，使用公共离线包。
4. 离线包 => react、react-dom等公共JS库直接在html中使用CDN链接，数据包接入离线包。
5. 数据预加载，schema加上预请求接口，通过jsb快速获取数据

- 移动端开发
1. 低版本安卓1px偶尔不显示，需要1.05px
2. 低版本chrome内核无法用line-height垂直居中，需要用绝对定位+scale
3. 横向滚动条最右边的dom右边距不生效，需要插一个空dom
4. ipx需要做底部兼容
5. 刘海屏需要对status做兼容处理
6. IOS默认带有弹性滚动，需要做兼容处理
7. 指定行文案css
```css
overflow: hidden;
text-overflow: ellipsis;
-webkit-box-orient: vertical;
display: -webkit-box;
-webkit-line-clamp: 1;
```
8. 图层不显示的问题 => 加上transform: translate3d(0, 0, 0)
9. 页面渲染与代码不同步 => 改变opacity会强制重绘
10. 一般h5自带flexible，自动会将px转为rem，但是需要注意的是行内样式不会转换，另外PX也会被忽略

- 调试
1. SSR的开发，使用本地json文件
2. 跨域用webpack反向代理
devServer: {
    proxy: {
      '/xxx/\*\*': {
        target: 'xxx',
        changeOrigin: true,
      },
    }
}
3. 联调利用抓包工具Charles，主要是看接口请求与相应

- webpack优化
1. babel-plugin-import 按需引入组件
具体原理 将整个库的引用转换为单个模块的引入
import {Buttom} from 'antd' => import _Buttom from 'antd/lib/button';
如果只是单纯的import {xxx} from 'xxx'是不会得到优化的
2. ScriptExtHtmlWebpackPlugin可以强化HtmlWebpackPlugin插件 => 比如给script标签添加属性或设置为async
3. 多entry的webpack，通过cacheGroups的配置将特殊库(比如echarts)单独拆分出来

---

<!-- # 面试记录(目前全挂，哈哈哈哈) -->

### 虾皮一面

- 原型链
- 无bind实现call函数
- 判断对象是否由new调用

### 腾讯微视一面

- H5的优化、node使用
- 笔试题实现驼峰化、sleep函数、节流函数

### 平安二面
- 无CDN如何解决高并发
- 如何做页面白屏的优化，具体的措施
- 头条内部工具有什么不足的地方，指出来
- 有自己做过架构方面的事吗
- 对webpack、vue了解如何，说说原理
- 对PC端兼容有什么看法
- 移动端和H5通信过程，JSB的原理
- 与服务端联调过程，如何上线的
- 安全方面有做过吗
- 有写过公共组件吗，过程，测试用例怎么写的

### 阿里电话面(挂)

- 从url中输出taobao.com到页面出来发生了什么

**计算机网络确实是弱项，力争最详细解释**

主流程如下(内容参考https://github.com/skyline75489/what-happens-when-zh_CN)

1. DNS查询
> DNS全称Domain Name System，主要是将域名转换为数字IP地址以便计算机服务定位
- 进行punyCode转码，兼容带特殊字符的hostname，例如：吉米.com => xn--9pr835g.com，xn--为punyCode的特殊标识
- 检查域名是否在缓存中，如果有直接返回(chrome是chrome://net-internals/#dns)
- 缓存没有从本地的Host文件中找，mac电脑是/etc/hosts，形式类似于127.0.0.1  localhost
- 上面那一步是可选的，具体实现取决于浏览器(在node的dns模块中，若使用resolve方法，则会始终通过网络执行DNS查询，不走上一步。而dns.lookup方法则纯粹走的系统api => getaddrinfo，不会跟DNS协议产生联系)
- 发送DNS查询给DNS服务器。DNS会优先发送基于UDP的请求(端口53)，仅当请求包体积长度大于512bytes(DNS messages carried by UDP were restricted to 512 bytes)且服务端、客户端不支持EDNS的情况下，才会通过TCP发送DNS查询
- DNS服务器是树结构，查询请求到达根服务时，不会直接响应结果，而是会转给子服务。按照提问的```www.taobao.com```，首先会转给com服务，之后会反复重复这个过程直到返回最终结果。
> 注: edns协议的请求体也有上限 => #define EDNSPACKETSZ   1280  /* Reasonable UDP payload size, as suggested in RFC2671 */

> 关于punyCode的实现见https://github.com/bestiejs/punycode.js/blob/master/punycode.js


2. TCP封包

- 获取目标服务器IP地址、端口号后，开始封装TCP请求
- 首先请求交给传输层，封装成TCP分片，头部加入目标端口和源端口
- 接下来交给网络层，加入目标服务器的IP地址以及本机IP地址，封装成IP数据包
- 接下来进入链路层，加入MAC地址
- TCP封包准备好了后，会从本地计算机出发，经过多个路由器，每个路由器会从包中提取目标地址，将封包转移到下一个目的地。每经过一个路由器，包头部的TTL值都会减1，到0时这个包会被丢弃
> 注：这一步可参考node的DNS模块封装TCP请求的过程，具体源码见\deps\cares\src\ares_process.c

3. 建立TCP连接
> TCP是网络协议簇中的一个，最初是为了补充IP协议，因此整个簇一般被统称为TCP/IP。TCP提供可靠、有序、错误检查的连接，位于传输层，SSL/TLS在其之上，与之对比的是UDP。
- TCP的头部字段与连接相关的有Sequence number、ACK(Acknowledgment number)确认号，SYN(Synchronize sequence numbers)同步序列号。
- 服务端首先会开放一个端口并监听，被称为被动打开，之后客户端会通过三次握手来主动打开。
- SYN => 客户端发送SYN包给服务端，将Sequence number设置为一个随机值，将SYN标记置1
- SYN-ACK => 服务端接受到包后，返回一个新包，将ACK的值设置为sequence numbers + 1，服务端的包sequence numbers值会为另外一个随机值
- ACK => 客户端发送一个ACK包给服务端，将sequence numbers设置为ACK的值，ACK的值设置为服务端回包的sequence numbers + 1

妄妄语录：序号并不是从 1 开始的，而是需要用随机数计算出一个初始值，这是因为如果序号都从 1 开始，通信过程就会非常容易预测，有人会利用这一点来发动攻击。但是如果初始值是随机的，那么对方就搞不清楚序号到底是从多少开始计算的，因此需要在开始收发数据之前将初始值告知通信对象。

TIP:断开连接

- 断开需要四次挥手，这个两边都可以发起
- 有一端需要结束连接时，会发送一个FIN包
- 由于服务端会连续发送两个包，分别代表收到断开请求以及所有内容都发送完毕，所以会有四次挥手

4.TLS握手

- 客户端发送一个消息到服务端，包含了可用的TLS版本、加密压缩算法
- 服务端返回一个消息，包含服务端的TLS版本，所选择的加密压缩算法，以及一个CA证书，证书包含了公钥
- 客户端判断该证书是否可信，如果可信，会生成一个伪随机数，该数被用来生成对称秘钥，使用公钥加密后发送给服务端
- 服务端用私钥解密后同样生成一个对称秘钥
- 之后客户端与服务端根据随机数与加密算法生成的秘钥进行通信

5.状态响应

- 通过HTTP/HTTPS等协议进行通信，服务端会返回一个响应状态码
- 200 => 代表请求成功，返回新的内容。通过缓存返回则会有一个标识，即200(from disk cache) or 200(from memory cache)
- 304 => 代表内容无改变，可以使用缓存内容，此时虽然有通信，但服务端并未返回任何内容
- 相关缓存知识自行查询

6.资源解析

- 解析HTML、CSS、JS
- 构建 DOM 树 -> 渲染 -> 布局 -> 绘制
- 解析HTML输入，规范在https://html.spec.whatwg.org/multipage/parsing.html，里面包含了完整的文档与错误兼容。
还没看这块，具体的原理待完善……