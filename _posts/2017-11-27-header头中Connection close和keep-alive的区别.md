---
layout:     post
title:      header头中Connection:close和keep-alive的区别
subtitle:   
date:       2017-11-27
author:     Alex Kinhoom
header-img: img/home-bg-geek.jpg
catalog: true
tags:
    - WEB SERVER
---
## keep-alive
直观理解，就是一个连接一直`存活`,换言之，就是`长连接`，开启该属性，可以使<strong>一次TCP连接</strong>为<strong>同一</strong>用户的<strong>多次</strong>请求服务，`提高了响应速度`。比如网页中的`静态资源`图片、CSS、JS、Html都在一台Server上，当用户访问其中的`Html网页`时，网页中的图片、Css、Js都构成了`访问请求`，打开`KeepAlive `属性可以有效地<strong>降低</striong>TCP握手的次数，减少`httpd`进程数，从而降低内存的使用。
## close
关闭`长连接`，根据请求，<strong>包括静态资源的请求</strong>，来建立`tcp`连接，<strong>单次</strong>请求结束，`连接`关闭。
### 适用范围
1、当你的Server`内存充足`时，`KeepAlive =On`还是`Off`对系统性能影响不大。
2、当你的Server上静态网页(Html、图片、Css、Js)居多时，建议打开`KeepAlive `。
3、当你的Server多为动态请求(因为连接数据库，对文件系统访问较多)，KeepAlive 关掉，会节省一定的内存，节省的内存正好可以作为文件系统的`Cache`(vmstat命令中cache一列)，降低`I/O`压力。
PS：当`KeepAlive =On`时，`KeepAliveTimeOut`的设置其实也是一个问题，设置的过短，会导致`Apache` 频繁建立连接，给`Cpu`造成压力，设置的过长，系统中就会堆积无用的`Http`连接，消耗掉大量内存，具体设置多少，可以进行不断的调节，因你的网站浏览和服务器配置 而异。
###例子
当应用动态程序(比如：`php` )居多，用户访问时由动态程序即时生成`html`内容，`html`内容中图片素材和Css、Js等比较少或者散列在其他Server上时，`KeepAlive =On`反而会降低`Apache` 的性能。为什么呢？
前面提到过，`KeepAlive =On`时，每次用户访问，打开一个`TCP`连接，`Apache` 都会保持该连接一段时间，以便该连接能连续为同一`client`服务，在`KeepAliveTimeOut`还没到期并且`MaxKeepAliveRequests`还没到阈值之前，`Apache` 必然要有一个`httpd`进程来维持该连接，`httpd`进程不是廉价的，他要消耗内存和CPU时间片的。假如当前`Apache` 每秒响应`100`个用户访问，`KeepAliveTimeOut=5`，此时httpd进程数就是`100*5=500`个(prefork 模式)，一个`httpd`进程消耗5M内存的话，就是`500*5M=2500M=2.5G`，夸张吧？当然，Apache 与Client只进行了`100次TCP`连接。如果你的内存够大，系统负载不会太高，如果你的内存小于`2.5G`，就会用到`Swap`，频繁的`Swap`切换会加重CPU的`Load`。
现在我们关掉`KeepAlive` ，`Apache` 仍然每秒响应100个用户访问，因为我们将图片、js、css等分离出去了，每次访问只有1个request，此时httpd的进程数是100*1=100个，使用内存100*5M=500M，此时Apache 与Client也是进行了100次TCP连接。性能却提升了太多。


- 参考 [Connection: close和Connection: keep-alive有什么区别？](http://www.bubuko.com/infodetail-932247.html)