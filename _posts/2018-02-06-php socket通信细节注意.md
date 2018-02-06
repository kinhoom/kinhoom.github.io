---
layout:     post
title:      php socket通信细节注意
subtitle:   
date:       2018-02-06
author:     Alex Kinhoom
header-img: img/home-bg-art.jpg
catalog: true
tags:
    - PHP
    - SOCKET
---
## 实现简单的socket通信实例
server.php
```php
<?php
$socket =  socket_create(AF_INET, SOCK_STREAM, SOL_TCP );
socket_set_option($socket,SOL_SOCKET,SO_REUSEADDR,1);
socket_bind($socket ,'127.0.0.1', 11211 );
socket_listen( $socket ,5);
while( true ){
    $con = socket_accept( $socket );
    if( $con !==false ){
        socket_write($con, 'init', 4 );
        while(  $str = socket_read( $con,1024 ) ){
            echo 'client:'.$str."\n";
            $ret = fgets(STDIN);
            socket_write($con,$ret,strlen($ret));
        }
        socket_close( $con );
    }
}

```
client.php
```php
<?php
$socket =  socket_create(AF_INET, SOCK_STREAM, SOL_TCP );
socket_connect( $socket ,'127.0.0.1', 11211 );
while( $t = socket_read( $socket,1024  ) ){
    echo 'server:'.$t."\n";
    $str = trim(fgets(STDIN));
    if( $str ){
        socket_write($socket, $str, strlen($str) );
    }
}
socket_close( $socket );

```

## `socket_listen`里面第二个参数`backlog`的用处
当有多个客户端一起请求的时候，服务端不可能来多少就处理多少，这样如果并发太多，就会因为性能的因素发生拥塞，然后造成雪崩。所有服务器就搞了一个队列，先将请求放在队列里面，一个个来。`socket_listen`里面的第二个参数`backlog`就是设置这个队列的长度。如果将队列长度设置成`10`，那么如果有`20`个请求一起过来，服务端就会先放`10`个请求进入这个队列，因为长度只有`10`。然后其他的就直接拒绝。`tcp`协议这时候不会发送`rst`给客户端，这样的话客户端就会重新发送`SYN`，以便能进入这个队列。
<br>
如果<strong>三次握手</strong>完成了，就会将完成三次握手的<strong>请求</strong>取出来，放入<strong>另一个队列</strong>中，这样队列就空出一个位置，其他重发`SYN`的请求就可以进入队列中。