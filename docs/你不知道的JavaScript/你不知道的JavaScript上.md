# 第一部分 作用域和闭包



## 第二章 词法作用域

### eval

 JavaScript 中的 eval(..) 函数可以接受一个字符串为参数，并将其中的内容视为好像在书 

写时就存在于程序中这个位置的代码。换句话说，可以在你写的代码中用程序生成代码并 

运行，就好像代码是写在那个位置的一样。 

根据这个原理来理解 eval(..)，它是如何通过代码欺骗和假装成书写时（也就是词法期） 

代码就在那，来实现修改词法作用域环境的，这个原理就变得清晰易懂了。 

在执行 eval(..) 之后的代码时，引擎并不“知道”或“在意”前面的代码是以动态形式插 

入进来，并对词法作用域的环境进行修改的。引擎只会如往常地进行词法作用域查找。

````javascript
function foo(str, a) { 
  eval( str ); // 欺骗！ console.log( a, b ); 
}
var b = 2; foo( "var b = 3;", 1 ); // 1, 3
````

eval(..) 调用中的 "var b = 3;" 这段代码会被当作本来就在那里一样来处理。由于那段代 

码声明了一个新的变量 b，因此它对已经存在的 foo(..) 的词法作用域进行了修改。事实 

上，和前面提到的原理一样，这段代码实际上在 foo(..) 内部创建了一个变量 b，并遮蔽 

了外部（全局）作用域中的同名变量。

当 console.log(..) 被执行时，会在 foo(..) 的内部同时找到 a 和 b，但是永远也无法找到 

外部的 b。因此会输出“1, 3”而不是正常情况下会输出的“1, 2”。 



在严格模式的程序中，eval(..) 在运行时有其自己的词法作用域，意味着其 

中的声明无法修改所在的作用域。

````javascript
function foo(str) {
  "use strict";
  eval( str ); 
  console.log( a ); // ReferenceError: a is not defined
}
foo( "var a = 2" );
````



### with

with 通常被当作重复引用同一个对象中的多个属性的快捷方式，可以不需要重复引用对象 

本身。

````javascript
var obj = { 
  a: 1, b: 2, c: 3 
};// 单调乏味的重复 "obj" 
obj.a = 2;
obj.b = 3;
obj.c = 4; // 简单的快捷方式
with (obj) { 
  a = 3; b = 4; c = 5; 
}
````

但实际上这不仅仅是为了方便地访问对象属性。考虑如下代码：

````javascript
function foo(obj) {
	with (obj) {
  	a = 2; 
	} 
}
var o1 = {
  a: 3 
};
var o2 = {
  b: 3
};
foo( o1 ); 
console.log( o1.a ); // 2 
foo( o2 ); 
console.log( o2.a ); // undefined 
console.log( a ); // 2——不好，a 被泄漏到全局作用域上了！
````

​	这个例子中创建了 o1 和 o2 两个对象。其中一个具有 a 属性，另外一个没有。foo(..) 函 

数接受一个 obj 参数，该参数是一个对象引用，并对这个对象引用执行了 with(obj) {..}。 

在 with 块内部，我们写的代码看起来只是对变量 a 进行简单的词法引用，实际上就是一个 

LHS 引用（查看第 1 章），并将 2 赋值给它。 

​	当我们将 o1 传递进去，a＝2 赋值操作找到了 o1.a 并将 2 赋值给它，这在后面的 console. 

log(o1.a) 中可以体现。而当 o2 传递进去，o2 并没有 a 属性，因此不会创建这个属性， 

o2.a 保持 undefined。

​	尽管 with 块可以将一个对象处理为词法作用域，但是这个块内部正常的 var 

声明并不会被限制在这个块的作用域中，而是被添加到 with 所处的函数作 

用域中。

​	eval(..) 函数如果接受了含有一个或多个声明的代码，就会修改其所处的词法作用域，而 

with 声明实际上是根据你传递给它的对象凭空创建了一个全新的词法作用域。 

​	可以这样理解，当我们传递 o1 给 with 时，with 所声明的作用域是 o1，而这个作用域中含 

有一个同 o1.a 属性相符的标识符。但当我们将 o2 作为作用域时，其中并没有 a 标识符， 

因此进行了正常的 LHS 标识符查找（查看第 1 章）。 

​	o2 的作用域、foo(..) 的作用域和全局作用域中都没有找到标识符 a，因此当 a＝2 执行 

时，自动创建了一个全局变量（因为是非严格模式）。

​		另外一个不推荐使用 eval(..) 和 with 的原因是会被严格模式所影响（限 

制）。with 被完全禁止，而在保留核心功能的前提下，间接或非安全地使用 

eval(..) 也被禁止了。



## 第三章函数作用域和块作用域



### 函数作用域

````javascript
var a = 2;
function foo() { // <-- 添加这一行
	var a = 3;
  console.log( a ); // 3 
} // <-- 以及这一行 
foo(); // <-- 以及这一行 
console.log( a ); // 2
````



虽然这种技术可以解决一些问题，但是它并不理想，因为会导致一些额外的问题。首先， 

必须声明一个具名函数 foo()，意味着 foo 这个名称本身“污染”了所在作用域（在这个 

例子中是全局作用域）。其次，必须显式地通过函数名（foo()）调用这个函数才能运行其 

中的代码。 

如果函数不需要函数名（或者至少函数名可以不污染所在作用域），并且能够自动运行， 

这将会更加理想。 

幸好，JavaScript 提供了能够同时解决这两个问题的方案、

````javascript
var a = 2; 
(function foo(){ // <-- 添加这一行
	var a = 3; 
  console.log( a ); // 3 
})(); // <-- 以及这一行 
console.log( a ); // 2
````

接下来我们分别介绍这里发生的事情。 

首先，包装函数的声明以 (function... 而不仅是以 function... 开始。尽管看上去这并不 

是一个很显眼的细节，但实际上却是非常重要的区别。函数会被**当作函数表达式**而不是一 

个标准的函数声明来处理。

**区分函数声明和表达式最简单的方法是看 function 关键字出现在声明中的位 置（不仅仅是一行代码，而是整个声明中的位置）。如果 function 是声明中的第一个词，那么就是一个函数声明，否则就是一个函数表达式。**

函数声明和函数表达式之间最重要的区别是它们的名称标识符将会绑定在何处。 

比较一下前面两个代码片段。第一个片段中 foo 被绑定在所在作用域中，可以直接通过 

foo() 来调用它。第二个片段中 foo 被绑定在函数表达式自身的函数中而不是所在作用域中。 

换句话说，(function foo(){ .. }) 作为函数表达式意味着 foo 只能在 .. 所代表的位置中 

被访问，外部作用域则不行。foo 变量名被隐藏在自身中意味着不会非必要地污染外部作 

用域。

### 匿名和具名 

````javascript
setTimeout( function() {
  console.log("I waited 1 second!");
}, 1000 );
````

这叫作**匿名函数表达式**，因为 function().. 没有名称标识符。函数表达式可以是匿名的， 

而函数声明则不可以省略函数名——在 JavaScript 的语法中这是非法的。 

但是它也有几个缺点需要考虑。 

1. 匿名函数在栈追踪中不会显示出有意义的函数名，使得调试很困难。 函数作用域和块作用域 

2. 如果没有函数名，当函数需要引用自身时只能使用已经过期的 arguments.callee 引用， 比如在递归中。另一个函数需要引用自身的例子，是在事件触发后事件监听器需要解绑自身。 

3. 匿名函数省略了对于代码可读性 / 可理解性很重要的函数名。一个描述性的名称可以让代码不言自明。 

行内函数表达式非常强大且有用——匿名和具名之间的区别并不会对这点有任何影响。给函 

数表达式指定一个函数名可以有效解决以上问题。始终给函数表达式命名是一个最佳实践： 

````javascript
setTimeout( function timeoutHandler() { // <-- 快看，我有名字了！ 
  console.log( "I waited 1 second!" ); 
}, 1000 );
````

### 立即执行函数表达式

````javascript
var a = 2; 
(function foo(){ // <-- 添加这一行
	var a = 3; 
  console.log( a ); // 3 
})(); // <-- 以及这一行 
console.log( a ); // 2
````

由于函数被包含在一对 ( ) 括号内部，因此成为了一个表达式，通过在末尾加上另外一个 ( ) 可以立即执行这个函数，比如 (function foo(){ .. })()。第一个 ( ) 将函数变成表达式，第二个 ( ) 执行了这个函数。

这种模式很常见，几年前社区给它规定了一个术语：IIFE，代表立即执行函数表达式 

（Immediately Invoked Function Expression）； 

函数名对 IIFE 当然不是必须的，IIFE 最常见的用法是使用一个匿名函数表达式。虽然使 

用具名函数的 IIFE 并不常见，但它具有上述匿名函数表达式的所有优势，因此也是一个值 

得推广的实践。

````javascript
var a = 2; 
(function IIFE() {
	var a = 3; 
  console.log( a ); // 3
})();
console.log( a ); // 2
````

IIFE 还有一种变化的用途是倒置代码的运行顺序，将需要运行的函数放在第二位，在 IIFE 

执行之后当作参数传递进去。这种模式在 UMD（Universal Module Definition）项目中被广 

泛使用。尽管这种模式略显冗长，但有些人认为它更易理解。

````javascript
var a = 2;
(function IIFE( def ) { 
 	def( window ); 
})(function def( global ) {
	var a = 3; 
  console.log( a ); // 3
  console.log( global.a ); // 2 
});
````

函数表达式 def 定义在片段的第二部分，然后当作参数（这个参数也叫作 def）被传递进 

IIFE 函数定义的第一部分中。最后，参数 def（也就是传递进去的函数）被调用，并将 

window 传入当作 global 参数的值。

### try/catch 

非常少有人会注意到 JavaScript 的 ES3 规范中规定 try/catch 的 catch 分句会创建一个块作 

用域，其中声明的变量仅在 catch 内部有效。

````javascript
try {
	undefined(); // 执行一个非法操作来强制制造一个异常 
}
catch (err) { 
  console.log( err ); // 能够正常执行！ 
}
console.log( err ); // ReferenceError: err not found
````

正如你所看到的，err 仅存在 catch 分句内部，当试图从别处引用它时会抛出错误。 

尽管这个行为已经被标准化，并且被大部分的标准 JavaScript 环境（除了老 

版本的 IE 浏览器）所支持，但是当同一个作用域中的两个或多个 catch 分句 

用同样的标识符名称声明错误变量时，很多静态检查工具还是会发出警告。 

实际上这并不是重复定义，因为所有变量都被安全地限制在块作用域内部， 

但是静态检查工具还是会很烦人地发出警告。

为了避免这个不必要的警告，很多开发者会将 catch 的参数命名为 err1、 err2 等。也有开发者干脆关闭了静态检查工具对重复变量名的检查。 



## 第 4 章 提升

````javascript
a = 2;
var a; 
console.log( a );	// 2
````

````javascript
console.log( a );
var a = 2;	// undefined。
````

先有蛋（声明）后有鸡（赋值）

只有声明本身会被提升，而赋值或其他运行逻辑会留在原地。如果提升改变 了代码执行的顺序，会造成非常严重的破坏。 

````javascript
function foo() {
	var a; 
  console.log( a ); // undefined a = 2; 
}
foo();
````

可以看到，函数声明会被提升，但是函数表达式却不会被提升。 

````javascript
foo(); // 不是 ReferenceError, 而是 TypeError!
var foo = function bar() { // 
  ... };
````

````javascript
foo(); // TypeError 
bar(); // ReferenceError
var foo = function bar() { 
  // ... 
}; 
//这个代码片段经过提升后，实际上会被理解为以下形式：
var foo; 
foo(); // TypeError 
bar(); // ReferenceError 
foo = function() {
	var bar = ...self... 
// ... 
}
````

### 函数优先

````javascript
foo(); // 1
var foo;
function foo() { 
  console.log( 1 ); 
}
foo = function() { 
  console.log( 2 ); 
};
会输出 1 而不是 2 ！这个代码片段会被引擎理解为如下形式：
function foo() {
  console.log( 1 ); 
}foo(); // 1 
foo = function() { 
  console.log( 2 );
};
````

### 闭包

````javascript
for (var i=1; i<=5; i++) { 
  setTimeout( function timer() { 
    console.log( i ); 
  }, i*1000 ); 
}
````

正常情况下，我们对这段代码行为的预期是分别输出数字 1~5，每秒一次，每次一个。 

**但实际上，这段代码在运行时会以每秒一次的频率输出五次 6。**



# 第二部分**this**和对象原型

**this 并不像我们所想的那样指向函数本身。**

````javascript
function foo(num) { 
  console.log( "foo: " + num ); 
  // 记录 foo 被调用的次数
	this.count++; 
}
foo.count = 0;
var i;
for (i=0; i<10; i++) {
	if (i > 5) { 
    foo( i ); 
  } 
}
// foo: 6
// foo: 7
// foo: 8
// foo: 9 
// foo 被调用了多少次？ 
console.log( foo.count ); // 0 -- WTF?
````

console.log 语句产生了 4 条输出，证明 foo(..) 确实被调用了 4 次，但是 foo.count 仍然是 0。显然从字面意思来理解 this 是错误的。 

执行 foo.count = 0 时，的确向函数对象 foo 添加了一个属性 count。但是函数内部代码this.count 中的 this 并不是指向那个函数对象，所以虽然属性名相同，根对象却并不相同，困惑随之产生。

词法作用域是一种非常优秀并且有用的技术。我丝毫没有贬低它的意思（可 

以参考本书第一部分“作用域和闭包”）。但是如果你仅仅是因为无法猜对 

this 的用法，就放弃学习 this 而去使用词法作用域，就不能算是一种很好 

的解决办法了。

````javascript
function foo() {
  foo.count = 4; // foo 指向它自身 
}
setTimeout( function(){ // 匿名（没有名字的）函数无法指向自身 
}, 10 );
````

​	第一个函数被称为具名函数，在它内部可以使用 foo 来引用自身。但是在第二个例子中，传入 setTimeout(..) 的回调函数没有名称标识符（这种函数被称为匿名函数），因此无法从函数内部引用自身。 

**this到底是什么**

​	之前我们说过 this 是在运行时进行绑定的，并不是在编写时绑定，它的上下文取决于函数调用时的各种条件。this 的绑定和函数声明的位置没有任何关系，只取决于函数的调用方式。 当一个函数被调用时，会创建一个活动记录（有时候也称为执行上下文）。这个记录会包含函数在哪里被调用（调用栈）、函数的调用方法、传入的参数等信息。this 就是记录的其中一个属性，会在函数执行的过程中用到。 

## 第 3 章 对象

````javascript
对象可以通过两种形式定义：声明（文字）形式和构造形式。 
对象的文字语法大概是这样：
var myObj = { 
  key: value 
  // ... 
}; 
构造形式大概是这样：
var myObj = new Object(); 
myObj.key = value;
````

构造形式和文字形式生成的对象是一样的。唯一的区别是，在文字声明中你可以添加多个键 / 值对，但是在构造形式中你必须逐个添加属性。

````javascript
var strPrimitive = "I am a string";
typeof strPrimitive; // "string" 
strPrimitive instanceof String; // false
var strObject = new String( "I am a string" );
typeof strObject; // "object" 
strObject instanceof String; // true 
// 检查 sub-type 对象 
Object.prototype.toString.call( strObject ); // [object String]
````

​	不过简单来说，我们可以认为子类型在内部借用了 Object 中的 toString() 方法。从代码中可以看到，strObject 是由 String 构造函数创建的一个对象。 

​	原始值 "I am a string" 并不是一个对象，它只是一个字面量，并且是一个不可变的值。如果要在这个字面量上执行一些操作，比如获取长度、访问其中某个字符等，那需要将其转换为 String 对象。 

​	幸好，在必要时语言会自动把字符串字面量转换成一个 String 对象，也就是说你并不需要显式创建一个对象。JavaScript 社区中的大多数人都认为能使用文字形式时就不要使用构造形式。 

````javascript
var myObject = { 
  a: 2 
};
myObject.a; // 2 
myObject["a"]; // 2
````

​	如果要访问 myObject 中 a 位置上的值，我们需要使用 . 操作符或者 [] 操作符。.a 语法通常被称为“**属性访问**”，["a"] 语法通常被称为“**键访问**”。实际上它们访问的是同一个位置，并且会返回相同的值 2，所以这两个术语是可以互换的。在本书中我们会使用最常见的术语“属性访问”。

​	这两种语法的主要区别在于 . 操作符要求属性名满足标识符的命名规范，而 [".."] 语法可以接受任意 UTF-8/Unicode 字符串作为属性名。举例来说，如果要引用名称为 "Super-Fun!" 的属性，那就必须使用 ["Super-Fun!"] 语法访问，因为 Super-Fun! 并不是一个有效的标识符属性名。

​	在对象中，属性名永远都是字符串。如果你使用 string（字面量）以外的其他值作为属性名，那它首先会被转换为一个字符串。即使是数字也不例外，虽然在数组下标中使用的的确是数字，但是在对象属性名中数字会被转换成字符串，所以当心不要搞混对象和数组中数字的用法：

````javascript
var myObject = { }; 
myObject[true] = "foo";
myObject[3] = "bar"; 
myObject[myObject] = "baz"; 
myObject["true"]; // "foo" 
myObject["3"]; // "bar" 
myObject["[object Object]"]; // "baz"
````

### 可计算属性名

​	如果你需要通过表达式来计算属性名，那么我们刚刚讲到的 myObject[..] 这种属性访问语法就可以派上用场了，如可以使用 myObject[prefix + name]。但是使用文字形式来声明对象时这样做是不行的。

````javascript
ES6 增加了可计算属性名，可以在文字形式中使用 [] 包裹一个表达式来当作属性名：
var prefix = "foo";
var myObject = { 
  [prefix + "bar"]:"hello", 
  [prefix + "baz"]: "world" 
};
myObject["foobar"]; // hello 
myObject["foobaz"]; // world
````

### 属性描述符 

在创建普通属性时属性描述符会使用默认值，我们也可以使用 Object.defineProperty(..)来添加一个新属性或者修改一个已有属性（如果它是 configurable）并对特性进行设置。 

```javascript
var myObject = {}; 
Object.defineProperty( myObject, "a", { 
	value: 2, 
  writable: true, 
  configurable: true, 
  enumerable: true
} ); 
myObject.a; // 2
```

三个特性：writable（可写）、enumerable（可枚举）和 configurable（可配置）。 

1. Writable 	writable 决定是否可以修改属性的值。
2. Configurable  	只要属性是可配置的，就可以使用 defineProperty(..) 方法来修改属性描述符：

````javascript
var myObject = { a:2 };
myObject.a = 3; 
myObject.a; // 3 
Object.defineProperty( myObject, "a", { 
  value: 4, 
  writable: true, 
  configurable: false, // 不可配置！ 
	enumerable: true
} );
myObject.a; // 4 
myObject.a = 5; 
myObject.a; // 5 
Object.defineProperty( myObject, "a", { 
  value: 6, 
  writable: true, 
  configurable: true, 
  enumerable: true
} ); // TypeError
````

最后一个 defineProperty(..) 会产生一个 TypeError 错误，不管是不是处于严格模式，尝试修改一个不可配置的属性描述符都会出错。注意：如你所见，把 configurable 修改成false 是单向操作，无法撤销！ 

**除了无法修改，configurable:false 还会禁止删除这个属性**

3. Enumerable

从名字就可以看出，这个描述符控制的是属性是否会出现在对象的属性枚举中，比如说for..in 循环。如果把 enumerable 设置成 false，这个属性就不会出现在枚举中，虽然仍然可以正常访问它。相对地，设置成 true 就会让它出现在枚举中。

## 第 5 章 原型

### [[Prototype]]

````javascript
var anotherObject = {
  a:2 
};// 创建一个关联到 anotherObject 的对象
var myObject = Object.create( anotherObject ); 
myObject.a; // 2
````

​	现在 myObject 对象的 [[Prototype]] 关联到了 anotherObject。显然 myObject.a 并不存在，但是尽管如此，属性访问仍然成功地（在 anotherObject 中）找到了值 2。但是，如果 anotherObject 中也找不到 a 并且 [[Prototype]] 链不为空的话，就会继续查找下去。 

````javascript
var anotherObject = { 
  a:2
}; 
// 创建一个关联到 anotherObject 的对象 
var myObject = Object.create( anotherObject ); 
for (var k in myObject) { 
  console.log("found: " + k);
}// found: a 
("a" in myObject); // true
````

​	因此，当你通过各种语法进行属性查找时都会查找 [[Prototype]] 链，直到找到属性或者查找完整条原型链

### Object.prototype 

但是到哪里是 [[Prototype]] 的“尽头”呢？ 

所有普通的 [[Prototype]] 链最终都会指向内置的 Object.prototype。由于所有的“普通” （内置，不是特定主机的扩展）对象都“源于”（或者说把 [[Prototype]] 链的顶端设置为）这个 Object.prototype 对象，所以它包含 JavaScript 中许多通用的功能。

## 第 6 章 行为委托
