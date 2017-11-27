---
layout:     post
title:      利用phpSocket进行文件上传
subtitle:   
date:       2017-11-27
author:     Alex Kinhoom
header-img: img/home-bg-geek.jpg
catalog: true
tags:
    - PHP
    - SOCKET
---
## 原理
PHP利用`socket`进行文件上传，欲知其`原理`，首先需要知悉上传时的`请求信息`，请求信息包含`请求头`和`请求体`。通常情况下，PHP通过表单上传文件时，`CHROME`抓取到的<strong>信息</strong>如图
![](http://a1.qpic.cn/psb?/V119AGHh0HMOkI/WLoNduhA6ldbf*UJ.IzDrCc19yvzCgrhOGkI934xphE!/b/dPMAAAAAAAAA&bo=PwWXAQAAAAADB44!&rf=viewer_4)
根据具体的请求信息，便可以进行<strong>请求</strong>`请求头和请求体`的拼装,继而通过`fsock`一系列`socket文件处理`从而达到和表单上传一样的效果。
## 核心代码
```php
class SOCKET_UPLOAD{
    private $host="127.0.0.1";
    private $port=80;
    private $errno=null;
    private $errstr=null;
    public $timeout=30;
    public $url="/upload/handle.php";
    private $ch=null;
    private $header="";
    private $body="";
    private $boundary='----avcss';
    private $res=null;
    private $file=null;
    private $form=null;
    public function __construct($form='',$file=''){
        $this->ch = fsockopen($this->host,$this->port,$this->errno,$this->errstr,$this->timeout);
        if(!$this->ch) exit('connect error!');
        $this->form=$form;
        $this->file=$file;
        $this->setHead();
        $this->setBody();
        var_dump($this->body);
        $this->getStr();
    }
    /*写入请求信息，并获得相应信息*/
    public function write(){
        fwrite($this->ch,$this->res);
        $response = '';
        while($row=fread($this->ch,4096)){
            $response .= $row;
        }
        fclose($this->ch);
        // var_dump($response);
        $pos = strpos($response, "\r\n\r\n");
        $response = substr($response,$pos+4);
        echo $response;
    }
    /*请求信息拼装完成*/
    public function getStr(){
        $this->header .= "Content-Length:".strlen($this->body)."\r\n";
        $this->header .= "Connection: close\r\n\r\n";
        $this->res = $this->header.$this->body;
    }
    /*请求头拼装*/
    private function setHead(){
        $this->header .= "POST {$this->url} HTTP/1.1\r\n";
        $this->header .= "HOST:{$this->host} \r\n";
        $this->header .= "Content-Type:multipart/form-data;boundary={$this->boundary}\r\n";
    }
    private function setBody(){
        $this->form();
        $this->file();
    }
    /*请求体表单字段拼装*/
    private function form(){
        if($this->form && is_array($this->form)){
            foreach($this->form as $k=>$v){
                $this->body .= "--$this->boundary"."\r\n";
                $this->body .= "Content-Disposition:form-data;name=\"{$k}\"\r\n";
                $this->body .= "Content-type:text/plain\r\n\r\n";
                $this->body .= "{$v}\r\n";
            }
        }
    }
    /*请求体文件拼装*/
    private function file(){
        if($this->file && is_array($this->file)){
            foreach($this->file as $k=>$val){
                // var_dump($val);
                $this->body .= "--$this->boundary"."\r\n";
                $this->body .= "Content-Disposition:form-data; name=\"{$val['name']}\";filename=\"{$val['filename']}\"\r\n";
                $this->body .= "Content-Type:{$val['type']}\r\n\r\n";
                $this->body .= file_get_contents($val['path'])."\r\n";
                $this->body .= "--{$this->boundary}";
            }
        }
    }

}
```
- 参考 [php 利用http上传协议(表单提交上传图片 )](https://www.cnblogs.com/loveyouyou616/p/5417818.html)
