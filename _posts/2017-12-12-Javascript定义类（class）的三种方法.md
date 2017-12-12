---
layout:     post
title:      Javascript定义类（class）的三种方法
subtitle:   
date:       2017-12-12
author:     Alex Kinhoom
header-img: img/home-bg-art.jpg
catalog: true
tags:
    - JAVASCRIPT
---
## Javascript定义类（class）的三种方法

# 构造函数法
这是经典方法，也是教科书必教的方法。它用构造函数模拟<strong>类</strong>，在其内部用`this`关键字指代实例对象。
```javascript
function Cat() {
　　　　this.name = "大毛";
}
```
生成实例的时候，使用`new`关键字。
```javascript
var cat1 = new Cat();
alert(cat1.name); // 大毛
```
类的<strong>属性</strong>和<strong>方法</strong>，还可以定义在构造函数的`prototype`对象之上。
```javascript
Cat.prototype.makeSound = function(){
	alert("喵喵喵");
}
```
# Object.create()
为了解决<strong>构造函数法</strong>的缺点，更方便地生成对象，`Javascript`的国际标准`ECMAScript第五版（目前通行的是第三版）`，提出了一个新的方法`Object.create()`。
用这个方法，"类"就是一个对象，不是函数。
```javascript
var Cat = {
　　name: "大毛",
　　makeSound: function(){ alert("喵喵喵"); }
};
```
然后，直接用`Object.create()`生成实例，不需要用到`new`。
```javascript
var cat1 = Object.create(Cat);
alert(cat1.name); // 大毛
cat1.makeSound(); // 喵喵喵
```
目前，各大浏览器的最新版本<strong>（包括`IE9`）</strong>都部署了这个方法。如果遇到老式浏览器，可以用下面的代码自行部署。
```javascript
if (!Object.create) {
　　Object.create = function (o) {
　　	function F() {}
　　　　F.prototype = o;
　　　　return new F();
　　};
}
```
这种方法比<strong>构造函数法</strong>简单，但是不能实现私有属性和私有方法，实例对象之间也不能共享数据，对<strong>类</strong>的模拟不够全面。
# 极简主义法
荷兰程序员Gabor de Mooij提出了一种比`Object.create()`更好的新方法，他称这种方法为<strong>极简主义法</strong>（minimalist approach）。这也是我推荐的方法。
<strong>封装</strong><br>
这种方法不使用`this`和`prototype`，代码部署起来非常简单，这大概也是它被叫做<strong>极简主义法</strong>的原因。
首先，它也是用一个对象模拟<strong>类</strong>。在这个类里面，定义一个构造函数`createNew()`，用来生成实例。
```javascript
var Cat = {
　　createNew: function(){
　　　// some code here
　　}
};
```
然后，在`createNew()`里面，定义一个实例对象，把这个实例对象作为返回值。
```javascript
var Cat = {
　　　createNew: function(){
　　　　　var cat = {};
　　　　　cat.name = "大毛";
　　　　　cat.makeSound = function(){ alert("喵喵喵"); };
　　　　　return cat;
　　　}
};
```
使用的时候，调用`createNew()`方法，就可以得到实例对象。
```javascript
var cat1 = Cat.createNew();
cat1.makeSound(); // 喵喵喵
```
这种方法的好处是，容易理解，结构清晰优雅，符合传统的<strong>面向对象编程</strong>的构造，因此可以方便地部署下面的特性。
<strong>继承</strong><br>
让一个类继承另一个类，实现起来很方便。只要在前者的`createNew()`方法中，调用后者的`createNew()`方法即可。
先定义一个`Animal`类。
```javascript
var Animal = {
　　createNew: function(){
　　　　var animal = {};
　　　　animal.sleep = function(){ alert("睡懒觉"); };
　　　　return animal;
　　}
};
```
然后，在Cat的`createNew()`方法中，调用Animal的`createNew()`方法。
```javascript
var Cat = {
　　　createNew: function(){
　　　　　var cat = Animal.createNew();
　　　　　cat.name = "大毛";
　　　　　cat.makeSound = function(){ alert("喵喵喵"); };
　　　　　return cat;
　　　}
};
```
这样得到的`Cat`实例，就会同时继承`Cat`类和`Animal`类。
```javascript
var cat1 = Cat.createNew();
cat1.sleep(); // 睡懒觉
```
<strong>私有属性和私有方法</strong><br>
在`createNew()`方法中，只要不是定义在`cat`对象上的方法和属性，都是<strong>私有</strong>的。
```javascript
　var Cat = {
　　　　createNew: function(){
　　　　　　var cat = {};
　　　　　　var sound = "喵喵喵";
　　　　　　cat.makeSound = function(){ alert(sound); };
　　　　　　return cat;
　　　　}
　　};
```
上例的内部变量`sound`，外部无法读取，只有通过cat的公有方法`makeSound()`来读取。
```javascript
var cat1 = Cat.createNew();
alert(cat1.sound); // undefined
```
<strong>数据共享</strong><br>
有时候，我们需要所有实例对象，能够读写同一项内部数据。这个时候，只要把这个内部数据，封装在类对象的里面、`createNew()`方法的外面即可。
```javascript
var Cat = {
　sound : "喵喵喵",
　createNew: function(){
　　　var cat = {};
　　　cat.makeSound = function(){ alert(Cat.sound); };
　　　cat.changeSound = function(x){ Cat.sound = x; };
　　　return cat;
　}
};
```
然后，生成两个实例对象：
```javascript
var cat1 = Cat.createNew();
var cat2 = Cat.createNew();
cat1.makeSound(); // 喵喵喵
```
这时，如果有一个实例对象，修改了共享的数据，另一个实例对象也会受到影响。
```javascript
cat2.changeSound("啦啦啦");
cat1.makeSound(); // 啦啦啦
```
- 转载自 [ 阮一峰---Javascript定义类（class）的三种方法](http://www.ruanyifeng.com/blog/2012/07/three_ways_to_define_a_javascript_class.html)