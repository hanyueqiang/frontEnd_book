## 浏览器篇

#### localstorage sessionstorage cookie 区别 大小限制？

##### 生命周期：

cookie：可设置失效时间，没有设置的话，默认是关闭浏览器后失效

localStorage：除非被手动清除，否则将会永久保存。

sessionStorage： 仅在当前网页会话下有效，关闭页面或浏览器后就会被清除。

##### 存放数据大小：

cookie：4KB 左右

localStorage 和 sessionStorage：可以保存 5MB 的信息。

##### http 请求：

cookie：每次都会携带在 HTTP 头中，如果使用 cookie 保存过多数据会带来性能问题

localStorage 和 sessionStorage：仅在客户端（即浏览器）中保存，不参与和服务器的通信

##### 易用性：

cookie：需要程序员自己封装，源生的 Cookie 接口不友好

localStorage 和 sessionStorage：源生接口可以接受，亦可再次封装来对 Object 和 Array 有更好的支持

##### 应用场景：

从安全性来说，因为每次 http 请求都会携带 cookie 信息，这样无形中浪费了带宽，所以 cookie 应该尽可能少的使用，另外 cookie 还需要指定作用域，不可以跨域调用，限制比较多。但是用来识别用户登录来说，cookie 还是比 storage 更好用的。其他情况下，可以使用 storage

localStorage 和 sessionStorage 唯一的差别一个是永久保存在浏览器里面，一个是关闭网页就清除了信息。

localStorage 可以用来夸页面传递参数，sessionStorage 用来保存一些临时的数据，防止用户刷新页面之后丢失了一些参数。

##### 浏览器支持情况：

localStorage 和 sessionStorage 是 html5 才应用的新特性，可能有些浏览器并不支持，IE8+

cookie 可以通过下面这段代码来判断所使用的浏览器是否支持 cookie

```
if(navigator.cookieEnabled) {
  alert("该浏览器支持cookie"); // 浏览器支持cookie
} else {
  alert("该浏览器不支持cookie"); // 浏览器不支持cookie
}

```

#### 跨域的问题 说了 jsonp domain cors postmessage，iframe 怎么在开发中实现跨域不在后端设置(node)

1.JSONP
全称：JSON with Padding；

作用：利用一些 HTML 标签天生的跨域能力来发送跨域请求的 eg:img、script、link

缺点：JSONP 只能发送 get 请求，不能发送 post 请求，且 JSONP 不是官方的跨域解决方案

2.CORS
全称：Cross-Origin Resource 跨域资源共享

优点：CORS 是官方的跨域解决方案，且使用 CORS 不需要在客户端做任何修改，直接发送 AJAX 请求即可

CORS 支持 get/post 请求；IE8 以下不支持

方法：通过服务器设置一个响应头来告诉浏览器允许跨域请求
res.sendHeader(‘Access-Control-Allow-Origin’,’请求地址’)

允许所有的跨域请求 用 _ 即 res.sendHeader(‘Access-Control-Allow-Origin’, ’_’ )
3.iframe
4.document.domain 来跨子域
例如 a.qq.com 嵌套一个 b.qq.com 的 iframe ，如果 a.qq.com 设置 document.domain 为 qq.com 。b.qq.com 设置 document.domain 为 qq.com，那么他俩就能互相通信了，不受跨域限制了。
5.postMessage()
window.postMessage(message,targetOrigin) 此方法是 html5 新引进的特性，

优点：可以使用它来向其它的 window 对象发送消息，无论这个 window 对象是属于同源或不同源(目前 IE8+、FireFox、Chrome、Opera 等浏览器都已经支持)。
缺点：缺点是 IE6、IE7 不支持，所以用不用还得根据实际需要来决定。

#### 介绍一下对浏览器内核的理解 v8 引擎

#### 说下缓存 浏览器缓存 http 缓存

浏览器缓存是有 header 决定的

1.缓存介绍
常见的 http 缓存只存储 get 响应，
浏览器每次 get url 时会先检查 url 对应的缓存，除非你不使用缓存(强制刷新或者 Disable Cache)

参考：
https://segmentfault.com/a/1190000008377508

#### 浏览器从输入 URL 到渲染完页面的整个过程 越详细越好

1. DNS 解析
   以 chrome 浏览器为例，当输入 baidu.com 的时候，我们实际访问的是 14.215.177.39，这是百度的 IP 地址，从 baidu.com 到 14.215.177.39 的过程就是一个 DNS 解析的过程，首先会从浏览器里 DNS 缓存查找，chrome://dns/，一旦查找到了就完成了这个解析过程，但是如果没有呢？ 那么接着会从电脑本地的 hosts 文件中查找 2.三次握手
   当了解了互联网协议后，我们接着之前的 URL 访问过程，获得了服务器 IP 地址以后，我们需要进行通信，这会进行一次连接，这是通过 TCP 协议完成的
   三次握手:
   第一次由客户端发送 SYN 包到服务器，等待服务器确认；
   第二次是服务器接收到 SYN 数据包，将 SYN + 自己发送的 ACK 包一同发送给客户端；
   第三次是客户端接收到服务器发送过来的 SYN + ACK 数据包后，再向服务器发送确认包 ACK，客户端和服务器进入连接状态，完成三次握手。
   3.HTTP 通信
   当客户端和服务器进入连接状态后，那么就可以进行 HTTP(应用层) 的通信了。 4.页面渲染
   当浏览器接受到响应报文，举例是 html 文件，就开始解析和渲染并呈现给用户也就是我们。 一个完整的 html 文件包括了 html 部分，css 部分，javascript 部分

TCP 四次挥手
TCP 断开链接的过程和建立链接的过程比较类似，只不过中间的两部并不总是会合成一步走，所以它分成了 4 个动作，
客户端挥手发送(fin)——服务端发送(ack)——服务端发送(fin)——客户端发送(ack)。
