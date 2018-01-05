---
layout:     post
title:      JQuery操作select(方便查阅)
subtitle:   
date:       2018-01-05
author:     Alex Kinhoom
header-img: img/home-bg-art.jpg
catalog: true
tags:
    - JAVASCRIPT
    - JQUERY
---
## 设置`value`为`a`的项选中
```javascript
$(".selector").val("a");
```
## 设置`text`为`pxx`的项选中
```javascript
$(".selector").find("option[text='pxx']").attr("selected",true);
```
这里有一个中括号的用法，中括号里的等号的前面是属性名称，不用加引号。很多时候，中括号的运用可以使得逻辑变得很简单。

## 获取当前选中项的`value`
```javascript
$(".selector").val();
```

## 获取当前选中项的`text`
```javascript
$(".selector").find("option:selected").text();
```
这里用到了冒号，掌握它的用法并举一反三也会让代码变得简洁。<br>
很多时候用到`select`的级联，即第二个`select`的值随着第一个`select`选中的值变化。这在`jquery`中是非常简单的。
```javascript
$(".selector1").change(function(){
	// 先清空第二个
	$(".selector2").empty();
	// 实际的应用中，这里的option一般都是用循环生成多个了
	var option = $("<option>").val(1).text("pxx");
	$(".selector2").append(option);
});
```

- 转载整理自 [ jquery操作select(取值，设置选中） ](http://blog.csdn.net/nairuohe/article/details/6307367)