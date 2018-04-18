---
layout:     post
title:      php中global与$GLOBALS的区别
subtitle:   
date:       2018-04-17
author:     Alex Kinhoom
header-img: img/home-bg-art.jpg
catalog: true
tags:
    - PHP
---
## 官方区别
`$GLOBALS['var']` 是外部的全局变量`$var`本身。<br>
`global $var` 是外部`$var`的<strong>同名引用</strong>或者<strong>指针</strong>。

```php
$var1 = 1;
$var2 = 2;
function test() {
$GLOBALS['var2'] = &$GLOBALS['var1'];
}

test();
echo $var2;//结果为1
```

```php
$var1 = 1;
$var2 = 2;

function test(){
global $var1, $var2;
$var2 = &$var1; //$var2 指向了 $var1的地址
echo $var2;
$var2 = 'snsgou.com';
}

test(); // 输出 1
echo $var2; // 输出 2
echo $var1; // 输出 snsgou.com

```
注释:`test()`函数中的`$var1`，`$var2`都是<strong>局部变量</strong>，只不过是加了`global`关键字后，分别引用指向全局变量`$var1`，`$var2`了，当 `$var2 = &$var1;` 时，局部变量`$var2`不再指向全局变量`$var2`，而重新指向全局变量`$var1`，换句话来说，局部变量`$var2`的改变，不会再影响到全局变量`$val2`，而会<strong>影响</strong>到重新指向的全局变量`$var1`。
```php
$var1 = 1;
function test(){
  unset($GLOBALS['var1']);
}
test();
echo $var1;//因为`$var1`被删除了，所以什么东西都没有打印。
``` 

```php
$var1 = 1;
function test(){
global $var1;
unset($var1);
}

test();
echo $var1;//打印1，证明删除的只是别名，$GLOBALS['var']的引用，起本身的值没有受到任何的改变。
```
注释:也就是说 `global $var` 其实就是`$var = &$GLOBALS['var']`。调用外部变量的一个<strong>别名</strong>而已。
```php
$id = 1;
function test() {
global $id;
unset($id);
}
test();
echo($id); // 输出 1
```

用 `global $var` 声明一个变量时实际上建立了一个到全局变量的<strong>引用</strong>。

```php
$GLOBALS["var1"] = 1;
$var = &$GLOBALS["var1"];
unset($var);
echo $GLOBALS['var1']; //输出1
//############################################
$GLOBALS["var1"] = 1;
$var = &$GLOBALS["var1"];
unset($GLOBALS['var1']);
echo $var; //输出1
//############################################
//如果写成如下，则会出错
$GLOBALS["var"] = 1;
$var = &$GLOBALS["var"];
unset($GLOBALS['var']);
echo $var; //脚本没法执行
//###########################################
```
注释:`unset $var `不会 `unset` 全局变量。`unset`只是把只是断开了<strong>变量名</strong>和<strong>变量内容</strong>之间的<strong>绑定</strong>。这并不意味着变量内容被<strong>销毁</strong>了。使用`isset($var)`的时候返回 `false`。`$this`在一个对象的方法中，`$this`永远是调用它的<strong>对象</strong>的引用。<br>

如果在一个函数内部给一个声明为 `global` 的变量赋于一个引用，该引用只在函数内部可见。可以通过使用 `$GLOBALS` 数组避免这一点。例子如下:
```php
$var1 = "Example variable";
$var2 = "";
function global_references($use_globals) {
  global $var1, $var2;
  if (!$use_globals) {
    $var2 = &$var1; // visible only inside the function
  } else {
    $GLOBALS["var2"] = &$var1; // visible also in global context
  }
}
global_references(false);
echo "var2 is set to '$var2'"; // var2 is set to ''
global_references(true);
echo "var2 is set to '$var2'"; // var2 is set to 'Example variable'
```
注释:把 `global $var`; 当成是 `$var = &$GLOBALS['var']`; 的简写。所以 如果将其它引用赋给 `$var`， 只改变了<strong>本地变量</strong>的引用。



```php
$bar = 3;
function foo(&$var) {
  $GLOBALS["baz"] = 5;
  $var = &$GLOBALS["baz"];
}
foo($bar);
echo $bar;//输出3
```
注释:这将使 `foo` 函数中的 `$var` 变量在函数调用时和 `$bar` 绑定在一起，但接着又被重新绑定到了 `$GLOBALS["baz"]` 上面。不可能通过引用机制将 `$bar` 在函数<strong>调用范围内</strong>绑定到别的变量上面，因为在函数 `foo` 中并没有变量 `$bar`（它被表示为 `$var`，但是 `$var` 只有变量<strong>内容</strong>而没有调用符号表中的<strong>名字到值</strong>的绑定）。可以使用引用返回来引用被函数选择的变量。

最后来看一个实例：
```php
$a = 1;
$b = 2;
function Sum() {
global $a, $b;
$b = $a + $b;
}
Sum();
echo $b; //输出3
```
结论:输出将是`3`。在函数中申明了全局变量 `$a` 和 `$b`，任何变量的所有引用变量都会指向到全局变量。
