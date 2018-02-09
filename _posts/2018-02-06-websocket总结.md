---
layout:     post
title:      websocket 总结
subtitle:   
date:       2018-02-09
author:     Alex Kinhoom
header-img: img/home-bg-art.jpg
catalog: true
tags:
    - JAVASCRIPT
    - SOCKET
---
## websocket实现步骤
### 申请一个WebSocket对象
参数是需要连接的服务器端的地址，同`http`协议使用`http://`开头一样，`WebSocket`协议的`URL`使用`ws://`开头，另外安全的`WebSocket`协议使用`wss://`开头。
```javascript
var wsUri ="ws://echo.websocket.org/";
websocket = new WebSocket(wsUri);
```
### `WebSocket`对象一共支持四个消息 `onopen`, `onmessage`, `onclose`和`onerror`
我们可以看出所有的操作都是采用消息的方式触发的，这样就不会<strong>阻塞</strong>`UI`，使得`UI`有更快的响应时间，得到更好的用户体验。<br>
1.当`Browser`和`WebSocketServer`连接成功后，会触发`onopen`消息<br>
```javascript
websocket.onopen = function(evt) {};
```
2.如果连接失败，发送、接收数据失败或者处理数据出现错误，`browser`会触发`onerror`消息<br>
```javascript
websocket.onerror = function(evt){};
```
3.当`Browser`接收到`WebSocketServer`发送过来的数据时，就会触发`onmessage`消息，参数`evt`中包含`server`传输过来的数据<br>
```javascript
websocket.onmessage = function(evt) { };
```
4.当`Browser`接收到`WebSocketServer`端发送的关闭连接请求时，就会触发`onclose`消息。
```javascript
websocket.onclose = function(evt) { };
```
### 通信协议
`WebSocket`与`TCP`、`HTTP`的关系`WebSocket`与`http`协议一样都是基于`TCP`的，所以他们都是可靠的协议，`Web`开发者调用的`WebSocket`的`send`函数在`browser`的实现中最终都是通过`TCP`的系统接口进行传输的。<br>
`WebSocket`和`Http`协议一样都属于应用层的协议，那么他们之间有没有什么关系呢?答案是肯定的，`WebSocket`在建立握手连接时，数据是通过`http`协议传输的，但是在建立连接之后，真正的数据传输阶段是不需要`http`协议参与的。
![](http://m.qpic.cn/psb?/V119AGHh0HMOkI/wVQ2Z1q*JDZ33prfV*mh8UkQ3Y*oLLwd5UbTxGdvfWk!/b/dFcBAAAAAAAA&bo=HALnAAAAAAADB9s!&rf=viewer_4)

## `WebSocket`通讯详细解读：
从下图可以明显的看到，分三个阶段：<br>
打开握手<br>

数据传递<br>

关闭握手<br>
![](http://m.qpic.cn/psb?/V119AGHh0HMOkI/DOc4xDl.glprJNUNQlviqs07eaGFutDa1yebYhf.MeQ!/b/dPMAAAAAAAAA&bo=JgIMAgAAAAADBwg!&rf=viewer_4)

下图显示了`WebSocket`主要的三步 浏览器和 服务器端分别做了那些事情。
![](http://m.qpic.cn/psb?/V119AGHh0HMOkI/CdmetQ0*Q*tCcyVYnExg7rgTjXqo9Q5wPbjeos0hexw!/b/dPMAAAAAAAAA&bo=WALBAQAAAAADB7g!&rf=viewer_4)

### `WebSocket`的优点
a)、<strong>服务器</strong>与<strong>客户端</strong>之间交换的标头信息很小，大概只有`2`字节;(标头信息应是客户端与服务器连接成功后互相传递/接收信息的头部)<br>

b)、客户端与服务器都可以主动传送数据给对方;<br>

c)、不用频率创建`TCP`请求及销毁请求，减少网络带宽资源的占用，同时也节省服务器资源;<br>

### 建立连接的握手
当`Web`应用程序调用`new WebSocket(url)`接口时，`Browser`就开始了与地址为`url`的`WebServer`建立握手连接的过程。<br>

1. `Browser`与`WebSocket`服务器通过`TCP`三次握手建立连接，如果这个建立连接失败，那么后面的过程就不会执行，`Web`应用程序将收到错误消息通知。<br>

2. 在`TCP`建立连接成功后，`Browser/UA`通过`http`协议传送`WebSocket`支持的版本号，协议的字版本号，原始地址，主机地址等等一些列字段给服务器端。<br>

3. `WebSocket`服务器收到`Browser/UA`发送来的握手请求后，如果数据包数据和格式正确，客户端和服务器端的协议版本号匹配等等，就接受本次握手连接，并给出相应的数据回复，同样回复的数据包也是采用`http`协议传输。<br>

4. `Browser`收到服务器回复的数据包后，如果数据包内容、格式都没有问题的话，就表示本次连接成功，触发`onopen`消息，此时`Web`开发者就可以在此时通过`send`接口想服务器发送数据。否则，握手连接失败，`Web`应用程序会收到`onerror`消息，并且能知道连接失败的原因。<br>

这个握手很像`HTTP`，但是实际上却不是，它允许服务器以`HTTP`的方式解释一部分`handshake`的请求，然后切换为`websocket`。

### 数据传输
`WebScoket`协议中，数据以帧序列的形式传输。考虑到数据安全性，客户端向服务器传输的数据帧必须进行掩码处理。服务器若接收到未经过掩码处理的数据帧，则必须主动关闭连接。服务器向客户端传输的数据帧一定不能进行掩码处理。客户端若接收到经过掩码处理的数据帧，则必须主动关闭连接。针对上情况，发现错误的一方可向对方发送close帧(状态码是1002，表示协议错误)，以关闭连接。

关闭WebSocket(握手)

![](http://m.qpic.cn/psb?/V119AGHh0HMOkI/jA5qW4fGnzwIyArgNbFPwVlu5TtNaVYZdwN7rND0kOs!/b/dJEAAAAAAAAA&bo=*gHqAAAAAAADBzc!&rf=viewer_4)