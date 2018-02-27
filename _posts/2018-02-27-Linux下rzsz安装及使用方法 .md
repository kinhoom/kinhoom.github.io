---
layout:     post
title:      Linux下rzsz安装及使用方法 
subtitle:   
date:       2018-02-27
author:     Alex Kinhoom
header-img: img/home-bg-art.jpg
catalog: true
tags:
    - Linux
---
## 工具说明
在`SecureCRT`这样的`ssh`登录软件里, 通过在`Linux`界面里输入`rz/sz`命令来上传/下载文件. 对于`RHEL5`, `rz/sz`默认没有安装所以需要手工安装.<br>
`sz`: 将选定的文件发送`(send)`到本地机器;<br>
`rz`：运行该命令会弹出 一个文件选择窗口, 从本地选择文件上传到服务器`(receive)`.<br>

## 具体方法
```shell
# cd /tmp
# wget http://www.ohse.de/uwe/releases/lrzsz-0.12.20.tar.gz
# tar zxvf lrzsz-0.12.20.tar.gz && cd lrzsz-0.12.20
# ./configure && make && make install
# 上面安装过程默认把lsz和lrz安装到了/usr/local/bin/目录下, 下面创建软链接, 并命名为rz/sz:
# cd /usr/bin
# ln -s /usr/local/bin/lrz rz
# ln -s /usr/local/bin/lsz sz
```

## 使用说明
打开`SecureCRT`软件` -> Options -> session options -> X/Y/Zmodem `下可以设置上传和下载的目录; 然后在用`SecureCRT`登陆`linux`终端的时候:<br>
# sz filename (发送文件到客户端,zmodem接收可以自行启动)<br>
# rz (从客户端上传文件到linux服务端)<br>