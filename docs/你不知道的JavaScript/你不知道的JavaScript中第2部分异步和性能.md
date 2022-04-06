# 异步：现在与将来

**程序中现在运行的部分和将来运行的部分之间的关系就是异步编程的核心。**

## 分块的程序

````javascript
// ajax(..)是某个库中提供的某个Ajax函数
var data = ajax( "http://some.url.1" );
console.log( data );
// 啊哦！data通常不会包含Ajax结果
````

​	标准 Ajax 请求不是同步完成的，这意味着 ajax(..) 函数还没有返回任何值可以赋给变量 data。如果 ajax(..) 能够阻塞到响应返回，那么 data = .. 赋值就会正确工作。

​	但我们并不是这么使用 Ajax 的。现在我们发出一个异步 Ajax 请求，然后在将来才能得到返回的结果。

​	从现在到将来的“等待”，最简单的方法（但绝对不是唯一的，甚至也不是最好的！）是使用一个通常称为回调函数的函数：

````javascript
// ajax(..)是某个库中提供的某个Ajax函数
ajax( "http://some.url.1", function myCallbackFunction(data){
 console.log( data ); // 耶！这里得到了一些数据！
} );
````

为了避免回调函数引起的混乱并不足以成为使用阻塞式同步Ajax 的理由。

举例来说，考虑一下下面这段代码：

````js
function now() {
 return 21;
}
function later() {
 answer = answer * 2;
 console.log( "Meaning of life:", answer );
}
var answer = now();
setTimeout( later, 1000 ); // Meaning of life: 42
````

这个程序有两个块：现在执行的部分，以及将来执行的部分。这两块的内容很明显，但这

里我们还是要明确指出来。

现在：

````js
function now() {
 return 21;
}
function later() { .. }
var answer = now();
setTimeout( later, 1000 );
````

将来：

````js
answer = answer * 2;
console.log( "Meaning of life:", answer );
````

任何时候，只要把一段代码包装成一个函数，并指定它在响应某个事件（定时器、鼠标点击、Ajax 响应等）时执行，你就是在代码中创建了一个将来执行的块，也由此在这个程序中引入了异步机制。

## 事件循环

​	JavaScript 引擎并不是独立运行的，它运行在宿主环境中，对多数开发者来说通常就是Web 浏览器。

​	它们都提供了一种机制来处理程序中多个块的执行，且执行每块时调用 JavaScript 引擎，这种机制被称为事件循环。

​	换句话说，JavaScript 引擎本身并没有时间的概念，只是一个按需执行 JavaScript 任意代码片段的环境。“事件”（JavaScript 代码执行）调度总是由包含它的环境进行。

## 并行线程

异步是关于现在和将来的时间间隙，而并行是关于能够同时发生的事情。

​	并行计算最常见的工具就是进程和线程。进程和线程独立运行，并可能同时运行：在不同的处理器，甚至不同的计算机上，但多个线程能够共享单个进程的内存。

​	与之相对的是，事件循环把自身的工作分成一个个任务并顺序执行，不允许对共享内存的并行访问和修改。通过分立线程中彼此合作的事件循环，并行和顺序执行可以共存。

​	并行线程的交替执行和异步事件的交替调度，其粒度是完全不同的。

````js
var a = 20; 
function foo() { 
 a = a + 1; 
} 
function bar() { 
 a = a * 2; 
} 
// ajax(..)是某个库中提供的某个Ajax函数
ajax( "http://some.url.1", foo ); 
ajax( "http://some.url.2", bar ); 
````

根据 JavaScript 的单线程运行特性，如果 foo() 运行在 bar() 之前，a 的结果是 42，而如果bar() 运行在 foo() 之前的话，a 的结果就是 41。

### 完整运行

由于 JavaScript 的单线程特性，foo()（以及 bar()）中的代码具有原子性。也就是说，一旦 foo() 开始运行，它的所有代码都会在 bar() 中的任意代码运行之前完成，或者相反。这称为完整运行（run-to-completion）特性。

````js
var a = 1; 
var b = 2; 
function foo() { 
 a++; 
 b = b * a; 
 a = b + 3; 
} 
function bar() { 
 b--; 
 a = 8 + b; 
 b = a * 2; 
} 
// ajax(..)是某个库中提供的某个Ajax函数
ajax( "http://some.url.1", foo ); 
ajax( "http://some.url.2", bar );
````

 1 是同步的（现在运行），而块 2 和块 3 是异步的（将来运行），也就是说，它们的运行在时间上是分隔的。

````js
// 块 1：
var a = 1; 
var b = 2; 
// 块 2（foo()）：
a++; 
b = b * a; 
a = b + 3; 
// 块 3（bar()）：
b--; 
a = 8 + b; 
b = a * 2; 
````

块 2 和块 3 哪个先运行都有可能，所以如下所示，这个程序有两个可能输出。

````js
输出 1：
var a = 1; 
var b = 2; 
// foo() 
a++; 
b = b * a; 
a = b + 3; 
// bar() 
b--; 
a = 8 + b; 
b = a * 2; 
a; // 11 
b; // 22 
输出 2：
var a = 1; 
var b = 2; 
// bar() 
b--; 
a = 8 + b; 
b = a * 2; 
// foo() 
a++; 
b = b * a; 
a = b + 3; 
a; // 183 
b; // 180
````

在 JavaScript 的特性中，这种函数顺序的不确定性就是通常所说的竞态条件（race condition），foo() 和 bar() 相互竞争，看谁先运行。具体来说，因为无法可靠预测 a 和 b的最终结果，所以才是竞态条件。

### 并发

###　协作

### 任务

# 回调

# Promise

绝大多数 JavaScript/DOM 平台新增的异步 API 都是基于 Promise 构建的。

## 什么是 [Promise](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)

### P202

