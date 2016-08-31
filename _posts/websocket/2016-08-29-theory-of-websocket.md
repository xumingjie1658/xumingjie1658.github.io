---
layout: post
title: "websocket的原理及业务中的使用"
description: ""
category: [websocket, english]
tags: [websocket,html5]
---
{% include setup %}
有几点需要首先明确：

    1.  websocket是一种协议。是html5的一种新协议；
    2.  与http的区别是，它是一种双向通信协议，服务器和客户端都能主动向对方发送或接受数据；
    3.  websocket需要进行握手连接之后才能进行通信；

那么我们就知道了websocket诞生的意义了：

传统的http的请求方式，是客户端发送请求服务端响应的形式，这种方式在应对信息变化不是很频繁的应用时还能较好应对，但是对于实时要求高、海量并发的应用来说显得捉襟见肘，尤其在当前业界移动互联网蓬勃发展的趋势下，高并发与用户实时响应是 Web 应用经常面临的问题，比如金融证券的实时信息，Web 导航应用中的地理位置获取，社交网络的实时消息推送等。

基于http，我们也有实时通讯的方案，比如轮询，比如使用flash的socket，但是这些都是非标准的，而且代价较大，html5的标准化组织需要针对实时通讯的应用场景提供一套业界统一的规范，那就是websocket。

### socket通信方式

![socket通信方式]({{ BLOG_IMG }}socket.png)

socket的概念最初出现在linux网络编程中，socket是在应用层和传输层之间的一个抽象层，它把TCP/IP层复杂的操作抽象为几个简单的接口供应用层调用已实现进程在网络中通信。

简而言之，socket就是对TCP连接建立和释放的过程进行了一层封装，提供出接口给应用层是使用。

socket是如何与对应网络中的进程进行通信的呢？

我们知道两个进程如果需要进行通讯最基本的一个前提能能够唯一的标示一个进程，在本地进程通讯中我们可以使用PID来唯一标示一个进程，但PID只在本地唯一，网络中的两个进程PID冲突几率很大，这时候我们需要另辟它径了，我们知道IP层的ip地址可以唯一标示主机，而TCP层协议和端口号可以唯一标示主机的一个进程，这样我们可以利用ip地址＋协议＋端口号唯一标示网络中的一个进程。

那么在进行socket接口调用了时候，传递上述唯一标识之后，生成相应的协议请求，双向通信就能通过socket进行了。

web socket的作用也是模仿socket的通信能力，但是不同的是，他本身是一种协议，浏览器和服务端会对这种协议进行解析，他是基于基础TCP连接的，所以本身它是具有建立双向连接的能力的，只不过具体的数据传输方式和一些针对web实时通讯的特性需要在websocket中进行定义。

### websocket简介

传统的http请求方式：

![传统的http请求方式]({{ BLOG_IMG }}httprequest.png)

websocket的通信方式：

![websocket的通信方式]({{ BLOG_IMG }}websocketrequest.png)

可以看到，http请求每次客户端与服务端交互一定得重新建立连接，但是websocket协只需要双方通过握手建立连接，在连接释放之前，客户端和服务端可以相互接收和传递数据，因为双方通过协议是知道建立websocket链接各自进程的端口的。

在客户端中，我们需要使用WebSocket对象，去连接ws://这样的服务端url，这样客户端就会自动解析并识别websocket请求从而和服务端通过握手建立连接。

### WebSocket 客户端连接报文

    GET /webfin/websocket/ HTTP/1.1
    Host: localhost
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: xqBt3ImNzJbYqRINxEFlkg==
    Origin: http://localhost:8080
    Sec-WebSocket-Version: 13

其中，“Sec-WebSocket-Key”是 WebSocket 客户端发送的一个 base64 编码的密文，要求服务端必须返回一个对应加密的“Sec-WebSocket-Accept”应答，否则客户端会抛出“Error during WebSocket handshake”错误，并关闭连接。

### WebSocket 服务端响应报文

    HTTP/1.1 101 Switching Protocols
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Accept: K7DJLdLooIwIG/MOpvWFB3y3FE8=

“Sec-WebSocket-Accept”的值是服务端采用与客户端一致的密钥计算出来后返回客户端的,“HTTP/1.1 101 Switching Protocols”表示服务端接受 WebSocket 协议的客户端连接，经过这样的请求-响应处理后，客户端服务端的 WebSocket 连接握手成功, 后续就可以进行 TCP 通讯了。

具体参考：[websocket协议栈](https://tools.ietf.org/html/rfc6455?cm_mc_uid=54565781380814648371787&cm_mc_sid_50200000=1470358750)

### websocket的实现

要实现websocket，必须客户端和服务端都要支持这个协议。

Tomcat在新版本中提供了WebSocketServlet对象，用于支持websocket。php肯定也有自己的websocket封装。

而在客户端html5提供了标准的websocket API，demo实现如下：

    var ws = new WebSocket(“ws://echo.websocket.org”);
    ws.onopen = function(){ws.send(“Test!”); };
    ws.onmessage = function(evt){console.log(evt.data);ws.close();};
    ws.onclose = function(evt){console.log(“WebSocketClosed!”);};
    ws.onerror = function(evt){console.log(“WebSocketError!”);};
    ws.send("hello");//向服务器发送消息

第一行代码是在申请一个 WebSocket 对象，参数是需要连接的服务器端的地址，同 HTTP 协议开头一样，WebSocket 协议的 URL 使用 ws://开头，另外安全的 WebSocket 协议使用 ws://开头。

第二行到第五行为 WebSocket 对象注册消息的处理函数，WebSocket 对象一共支持四个消息 onopen, onmessage, onclose 和 onerror，有了这 4 个事件，我们就可以很容易很轻松的驾驭 WebSocket。

当 Browser 和 WebSocketServer 连接成功后，会触发 onopen 消息；如果连接失败，发送、接收数据失败或者处理数据出现错误，browser 会触发 onerror 消息；当 Browser 接收到 WebSocketServer 发送过来的数据时，就会触发 onmessage 消息，参数 evt 中包含 Server 传输过来的数据；当 Browser 接收到 WebSocketServer 端发送的关闭连接请求时，就会触发 onclose 消息。我们可以看出所有的操作都是采用异步回调的方式触发，这样不会阻塞 UI，可以获得更快的响应时间，更好的用户体验。

### 业务中websocket的使用

在糯米组件的最新迭代中，支持使用websocket更新shoplist页面中的订单状态。

在comp/utils/csocket.js中，定义了c端websocket的封装。

![]({{ BLOG_IMG }}websocketcode1.png)

调用new WebSocket建立websocket连接。

![]({{ BLOG_IMG }}websocketcode2.png)

websocket不同阶段的回调。

![]({{ BLOG_IMG }}websocketcode3.png)

其中message方法用于给某一类服务端返回的时间注册回调。

![]({{ BLOG_IMG }}websocketcode4.png)

参考文档：

[WebSocket实战](http://www.ibm.com/developerworks/cn/java/j-lo-WebSocket/)

[The WebSocket Protocol](https://tools.ietf.org/html/rfc6455?cm_mc_uid=54565781380814648371787&cm_mc_sid_50200000=1470358750)

[简单理解Socket](http://www.cnblogs.com/dolphinX/p/3460545.html)
