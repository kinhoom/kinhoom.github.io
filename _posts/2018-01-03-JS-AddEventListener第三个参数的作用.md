---
layout:     post
title:      JS-AddEventListener第三个参数的作用
subtitle:   
date:       2018-01-03
author:     Alex Kinhoom
header-img: img/home-bg-art.jpg
catalog: true
tags:
    - JAVASCRIPT
---
# JS-AddEventListener第三个参数的作用

## 概述:
　第3个参数叫做`useCapture`，是一个`boolean`值，就是`true or false` 。如果送出`true`的话就是浏览器会使用`Capture`方式，`false`的话是`Bubbling`，只有在特定的情况下会有影响，通常建议是`false`，而会有影响的情形是目标元素`target element`有祖先元素`ancestor element`，而且也有同样的事件对应函数。
```html
<div id="div1" style="background: blue;width: 100px; height: 100px;">
    <div id="div2" style="background: red;width: 70px; height: 70px;">
        <div id="div3" style="background: yellow;width: 50px; height: 50px;"></div>
    </div>
</div>
```
```javascript
var oDvi1 = document.getElementById('div1'),
    oDvi2 = document.getElementById('div2'),
    oDvi3 = document.getElementById('div3');


// 123 -> 456 -> 345
oDvi1.addEventListener('click', xx1, true);
oDvi2.addEventListener('click', xx2, false);
oDvi3.addEventListener('click', xx3, true);


function xx1(){ //蓝
    alert(123);
}

function xx2(){ //红
    alert(345);
}

function xx3(){//黄
    alert(456);
}
```
## 总结:
在`div3`上触发点击事件<br>
<strong>捕获阶段</strong>: 外`->`里  在`div1`处检测 `useCapture` 是否为`true`,是则执行程序， `div2`同理 。<br>
<strong>目标阶段</strong>: 目标到`div3`处，发现`div3`就是鼠标点击的节点， 所以这里是目标阶段。若有事件处理程序，则执行该程序，这里不论 `useCapture` 为 `true` 还是 `false`。<br>
<strong>冒泡阶段</strong>: 里`->`外  在`div2`处检测`useCapture`是否为`false`， 是则执行该程序 。`div1`同理。<br>
- 转载自 [ js-addEventListener()第三个参数useCapture ](https://www.cnblogs.com/loveyouyou616/p/3916345.html)