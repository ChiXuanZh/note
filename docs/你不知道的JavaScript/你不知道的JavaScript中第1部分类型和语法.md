# 类型

````javascript
typeof undefined === "undefined"; // true
typeof true === "boolean"; // true
typeof 42 === "number"; // true
typeof "42" === "string"; // true
typeof { life: 42 } === "object"; // true
// ES6中新加入的类型
typeof Symbol() === "symbol"; // true
````

````javascript
typeof null === "object"; // true
	// 我们需要使用复合条件来检测 null 值的类型：
var a = null;
(!a && typeof a === "object"); // true
````

JavaScript 中的变量是没有类型的，只有值才有。变量可以随时持有任何类型的值。

````javascript
typeof typeof 42; // "string"
````

## undefined 和 undeclared（未声明）

变量在未持有值的时候为 undefined。此时 typeof 返回 "undefined".

已在作用域中声明但还没有赋值的变量，是 undefifined 的。相反，还没有在作用域中声明过的变量，是 undeclared 的。

# 数值

````javascript
0.1 + 0.2 === 0.3; // false
````

简单来说，二进制浮点数中的 0.1 和 0.2 并不是十分精确，它们相加的结果并非刚好等于0.3，而是一个比较接近的数字 0.30000000000000004，所以条件判断结果为 false。

那么应该怎样来判断 0.1 + 0.2 和 0.3 是否相等呢？

最常见的方法是设置一个误差范围值，通常称为“机器精度”（machine epsilon），对JavaScript 的数字来说，这个值通常是 2^-52 (2.220446049250313e-16)。

````javascript
从 ES6 开始，该值定义在 Number.EPSILON 中，我们可以直接拿来用，也可以为 ES6 之前
的版本写 polyfill：
if (!Number.EPSILON) {
 Number.EPSILON = Math.pow(2,-52);
}
可以使用 Number.EPSILON 来比较两个数字是否相等（在指定的误差范围内）：
function numbersCloseEnoughToEqual(n1,n2) {
 return Math.abs( n1 - n2 ) < Number.EPSILON;
}
var a = 0.1 + 0.2;
var b = 0.3;
numbersCloseEnoughToEqual( a, b ); // true
numbersCloseEnoughToEqual( 0.0000001, 0.0000002 ); // false
能够呈现的最大浮点数大约是 1.798e+308（这是一个相当大的数字），它定义在 Number.
MAX_VALUE 中。最小浮点数定义在 Number.MIN_VALUE 中，大约是 5e-324，它不是负数，但
无限接近于 0
````

## 整数检测

要检测一个值是否是整数，可以使用 ES6 中的 Number.isInteger(..) 方法：

````javascript
Number.isInteger( 42 ); // true
Number.isInteger( 42.000 ); // true
Number.isInteger( 42.3 ); // false
````

也可以为 ES6 之前的版本 polyfifill Number.isInteger(..) 方法：

````javascript
if (!Number.isInteger) {
 	Number.isInteger = function(num) {
 		return typeof num == "number" && num % 1 == 0;
 	};
}
````

要检测一个值是否是安全的整数，可以使用 ES6 中的 Number.isSafeInteger(..) 方法：

````javascript
Number.isSafeInteger( Number.MAX_SAFE_INTEGER ); // true
Number.isSafeInteger( Math.pow( 2, 53 ) ); // false
Number.isSafeInteger( Math.pow( 2, 53 ) - 1 ); // true
````

可以为 ES6 之前的版本 polyfifill Number.isSafeInteger(..) 方法：

````javascript
if (!Number.isSafeInteger) {
 	Number.isSafeInteger = function(num) {
		 return Number.isInteger( num ) &&
			Math.abs( num ) <= Number.MAX_SAFE_INTEGER;
	}; 
}
````

从 Math.pow(-2,31)（-2147483648， 约－21 亿）到 Math.pow(2,31) - 1（2147483647，约 21 亿）。

Number.max   min

## NaN

​	NaN 意指“**不是一个数字**”（not a number），

​	如果数学运算的操作数不是数字类型（或者无法解析为常规的十进制或十六进制数字），就无法返回一个有效的数字，这种情况下返回值为 NaN。

​	NaN 是一个特殊值，它和自身不相等，是唯一一个非自反（自反，reflflexive，即 x === x 不成立）的值。而 NaN != NaN 为 true，

​	从 ES6 开始我们可以使用工具函数 Number.isNaN(..)。判断是否为NaN

## 无穷数 Infinity

````javascript
var a = 1 / 0; // Infinity
var b = -1 / 0; // -Infinity
````

如 果 除 法 运 算 中 的 一 个 操 作 数 为 负 数， 则 结 果 为 -Infinity（ 即 Number.NEGATIVE_INfiNITY）。

JavaScript 使用有限数字表示法（fifinite numeric representation，即之前介绍过的 IEEE 754浮点数），所以和纯粹的数学运算不同，JavaScript 的运算结果有可能溢出，此时结果为Infinity 或者 -Infinity。

````javascript
var a = Number.MAX_VALUE; // 1.7976931348623157e+308
a + a; // Infinity
a + Math.pow( 2, 970 ); // Infinity
a + Math.pow( 2, 969 ); // 1.7976931348623157e+308
````

规范规定，如果数学运算（如加法）的结果超出处理范围，则由 IEEE 754 规范中的“就近取整”（round-to-nearest）模式来决定最后的结果。例如，相对于 Infinity，Number.MAX_VALUE + Math.pow(2, 969) 与 Number.MAX_VALUE 更为接近，因此它被“向下取整”（round down）；而 Number.MAX_VALUE + Math.pow(2, 970) 与 Infinity 更为接近，所以它被“向上取整”（round up）。

## 零值

JavaScript 有一个常规的 0（也叫作+0）和一个 -0。

## 特殊等式

ES6 中新加入了一个工具方法 Object.is(..) 来判断两个值是否绝对相等，可以用来处理

上述所有的特殊情况：

````javascript
var a = 2 / "foo";
var b = -3 * 0;
Object.is( a, NaN ); // true
Object.is( b, -0 ); // true
Object.is( b, 0 ); // false
````

## 值和引用

JavaScript 中没有指针，引用的工作机制也不尽相同。在 JavaScript 中变量不可能成为指向另一个变量的引用。

JavaScript 引用指向的是值。如果一个值有 10 个引用，这些引用指向的都是同一个值，它们相互之间没有引用 / 指向关系。

JavaScript 对值和引用的赋值 / 传递在语法上没有区别，完全根据值的类型来决定。

````javascript
var a = 2;
var b = a; // b是a的值的一个副本
b++;
a; // 2
b; // 3
var c = [1,2,3];
var d = c; // d是[1,2,3]的一个引用
d.push( 4 );
c; // [1,2,3,4]
d; // [1,2,3,4]
````

简单值（即标量基本类型值，scalar primitive）总是通过值复制的方式来赋值 / 传递，包括null、undefined、字符串、数字、布尔和 ES6 中的 symbol。

复合值（compound value）——对象（包括数组和封装对象）和函数，则总 是通过引用复制的方式来赋值 / 传递。

````javascript
function foo(x) {
 x.push( 4 );
 x; // [1,2,3,4]
 // 然后
 x = [4,5,6];
 x.push( 7 );
 x; // [4,5,6,7]
}
var a = [1,2,3];
foo( a );
a; // 是[1,2,3,4]，不是[4,5,6,7]
````

我们向函数传递 a 的时候，实际是将引用 a 的一个复本赋值给 x，而 a 仍然指向 [1,2,3]。在函数中我们可以通过引用 x 来更改数组的值（push(4) 之后变为 [1,2,3,4]）。但 x = [4,5,6] 并不影响 a 的指向，所以 a 仍然指向 [1,2,3,4]。

我们不能通过引用 x 来更改引用 a 的指向，只能更改 a 和 x 共同指向的值。

如果要将 a 的值变为 [4,5,6,7]，必须更改 x 指向的数组，而不是为 x 赋值一个新的数组。

````javascript
function foo(x) {
 x.push( 4 );
 x; // [1,2,3,4]
 // 然后
 x.length = 0; // 清空数组
 x.push( 4, 5, 6, 7 );
 x; // [4,5,6,7]
}
var a = [1,2,3];
foo( a );
a; // 是[4,5,6,7]，不是[1,2,3,4]
````

从上例可以看出，x.length = 0 和 x.push(4,5,6,7) 并没有创建一个新的数组，而是更改了当前的数组。于是 a 指向的值变成了 [4,5,6,7]。

请记住：我们无法自行决定使用值复制还是引用复制，一切由值的类型来决定。

# 原生函数

常用的原生函数有：

• String()

• Number()

• Boolean()

• Array()

• Object()

• Function()

• RegExp()

• Date()

• Error()

• Symbol()——ES6 中新加入的！

实际上，它们就是内建函数。

````javascript
var a = new String( "abc" );
typeof a; // 是"object"，不是"String"
a instanceof String; // true
Object.prototype.toString.call( a ); // "[object String]"
````

## 封装对象包装

````javascript
var a = "abc";
var b = new String( a );
var c = Object( a );
typeof a; // "string"
typeof b; // "object"
typeof c; // "object"
b instanceof String; // true
c instanceof String; // true
Object.prototype.toString.call( b ); // "[object String]"
Object.prototype.toString.call( c ); // "[object String]"
````

## 拆封

如果想要得到封装对象中的基本类型值，可以使用 valueOf() 函数：

````javascript
var a = new String( "abc" );
var b = new Number( 42 );
var c = new Boolean( true );
a.valueOf(); // "abc"
b.valueOf(); // 42
c.valueOf(); // true
````

在需要用到封装对象中的基本类型值的地方会发生隐式拆封。具体过程（即强制类型转换）

````javascript
var a = new String( "abc" );
var b = a + ""; // b的值为"abc"
typeof a; // "object"
typeof b; // "string"
````

## Symbol

​	ES6 中新加入了一个基本数据类型 ——符号（Symbol）。符号是具有唯一性的特殊值（并非绝对），用它来命名对象属性不容易导致重名。该类型的引入主要源于 ES6 的一些特殊构造，此外符号也可以自行定义。

​	符号可以用作属性名，但无论是在代码还是开发控制台中都无法查看和访问它的值，只会显示为诸如 Symbol(Symbol.create) 这样的值。

​	ES6 中有一些预定义符号，以 Symbol 的静态属性形式出现，如 Symbol.create、Symbol.iterator 等，可以这样来使用：

````javascript
obj[Symbol.iterator] = function(){ /*..*/ };
````

​	我们可以使用 Symbol(..) 原生构造函数来自定义符号。但它比较特殊，不能带 new 关键字，否则会出错：

````javascript
var mysym = Symbol( "my own symbol" );
mysym; // Symbol(my own symbol)
mysym.toString(); // "Symbol(my own symbol)"
typeof mysym; // "symbol"
var a = { };
a[mysym] = "foobar";
Object.getOwnPropertySymbols( a );
// [ Symbol(my own symbol) ]
````

​	虽然符号实际上并非私有属性（通过 Object.getOwnPropertySymbols(..) 便可以公开获得对象中的所有符号），但它却主要用于私有或特殊属性。很多开发人员喜欢用它来替代有下划线（_）前缀的属性，而下划线前缀通常用于命名私有或特殊属性。

​	符号并非对象，而是一种简单标量基本类型。

# 强制类型转换

​	工具函数 JSON.stringify(..) 在将 JSON 对象序列化为字符串时也用到了 ToString。

对大多数简单值来说，JSON 字符串化和 toString() 的效果基本相同，只不过序列化的结果总是字符串：

````javascript
JSON.stringify( 42 ); // "42"
JSON.stringify( "42" ); // ""42"" （含有双引号的字符串）
JSON.stringify( null ); // "null"
JSON.stringify( true ); // "true"
````

JSON.stringify(..) 在对象中遇到 undefined、function 和 symbol 时会自动将其忽略，在数组中则会返回 null（以保证单元位置不变）。

````javascript
JSON.stringify( undefined ); // undefined
JSON.stringify( function(){} ); // undefined
JSON.stringify(
 [1,undefined,function(){},4]
); // "[1,null,null,4]"
JSON.stringify(
 { a:2, b:function(){} }
); // "{"a":2}"
````

如果要对含有非法 JSON 值的对象做字符串化，或者对象中的某些值无法被序列化时，就需要定义 toJSON() 方法来返回一个安全的 JSON 值。

````javascript
var o = { };
var a = { 
 b: 42,
 c: o,
 d: function(){}
};
// 在a中创建一个循环引用
o.e = a;
// 循环引用在这里会产生错误
// JSON.stringify( a );
// 自定义的JSON序列化
a.toJSON = function() {
 // 序列化仅包含b
 return { b: this.b };
};
JSON.stringify( a ); // "{"b":42}"
````

​	很多人误以为 toJSON() 返回的是 JSON 字符串化后的值，其实不然，除非我们确实想要对字符串进行字符串化（通常不会！）。toJSON() 返回的应该是一个适当的值，可以是任何类型，然后再由 JSON.stringify(..) 对其进行字符串化。

​	也就是说，toJSON() 应该“返回一个能够被字符串化的安全的 JSON 值”，而不是“返回一个 JSON 字符串”。

````javascript
var a = {
 val: [1,2,3],
 // 可能是我们想要的结果！
 toJSON: function(){
 	return this.val.slice( 1 );
 }
};
var b = {
 val: [1,2,3],
 // 可能不是我们想要的结果！
 toJSON: function(){
 	return "[" +
 	this.val.slice( 1 ).join() +
 	"]"; 
 }
};
JSON.stringify( a ); // "[2,3]"
JSON.stringify( b ); // ""[2,3]""
````

这里第二个函数是对 toJSON 返回的字符串做字符串化，而非数组本身。



请记住，JSON.stringify(..) 并不是强制类型转换。在这里介绍是因为它涉及 ToString 强制类型转换，具体表现在以下两点。

(1) 字符串、数字、布尔值和 null 的 JSON.stringify(..) 规则与 ToString 基本相同。

(2) 如果传递给 JSON.stringify(..) 的对象中定义了 toJSON() 方法，那么该方法会在字符串化前调用，以便将对象转换为安全的 JSON 值。

## ToNumber

ToNumber 对字符串的处理基本遵循数字常量的相关规则 / 语法。处理失败时返回 NaN（处理数字常量失败时会产生语法错误）。不同之处是 ToNumber 对以 0 开头的十六进制数并不按十六进制处理（而是按十进制）。

````js
var a = {
 valueOf: function(){
 return "42";
 }
};
var b = {
 toString: function(){
 return "42";
 }
};
var c = [4,2];
c.toString = function(){
 return this.join( "" ); // "42"
};
Number( a ); // 42
Number( b ); // 42
Number( c ); // 42
Number( "" ); // 0
Number( [] ); // 0
Number( [ "abc" ] ); // NaN
````

## 日期显式转换为数字

一元运算符 + 的另一个常见用途是将日期（Date）对象强制类型转换为数字，返回结果为Unix 时间戳，以微秒为单位（从 1970 年 1 月 1 日 00:00:00 UTC 到当前时间）：

````javascript
var d = new Date( "Mon, 18 Aug 2014 08:53:06 CDT" );
+d; // 1408369986000
````

我们常用下面的方法来获得当前的时间戳，例如：

````javascript
var timestamp = +new Date();
````

````javascript
// 显示方法
var timestamp = new Date().getTime();
// ES5
var timestamp = Date.now();
````

## 解析非字符串

````javascript
parseInt( 1/0, 19 ); // 18
````

很多人想当然地以为（实际上大错特错）“如果第一个参数值为 Infinity，解析结果也应该是 Infinity”，返回 18 也太无厘头了。

````javascript
parseInt( 0.000008 ); // 0 ("0" 来自于 "0.000008")
parseInt( 0.0000008 ); // 8 ("8" 来自于 "8e-7")
parseInt( false, 16 ); // 250 ("fa" 来自于 "false")
parseInt( parseInt, 16 ); // 15 ("f" 来自于 "function..")
parseInt( "0x10" ); // 16
parseInt( "103", 2 ); // 2
````

## 显式转换为布尔值

````javascript
var a = "0";
var b = [];
var c = {};
var d = "";
var e = 0;
var f = null;
var g;
Boolean( a ); // true
Boolean( b ); // true
Boolean( c ); // true
Boolean( d ); // false
Boolean( e ); // false
Boolean( f ); // false
Boolean( g ); // false
````

以显式强制类型转换为布尔值最常用的方法是 !!，因为第二个 ! 会将结果反转回原值：

````javascript
var a = "0";
var b = [];
var c = {};
var d = "";
var e = 0;
var f = null;
var g;
!!a; // true
!!b; // true
!!c; // true
!!d; // false
!!e; // false
!!f; // false
!!g; // false
````

##　字符串和数字之间的隐式强制类型转换

````javascript
var a = [1,2];
var b = [3,4];
a + b; // "1,23,4"
````

​	因为数组的valueOf() 操作无法得到简单基本类型值，于是它转而调用 toString()。因此上例中的两个数组变成了 "1,2" 和 "3,4"。+ 将它们拼接后返回 "1,23,4"。

​	简单来说就是，如果 + 的其中一个操作数是字符串（或者通过以上步骤可以得到字符串），则执行字符串拼接；否则执行数字加法。

````javascript
"0" == null; // false
"0" == undefined; // false
"0" == false; // true -- 晕！
"0" == NaN; // false
"0" == 0; // true
"0" == ""; // false
false == null; // false
false == undefined; // false
false == NaN; // false
false == 0; // true -- 晕！
false == ""; // true -- 晕！
false == []; // true -- 晕！
false == {}; // false
"" == null; // false
"" == undefined; // false
"" == NaN; // false
"" == 0; // true -- 晕！
"" == []; // true -- 晕！
"" == {}; // false
0 == null; // false
0 == undefined; // false
0 == NaN; // false
0 == []; // true -- 晕！
0 == {}; // false
[] == ![] // true
2 == [2]; // true
"" == [null]; // true

42 == "43"; // false
"foo" == 42; // false
"true" == true; // false
42 == "42"; // true
"foo" == [ "foo" ]; // true

"0" == false; // true -- 晕！
false == 0; // true -- 晕！
false == ""; // true -- 晕！
false == []; // true -- 晕！
"" == 0; // true -- 晕！
"" == []; // true -- 晕！
0 == []; // true -- 晕！
````

## 抽象关系比较

比较双方首先调用 ToPrimitive，如果结果出现非字符串，就根据 ToNumber 规则将双方强制类型转换为数字来进行比较。

````javascript
var a = [ 42 ];
var b = [ "43" ];
a < b; // true
b < a; // false
````

如果比较双方都是字符串，则按字母顺序来进行比较：

````javascript
var a = [ "42" ];
var b = [ "043" ];
a < b; // false

var a = [ 4, 2 ];
var b = [ 0, 4, 3 ];
a < b; // false
````



````javascript
var a = { b: 42 };
var b = { b: 43 };
a < b; // false
a == b; // false
a > b; // false
a <= b; // true
a >= b; // true
````

​	因为根据规范 a <= b 被处理为 b < a，然后将结果反转。因为 b < a 的结果是 false，所以 a <= b 的结果是 true。

​	这可能与我们设想的大相径庭，即 <= 应该是“小于或者等于”。实际上 JavaScript 中 <= 是“不大于”的意思（即 !(a > b)，处理为 !(b < a)）。同理 a >= b 处理为 b <= a。

​	相等比较有严格相等，关系比较却没有“严格关系比较”（strict relational comparison）。也就是说如果要避免 a < b 中发生隐式强制类型转换，我们只能确保 a 和 b 为相同的类型，除此之外别无他法。

​	与 == 和 === 的完整性检查一样，我们应该在必要和安全的情况下使用强制类型转换，如：42 < "43"。换句话说就是为了保证安全，应该对关系比较中的值进行显式强制类型转换：

````javascript
var a = [ 42 ];
var b = "043";
a < b; // false -- 字符串比较！
Number( a ) < Number( b ); // true -- 数字比较！
````

# 语法

````javascript
var a = 3 * 6;
var b = a;
b;
````

​	这三行代码都是包含表达式的语句。var a = 3 * 6 和 var b = a 称为“声明语句”（declaration statement），因为它们声明了变量（还可以为其赋值）。a = 3 * 6 和 b = a（不带 var）叫作“赋值表达式”。

​	第三行代码中只有一个表达式 b，同时它也是一个语句（虽然没有太大意义）。这样的情况通常叫作“表达式语句”（expression statement）。

## 表达式的副作用

最常见的有副作用（也可能没有）的表达式是函数调用：

````javascript
function foo() {
 a = a + 1;
}
var a = 1;
foo(); // 结果值：undefined。副作用：a的值被改变
````

## 对象解构

````javascript
function getData() {
 // ..
 return {
 a: 42,
 b: "foo" 
 };
}
var { a, b } = getData();
console.log( a, b ); // 42 "foo"
````

{ a, b } 实际上是 { a: a, b: b } 的简化版本，两者均可，只不过 { a, b }更简洁。

{ .. } 还可以用作函数命名参数（named function argument）的对象解构（object destructuring），方便隐式地用对象属性赋值：

````javascript
function foo({ a, b, c }) {
 // 不再需要这样:
 // var a = obj.a, b = obj.b, c = obj.c
 console.log( a, b, c );
}
foo( {
 c: [1,2,3],
 a: 42,
 b: "foo"
} ); // 42 "foo" [1, 2, 3]
````

JavaScript 没有 else if,以我们经常用到的 else if 实际上是这样的：

````javascript
if (a) { 
 // ..
} 
else {
 if (b) { 
 // ..
 } 
 else {
 // .. 
 }
}
````

## 运算符优先级

````javascript
var a = 42;
var b = "foo";
a && b; // "foo"
a || b; // 42
````

&& 运算符先于 || 执行

## [运算符优先级汇总表](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Operator_Precedence#汇总表)



## [JavaScript 错误参考](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Errors#错误列表)



## try..finally

finally 中的代码总是会在 try 之后执行，如果有 catch 的话则在 catch 之后执行。也可以将 finally 中的代码看作一个回调函数，即无论出现什么情况最后一定会被调用。

````javascript
function foo() {
 try {
 return 42;
 } 
 finally {
 console.log( "Hello" );
 }
 console.log( "never runs" );
}
console.log( foo() );
// Hello
// 42
````

这里 return 42 先执行，并将 foo() 函数的返回值设置为 42。然后 try 执行完毕，接着执行 finally。最后 foo() 函数执行完毕，console.log(..) 显示返回值。

try 中的 throw 也是如此：

````js
 function foo() {
 try {
 throw 42; 
 }
 finally {
 console.log( "Hello" );
 }
 console.log( "never runs" );
}
console.log( foo() );
// Hello
// Uncaught Exception: 42
````

如果 finally 中抛出异常（无论是有意还是无意），函数就会在此处终止。如果此前 try 中已经有 return 设置了返回值，则该值会被丢弃：

````js
function foo() {
 try {
 return 42;
 } 
 finally {
 throw "Oops!";
 }
 console.log( "never runs" );
}
console.log( foo() );
// Uncaught Exception: Oops!
continue 和 break 等控制语句也是如此：
for (var i=0; i<10; i++) {
 try {
 continue; 
 }
 finally {
 console.log( i );
 }
}
// 0 1 2 3 4 5 6 7 8 9
````

finally 中的 return 会覆盖 try 和 catch 中 return 的返回值：

````js
function foo() {
 try {
 return 42;
 } 
 finally {
 // 没有返回语句，所以没有覆盖
 } 
}
function bar() {
 try {
 return 42;
 }
 finally {
 // 覆盖前面的 return 42
 return; 
 }
}
function baz() {
 try {
 return 42;
 } 
 finally {
 // 覆盖前面的 return 42
 return "Hello";
 }
}
foo(); // 42
bar(); // undefined
baz(); // Hello
````

