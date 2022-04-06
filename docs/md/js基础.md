# Function.prototype.call()

**call()**方法使用一个指定的 **this** 值和单独给出的一个或多个参数来调用一个函数

````javascript
function Product(name, price) {
  this.name = name;
  this.price = price;
}

function Food(name, price) {
  Product.call(this, name, price);
  this.category = 'food';
}

console.log(new Food('cheese', 5).name);
// expected output: "cheese"

````

## [语法](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/call#语法)

```
function.call(thisArg, arg1, arg2, ...)
```

### 参数

- `thisArg`

  可选的。在 *`function`* 函数运行时使用的 `this` 值。请注意，`this`可能不是该方法看到的实际值：如果这个函数处于[非严格模式](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Strict_mode)下，则指定为 `null` 或 `undefined` 时会自动替换为指向全局对象，原始值会被包装。

- `arg1, arg2, ...`

  指定的参数列表。

### 返回值

使用调用者提供的 `this` 值和参数调用该函数的返回值。若该方法没有返回值，则返回 `undefined`。

# Function.prototype.apply()

## [语法](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/apply#syntax)

```
func.apply(thisArg, [argsArray])
```

### [参数](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/apply#parameters)

- `thisArg`

  必选的。在 *`func`* 函数运行时使用的 `this` 值。请注意，`this`可能不是该方法看到的实际值：如果这个函数处于[非严格模式](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Strict_mode)下，则指定为 `null` 或 `undefined` 时会自动替换为指向全局对象，原始值会被包装。

- `argsArray`

  可选的。一个数组或者类数组对象，其中的数组元素将作为单独的参数传给 `func` 函数。如果该参数的值为 [`null`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/null) 或 [`undefined`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/undefined)，则表示不需要传入任何参数。从ECMAScript 5 开始可以使用类数组对象。[浏览器兼容性](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/apply#浏览器兼容性) 请参阅本文底部内容。

### [返回值](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/apply#返回值)

调用有指定`**this**`值和参数的函数的结果。



**备注：**call()方法的作用和 apply() 方法类似，区别就是`call()`方法接受的是**参数列表**，而`apply()`方法接受的是**一个参数数组**。





# Object

**Object** 是 JavaScript 的一种 [数据类型](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Data_structures) 。它用于存储各种键值集合和更复杂的实体。Objects 可以通过 `Object()` 构造函数或者使用 [对象字面量](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Object_initializer) 的方式创建

## [描述](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object#描述)

在JavaScript中，几乎所有的对象都是`Object`类型的实例，它们都会从`Object.prototype`继承属性和方法，虽然大部分属性都会被覆盖（shadowed）或者说被重写了（overridden）。 除此之外，`Object` 还可以被故意的创建，但是这个对象并不是一个“真正的对象”（例如：通过 `Object.create(null)`），或者通过一些手段改变对象，使其不再是一个“真正的对象”（比如说: `Object.setPrototypeOf`）。

通过原型链，所有的 `object` 都能观察到 Object 原型对象（Object prototype object）的改变，除非这些受到改变影响的属性和方法沿着原型链被进一步的重写。尽管有潜在的危险，但这为覆盖或扩展对象的行为提供了一个非常强大的机制。

`Object` 构造函数为给定的参数创建一个包装类对象（object wrapper），具体有以下情况：

- 如果给定值是 [`null`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/null) 或 [`undefined`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/undefined)，将会创建并返回一个空对象
- 如果传进去的是一个基本类型的值，则会构造其包装类型的对象
- 如果传进去的是引用类型的值，仍然会返回这个值，经他们复制的变量保有和源对象相同的引用地址

当以非构造函数形式被调用时，`Object` 的行为等同于 `new Object()`。

可查看 [对象初始化/字面量语法](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Object_initializer)。

## [构造函数](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object#构造函数)

- [`Object()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/Object)

  创建一个新的 `Object` 对象。该对象将会包裹（wrapper）传入的参数 

## [静态方法](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object#静态方法)

- [`Object.assign()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)

  通过复制一个或多个对象来创建一个新的对象。

- [`Object.create()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/create)

  使用指定的原型对象和属性创建一个新对象。

- [`Object.defineProperty()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)

  给对象添加一个属性并指定该属性的配置。

- [`Object.defineProperties()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperties)

  给对象添加多个属性并分别指定它们的配置。

- [`Object.entries()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/entries)

  返回给定对象自身可枚举属性的 `[key, value]` 数组。

- [`Object.freeze()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze)

  冻结对象：其他代码不能删除或更改任何属性。

- [`Object.getOwnPropertyDescriptor()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyDescriptor)

  返回对象指定的属性配置。

- [`Object.getOwnPropertyNames()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyNames)

  返回一个数组，它包含了指定对象所有的可枚举或不可枚举的属性名。

- [`Object.getOwnPropertySymbols()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertySymbols)

  返回一个数组，它包含了指定对象自身所有的符号属性。

- [`Object.getPrototypeOf()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/GetPrototypeOf)

  返回指定对象的原型对象。

- [`Object.is()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/is)

  比较两个值是否相同。所有 NaN 值都相等（这与==和===不同）。

- [`Object.isExtensible()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/isExtensible)

  判断对象是否可扩展。

- [`Object.isFrozen()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/isFrozen)

  判断对象是否已经冻结。

- [`Object.isSealed()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/isSealed)

  判断对象是否已经密封。

- [`Object.keys()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/keys)

  返回一个包含所有给定对象**自身**可枚举属性名称的数组。

- [`Object.preventExtensions()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/preventExtensions)

  防止对象的任何扩展。

- [`Object.seal()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/seal)

  防止其他代码删除对象的属性。

- [`Object.setPrototypeOf()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/setPrototypeOf)

  设置对象的原型（即内部 `[[Prototype]]` 属性）。

- [`Object.values()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/values)

  返回给定对象自身可枚举值的数组。

## [实例属性](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object#实例属性)

- [`Object.prototype.constructor`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/constructor)

  一个引用值，指向 Object 构造函数

- [`Object.prototype.__proto__`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/proto)

  指向一个对象，当一个 object 实例化时，使用该对象作为实例化对象的原型

## [实例方法](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object#实例属性_2)

- [`Object.prototype.__defineGetter__()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/__defineGetter__)

  将一个属性与一个函数相关联，当该属性被访问时，执行该函数，并且返回函数的返回值。

- [`Object.prototype.__defineSetter__()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/__defineSetter__)

  将一个属性与一个函数相关联，当该属性被设置时，执行该函数，执行该函数去修改某个属性。

- [`Object.prototype.__lookupGetter__()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/__lookupGetter__)

  返回一个函数，该函数通过给定属性的 `Object.prototype.__defineGetter__()` 得出。

- [`Object.prototype.__lookupSetter__()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/__lookupSetter__)

  返回一个函数，该函数通过给定属性的 `Object.prototype.__defineSetter__()` 得出。

- [`Object.prototype.hasOwnProperty()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/hasOwnProperty)

  返回一个布尔值，用于表示一个对象自身是否包含指定的属性，该方法并不会查找原型链上继承来的属性。

- [`Object.prototype.isPrototypeOf()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/isPrototypeOf)

  返回一个布尔值，用于表示该方法所调用的对象是否在指定对象的原型链中。

- [`Object.prototype.propertyIsEnumerable()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/propertyIsEnumerable)

  返回一个布尔值，用于表示内部属性 [ECMAScript [[Enumerable\]] attribute](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Data_structures#属性) 是否被设置。

- [`Object.prototype.toLocaleString()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/toLocaleString)

  调用 `toString()。`

- [`Object.prototype.toString()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/toString)

  返回一个代表该对象的字符串。

- [`Object.prototype.valueOf()`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/valueOf)

  返回指定对象的原始值。