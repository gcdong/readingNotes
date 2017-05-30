#let和const命令

##1.let命令

###基本用法

ES6新增了let命令,用来声明变量.它的用法类似于var,但是所声明的变量,只在let命令所在的代码块内有效

所以下面的代码就undefined

```
for (let i = 0; i < 10; i++) {}

console.log(i);
//ReferenceError: i is not defined
```

下面的代码如果使用var,最后输出的是10

```
var a = [];
for (var i = 0; i < 10; i++) {
    a[i] = function() {
        console.log(i);
    }
}

a[6](); //10
```

如果改为了let,结果就是6

```
var a = [];
for (let i = 0; i < 10; i++) {
    a[i] = function() {
        console.log(i);
    }
}

a[6](); //6
```

上面代码中,变量i是let声明的,当前的i只在本轮循环有效,所以每一次循环的i其实都是一个新的变量,所以最后输入的是6.

另外,for循环还有一个特别之处,就是循环语句部分是一个父作用域,而循环体内部是一个单独的子作用域

```
for (let i = 0; i < 3; i++) {
    let i = 'abc';
    console.log(i);
}

<!--abc被输入了三次-->
```

###不存在变量提升

var命令会发生"变量提升"现象,即变量可以在声明之前使用,值为undefined.这种现象多多少少有些奇怪,按照一般的逻辑,变量应该在声明语句之后才可以使用.

但是let就不行,必须先声明后使用,否则报错

###暂时性死区

只要块级作用域内存在let命令,它所声明的变量就"绑定"(binding)这个区域,不再受外部的影响

```
var tmp = 123;

if (true) {
	tmp = 'abc'; //这里报错
	let tmp;
}
```

上面代码中，存在全局变量tmp，但是块级作用域内let又声明了一个局部变量tmp，导致后者绑定这个块级作用域，所以在let声明变量前，对tmp赋值会报错。

ES6明确规定，如果区块中存在let和const命令，这个区块对这些命令声明的变量，从一开始就形成了封闭作用域。凡是在声明之前就使用这些变量，就会报错.

总之，在代码块内，使用let命令声明变量之前，该变量都是不可用的。这在语法上，称为“暂时性死区”（temporal dead zone，简称 TDZ）。

```
if (true) {
	// TDZ 开始

	tmp = 'abc';
	console.log(tmp);

	let tmp; //这里TDZ结束了
	console.log(tmp);

	tmp = 123;
	console.log(tmp);
}

```

上面代码中,在let命令声明变量之前,都属于变量tmp的"死区"

所以,有时候,typeof也不安全了

```
typeof x; // ReferenceError
let x;
```

要是没有这个let x,这里的typeof是不会报错的,只会返回一个"undefined"

而且有些"死区"还比较隐藏,不太容易发现

```

<!--因为y未定义就使用了,所以这里是报错的-->
function bar(x = y, y = 2) {
    return [x, y];
}

bar();
```

```

<!--不报错-->
var x = x;


<!--报错-->
let x = x;
```

###不允许重复声明

```

// 报错
function () {
  let a = 10;
  var a = 1;
}

// 报错
function () {
  let a = 10;
  let a = 1;
}

function func(arg) {
  let arg; // 报错
}

function func(arg) {
  {
    let arg; // 不报错
  }
}
```

##2.块级作用域

###为什么需要块级作用域?

ES5只允许全局作用域和函数作用域,没有块级作用域,这带来很多不合理的场景

第一种场景，内层变量可能会覆盖外层变量。

```
var tmp = new Date();

function f() {
    console.log(tmp);
    if (false) {
    	var tmp = "hello word";
    }
}

f();
```

上面代码的原意是，if代码块的外部使用外层的tmp变量，内部使用内层的tmp变量。但是，函数f执行后，输出结果为undefined，原因在于变量提升，导致内层的tmp变量覆盖了外层的tmp变量。

第二种场景，用来计数的循环变量泄露为全局变量。

```
var s = 'hello';
for (var i = 0; i < s.length; i++) {
	console.log(s[i]);
}

console.log(i); //5

```



###ES6的块级作用域

let实际上为JavaScript新增了块级作用域

```
function f1() {
    let n = 5;
    if (true) {
        let n = 10;
    }
    console.log(n);  //5
}
```

上面的函数有两个代码块，都声明了变量n，运行后输出5。这表示外层代码块不受内层代码块的影响。如果使用var定义变量n，最后输出的值就是10

ES6 允许块级作用域的任意嵌套。

```
{{{{{let insane = 'Hello World'}}}}};
```

上面代码使用了一个五层的块级作用域。外层作用域无法读取内层作用域的变量。


```
{{{{
  {let insane = 'Hello World'}
  console.log(insane); // 报错
}}}};
```

但是如果换成var就可以了

```
{{{{
  {var insane = 'Hello World'}
  console.log(insane); // 报错
}}}};
```
内层作用域可以定义外层作用域的同名变量

```
{{{{
  let insane = 'Hello World';
  {let insane = 'Hello World'}
}}}};
```

块级作用域的出现，实际上使得获得广泛应用的立即执行函数表达式（IIFE）不再必要了。

```
// IIFE 写法
(function () {
  var tmp = ...;
  ...
}());

// 块级作用域写法
{
  let tmp = ...;
  ...
}
```

###块级作用域与函数声明

函数能不能再块级作用域之中声明?这是一个相当令人混淆的问题.

ES5规定,函数只能在顶层作用域和函数作用域之中声明,不能在块级作用域声明.

```
// 情况一
if (true) {
  function f() {}
}

// 情况二
try {
  function f() {}
} catch(e) {
  // ...
}
```

上面两种函数声明,根据ES5的规定都是非法的

但是，浏览器没有遵守这个规定，为了兼容以前的旧代码，还是支持在块级作用域之中声明函数，因此上面两种情况实际都能运行，不会报错。

ES6引入了块级作用域,明确允许在块级作用域之中声明函数,ES6规定,块级作用域之中,函数声明的行为类似于let,在会计作用域之外不可引用

```
function f() { console.log('I am outside!'); }

(function () {
  if (false) {
    // 重复声明一次函数f
    function f() { console.log('I am inside!'); }
  }

  f();
}());
```
上面代码在 ES5 中运行，会得到“I am inside!”，因为在if内声明的函数f会被提升到函数头部，实际运行的代码如下。

```
// ES5 环境
function f() { console.log('I am outside!'); }

(function () {
  function f() { console.log('I am inside!'); }
  if (false) {
  }
  f();
}());
```

ES6 就完全不一样了，理论上会得到“I am outside!”。因为块级作用域内声明的函数类似于let，对作用域之外没有影响。但是，如果你真的在 ES6 浏览器中运行一下上面的代码，是会报错的，这是为什么呢？

原来，如果改变了块级作用域内声明的函数的处理规则，显然会对老代码产生很大影响。为了减轻因此产生的不兼容问题，ES6在附录B里面规定，浏览器的实现可以不遵守上面的规定，有自己的行为方式。

* 允许在块级作用域内声明函数。
* 函数声明类似于var，即会提升到全局作用域或函数作用域的头部。
* 同时，函数声明还会提升到所在的块级作用域的头部。

注意，上面三条规则只对 ES6 的浏览器实现有效，其他环境的实现不用遵守，还是将块级作用域的函数声明当作let处理。

根据这三条规则，在浏览器的 ES6 环境中，块级作用域内声明的函数，行为类似于var声明的变

```
// 浏览器的 ES6 环境
function f() { console.log('I am outside!'); }

(function () {
  if (false) {
    // 重复声明一次函数f
    function f() { console.log('I am inside!'); }
  }

  f();
}());
// Uncaught TypeError: f is not a function
```

上面的代码在符合 ES6 的浏览器中，都会报错，因为实际运行的是下面的代码。

```
// 浏览器的 ES6 环境
function f() { console.log('I am outside!'); }
(function () {
  var f = undefined;
  if (false) {
    function f() { console.log('I am inside!'); }
  }

  f();
}());
// Uncaught TypeError: f is not a function
```

考虑到环境导致的行为差异太大，应该避免在块级作用域内声明函数。如果确实需要，也应该写成函数表达式，而不是函数声明语句。


```
// 函数声明语句
{
  let a = 'secret';
  function f() {
    return a;
  }
}

// 函数表达式
{
  let a = 'secret';
  let f = function () {
    return a;
  };
}
```

另外，还有一个需要注意的地方。ES6 的块级作用域允许声明函数的规则，只在使用大括号的情况下成立，如果没有使用大括号，就会报错。

```
// 不报错
'use strict';
if (true) {
  function f() {}
}

// 报错
'use strict';
if (true)
  function f() {}
 
```

###do表达式

本质上，块级作用域是一个语句，将多个操作封装在一起，没有返回值。

```
{
  let t = f();
  t = t * t + 1;
}
```

上面代码中，块级作用域将两个语句封装在一起。但是，在块级作用域以外，没有办法得到t的值，因为块级作用域不返回值，除非t是全局变量。

现在有一个提案，使得块级作用域可以变为表达式，也就是说可以返回值，办法就是在块级作用域之前加上do，使它变为do表达式。

```
let x = do {
        let t = f();
        t * t + 1;
    };
    
```

但是在chrome中失败,可能只是提案?

##3.const 命令

###基本用法

const声明一个只读的常量.一旦声明,常量的值就不能改变

```
const PI = 3.1415;
PI

PI = 3; //Assignment to constant variable.
```

这就意味着,在声明的时候就必须初始化,不能留到以后赋值

```
const foo;
//index.html:13 Uncaught SyntaxError: Missing initializer in const declaration
```
而且和let一样,只在声明所有的块级作用域内有效

```
if (true) {
  const MAX = 5;
}

MAX // Uncaught ReferenceError: MAX is not defined

```

const命令声明的常量也是不提升,同样存在暂时性死区,只能在声明的位置后面使用

```
if (true) {
    console.log(MAX); // ReferenceError
    const MAX = 5;
}
```

上面代码在常量MAX之前就调用,结果报错

const声明的常量,也与let一样不可重复声明

```
var message = "Hello";
let age = 25;

<!--下面这两句都报错-->
const messgae = "Goodbye!";
const age = 30;
```

###本质

const实际上保证的，并不是变量的值不得改动，而是变量指向的那个内存地址不得改动。对于简单类型的数据（数值、字符串、布尔值），值就保存在变量指向的那个内存地址，因此等同于常量。但对于复合类型的数据（主要是对象和数组），变量指向的内存地址，保存的只是一个指针，const只能保证这个指针是固定的，至于它指向的数据结构是不是可变的，就完全不能控制了。因此，将一个对象声明为常量必须非常小心。

```
const foo = {};

// 为foo添加属性
foo.prop = 123;

console.log(foo); //Object {prop: 123}

foo = {}; //Uncaught TypeError: Assignment to constant variable.
```

上面代码中，常量foo储存的是一个地址，这个地址指向一个对象。不可变的只是这个地址，即不能把foo指向另一个地址，但对象本身是可变的，所以依然可以为其添加新属性。

下面是另一个例子。

```
const a = [];
a.push('Hello'); // 可执行
a.length = 0;    // 可执行
a = ['Dave'];    // 报错
```

上面代码中，常量a是一个数组，这个数组本身是可写的，但是如果将另一个数组赋值给a，就会报错。

如果真的想将对象冻结,应该使用Object.freeze方法

```
const foo = Object.freeze({});

// 常规模式时，下面一行不起作用；
// 严格模式时，该行会报错
foo.prop = 123;
```

除了将对象本身冻结，对象的属性也应该冻结。下面是一个将对象彻底冻结的函数。

```
var constantize = (obj) => {
  Object.freeze(obj);
  Object.keys(obj).forEach( (key, i) => {
    if ( typeof obj[key] === 'object' ) {
      constantize( obj[key] );
    }
  });
};
```

###ES6声明变量的六种方法

ES5 只有两种声明变量的方法：var命令和function命令。ES6除了添加let和const命令，后面章节还会提到，另外两种声明变量的方法：import命令和class命令。所以，ES6 一共有6种声明变量的方法。

##4顶层对象的属性

顶层对象,在浏览器环境指的是window对象,在node中指的是global对象,es5之中,顶层对象的属性与全局变量是等价的

上面代码中，顶层对象的属性赋值与全局变量的赋值，是同一件事。

顶层对象的属性与全局变量挂钩，被认为是JavaScript语言最大的设计败笔之一。这样的设计带来了几个很大的问题，首先是没法在编译时就报出变量未声明的错误，只有运行时才能知道（因为全局变量可能是顶层对象的属性创造的，而属性的创造是动态的）；其次，程序员很容易不知不觉地就创建了全局变量（比如打字出错）；最后，顶层对象的属性是到处可以读写的，这非常不利于模块化编程。另一方面，window对象有实体含义，指的是浏览器的窗口对象，顶层对象是一个有实体含义的对象，也是不合适的。


ES6为了改变这一点，一方面规定，为了保持兼容性，var命令和function命令声明的全局变量，依旧是顶层对象的属性；另一方面规定，let命令、const命令、class命令声明的全局变量，不属于顶层对象的属性。也就是说，从ES6开始，全局变量将逐步与顶层对象的属性脱钩。

```
var a = 1;
console.log(window.a); //1

let b =1;
console.log(window.b); //undefined
```

上面代码中，全局变量a由var命令声明，所以它是顶层对象的属性；全局变量b由let命令声明，所以它不是顶层对象的属性，返回undefined。

##5.global对象

ES5的顶层对象,本身也是一个问题,因为它在各种实现里面是不统一的

* 浏览器里面,顶层对象是window,但Node和Web Worker没有window
* 浏览器和Web Worker里面,self也指向顶层对象,但是Node没有self
* Node里面,顶层对象是global,但其他环境都不支持


同一段代码为了能在各种环境,都能娶到顶层对象,现在一般是使用this变量,但是有局限性

* 全局环境中,this会返回顶层对象,但是Node模块和ES6模块中,this返回时当前模块
* 函数里面的this,如果函数不是作为对象的方法运行,而是单纯作为函数允许,this会执行,this会指向顶层对象,但是严格模式下,这时this会返回undefined/
* 不管严格模式还是普通模式,new Function('return this')()，总是会返回全局对象。但是，如果浏览器用了CSP（Content Security Policy，内容安全政策），那么eval、new Function这些方法都可能无法使用。

综上所述，很难找到一种方法，可以在所有情况下，都取到顶层对象。下面是两种勉强可以使用的方法。

```
// 方法一
(typeof window !== 'undefined'
   ? window
   : (typeof process === 'object' &&
      typeof require === 'function' &&
      typeof global === 'object')
     ? global
     : this);

// 方法二
var getGlobal = function () {
  if (typeof self !== 'undefined') { return self; }
  if (typeof window !== 'undefined') { return window; }
  if (typeof global !== 'undefined') { return global; }
  throw new Error('unable to locate global object');
};
```

现在有一个提案，在语言标准的层面，引入global作为顶层对象。也就是说，在所有环境下，global都是存在的，都可以从它拿到顶层对象。

垫片库system.global模拟了这个提案，可以在所有环境拿到global

```
// CommonJS的写法
require('system.global/shim')();

// ES6模块的写法
import shim from 'system.global/shim'; shim();
```

上面代码可以保证各种环境里面，global对象都是存在的。

```
// CommonJS的写法
var global = require('system.global')();

// ES6模块的写法
import getGlobal from 'system.global';
const global = getGlobal();

```

上面代码将顶层对象放入变量global。