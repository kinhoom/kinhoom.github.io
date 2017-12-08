---
layout:     post
title:      php mcrypt加密解密
subtitle:   
date:       2017-12-08
author:     Alex Kinhoom
header-img: img/home-bg-geek.jpg
catalog: true
tags:
    - PHP
    - 加密解密
---
## php mcrypt加密解密实例应用
由于出于安全考虑，参数传递的时候需要进行<strong>加密</strong>和<strong>解密</strong>，一个比较简单的方法是直接使用php中的函数mcrypt_encrypt、mcrypt_decrypt，一个<strong>加密</strong>，一个<strong>解密</strong>，但是问题又出现了，这个加密过程中会产生一些使`url`混乱的符号，于是在加密后对加密字符再进行一次处理，然后多了一一次解析：
## 实现代码
```php
$key = "miyao";//密钥

$string="jiami"//需要加密的字符

//自带的加密函数
$crypttext = base64_encode(mcrypt_encrypt(MCRYPT_RIJNDAEL_256, md5($key), $string, MCRYPT_MODE_CBC, md5(md5($key))));
$encrypted =trim($this->safe_b64encode($crypttext));//对特殊字符进行处理

$key="miyao"
$crypttexttb=safe_b64decode($encrypted)//对特殊字符解析
$decryptedtb = rtrim(mcrypt_decrypt(MCRYPT_RIJNDAEL_256, md5($key), base64_decode($crypttexttb), MCRYPT_MODE_CBC, md5(md5($key))), "\0")//解密函数

//处理特殊字符

 public  function safe_b64encode($string) {
        $data = base64_encode($string);
        $data = str_replace(array('+','/','='),array('-','_',''),$data);
        return $data;
  }

//解析特殊字符

public function safe_b64decode($string) {
    $data = str_replace(array('-','_'),array('+','/'),$string);
    $mod4 = strlen($data) % 4;
    if ($mod4) {
        $data .= substr('====', $mod4);
    }
    return base64_decode($data);
}
```
- 转载自 [mcrypt字符串加密解密](http://blog.csdn.net/lwx2615/article/details/6818658)