---
title: TypeScript
date: 2019-01-30 10:59:19
tags: [2.7]
---

# 简介

## TypeScript与ECMAScript

[TypeScript](http://www.typescriptlang.org/index.html)是ECMAScript的超集，它支持JavaScript的所有语法和语义，并且在此之上提供了更多额外的特性。JavaScript是ECMAScript的实现。

ECMAScript的标准发布在 <https://tc39.github.io/ecma262/> 。

[ES6 Fiddle](http://www.es6fiddler.net)：在线运行ES6代码的网站。

[Babel](http://babeljs.io/repl)：在线将ES6脚本转化为ES5脚本。

TypeScript和JavaScript不同的地方有两处：

1. 可选参数：在JavaScript中，调用一个N元函数时可以传递给它小于或等于N个实参，没有传递值的参数自动是可选的；而在TypeScript中，可选参数要显式在参数名末尾附加一个问号。
2. 在JavaScript中，可以使用空的对象字面量来初始化变量，并使用点号立即附加属性；而在TypeScript中，需要使用方括号。

## TypeScript vs Dart

Dart与第三方JavaScript库的互操作性不是很好；

Dart需要Dart VM才能运行，并不是所有浏览器能带有Dart VM；

Dart生成的JavaScript不易于阅读。

# 入门

## 安装

```bash
$ npm install -g typescript
```

查看版本：

```bash
$ tsc -v
```

## 编程

hello.ts：

```typescript
class Student {
    fullName: string;
    constructor(public firstName: string, public middleInitial: string, public lastName: string) {
        this.fullName = firstName + " " + middleInitial + " " + lastName;
    }
}

interface Person {
    firstName: string;
    lastName: string;
}

function greeter(person : Person) {
    return "Hello, " + person.firstName + " " + person.lastName;
}

let user = new Student("Jane", "M.", "User");

document.body.innerHTML = greeter(user);
```

## 编译

```bash
$ tsc hello.ts
```

将`hello.ts`编译为`hello.js`（ES5）

`-t`或`--target`：指定生成的目标代码，取值有：`ES3`、`ES5`、`ES2015`、`ES2016`、`ES2017`

`--noResolve`：编译器只将命令行上显式列出的.ts文件编译成目标代码，所有未显式列出的文件不会包含在目标代码中。

`--sourcemap`：生成source map文件，将TypeScript源码映射到JavaScript中，这样可以在TypeScript代码中设置断点，即使执行的是JavaScript。

### 配置——tsconfig.json



## 运行

### 在命令行上运行

```bash
$ node hello.js
```

> 由于node.js没有浏览器DOM支持，因此执行上面命令将报“document is not defined”错误。改成用`console.log()`输出。

如果要直接运行typescript脚本，可以使用`ts-node`模块：

```bash
$ npm install -g ts-node
$ ts-node hello.ts
```

### 在浏览器上运行

首先，加上一个页面——hello.html：

```html
<!DOCTYPE html>
<html>
  <head><title>TypeScript Greeter</title></head>
  <body>
    <script src="hello.js"></script>
  </body>
</html>：
```

然后，在浏览器中打开`hello.html`。

### 在线运行TypeScript脚本

[TypeScript Playground](http://www.typescriptlang.org/play/index.html)

# 基本类型

## 布尔型——boolean

布尔型只有两个取值：`true`（真）和`false`（假）。

## 数值型——number

在TypeScript中，数值型都是浮点型。

十进制：`6`；

十六进制：`0xf00d`；

二进制：`0b1010`；

八进制：`0o744`。

## 字符串型——string

和JavaScript一样，字符串可以使用双引号`"`或单引号`'`包围。

### 模版字符串

模版字符串可以定义多行文本和内嵌表达式。 这种字符串是被反引号包围，并且以`${ expr }`这种形式嵌入**插值**：

```typescript
let sentence: string = `Hello, my name is ${ name }.
                        I'll be ${ age + 1 } years old next month.`;
```

这与下面定义`sentence`的方式效果相同：

```javascript
let sentence: string = "Hello, my name is " + name + ".\n\n" +
                       "I'll be " + (age + 1) + " years old next month.";

```

### 带标签的模板字符串

如果一个模板字符串紧跟在一个函数名称引用的后面，那么这个字符串首先会被计算，之后被传入到函数中做进一步处理。模板中的字符串部分会被组成一个数组作为函数第一个参数，而每个插值会被当成单独的参数依次传入函数的第二个参数、……。

```javascript
function currencyAdjustment(stringParts, region, amount) {
  console.log(stringParts);
  console.log(region);
  console.log(amount);
  
  var sign;
  if (region == 1) {
    sign = "$";
  } else {
    sign = "\u20AC"  //欧元货币符号
    amount = 0.9 * amount;
  }
  
  return `${stringParts[0]}${sign}${amount}${stringParts[2]}`;
}

var amount = 100;
var region = 2;

var message = currencyAdjustment`You've earned ${region} ${amount}!`;
console.log(message);

```

上面的代码将输出：

```bash
["You've earned ", " ", "!"]
2
100
You've earned €90! 

```

## 数组

有三种方式可以定义数组。 

第一种：

```typescript
let list: number[] = [1, 2, 3];
```

第二种，使用泛型数组`Array<元素类型>`：

```typescript
let list: Array<number> = [1, 2, 3];
```

第三种：

```typescript
let list = new Array();
list[0] = 100;
list[1] = "Adam";
list[2] = true;
list[0] = "Tuesday";  //通过赋值可以改变元素类型。
```



## 元组

元组类型用于表示一个元素数量固定和元素类型确定的数组，各元素的类型不必相同。 

```typescript
// Declare a tuple type
let x: [string, number];

// Initialize it
x = ['hello', 10]; // OK

// Initialize it incorrectly
x = [10, 'hello']; // Error

```

可通过索引来访问数组的元素：

```typescript
console.log(x[0].substr(1)); // OK
console.log(x[1].substr(1)); // Error, 'number' does not have 'substr'

```

允许访问越界的元素，即允许动态增加元组的长度，而且新增元素的索引不必与原有元素索引连续。甚至，可以使用负数索引。这些新增加的元素的类型是元组所有原有元素的类型组成的联合类型：

```typescript
x[6] = 'world'; // OK, 字符串可以赋值给(string | number)类型
console.log(x[-10].toString()); // OK, 'string' 和 'number' 都有 toString
x[20] = true; // Error, 布尔不是(string | number)类型

```

## 枚举

```typescript
enum Color {Red, Green, Blue};

let c: Color = Color.Green;

```

默认情况下，从`0`开始依次为枚举量编号。 也可以手动的指定枚举量的编号（可以使用表达式）。 例如，我们将上面的例子改成从`1`开始编号：

```typescript
enum Color {Red = 1, Green, Blue};

```

或者，全部都采用手动编号：

```typescript
enum Color {Red = 1, Green = 2, Blue = 4};

```

你可以由枚举量的编号得到它的名字：

```typescript
let colorName: string = Color[2];

```

枚举量的编号最好不要重复：

```typescript
enum Color { Red = 1, Green = 2, Blue = 2 };
let colorName: string = Color[2];
alert(colorName);  //“Blue”

```

### 常量枚举

常量枚举的值只能使用常数枚举表达式并且不同于常规的枚举的是它们在编译阶段会被删除。

```typescript
const enum Directions {
  Up,
  Down,
  Left,
  Right
}

let directions = [Directions.Up, Directions.Down, Directions.Left, Directions.Right]

```

生成后的代码为：（常量枚举被移除）

```typescript
var directions = [0 /* Up */, 1 /* Down */, 2 /* Left */, 3 /* Right */];

```

### 外部枚举

外部枚举用来描述已经存在的枚举类型的形状。

```typescript
declare enum Enum {
  A = 1,
  B,
  C = 2
}

```

外部枚举和非外部枚举之间有一个重要的区别，在正常的枚举里，没有初始化方法的成员被当成常数成员。 对于非常数的外部枚举而言，没有初始化方法时被当做需要经过计算的。

## any

TypeScript中的所有类型都是`any`类型的子类型。声明成`any`类型的变量可以持有任何类型的值：

```typescript
let notSure: any = 4;
notSure = "maybe a string instead";
notSure = false; // okay, definitely a boolean

```

将变量声明成`any`类型，基本上就是在编写动态类型，这样做会失去TypeScript编译器带来的类型检查优点。所以，要慎用`any`。

`any`与`Object`有点类似，但`Object`类型的变量只是允许你给它赋任意值，但是却不能够在它上面调用任意的方法，即便它真的有这些方法：

```typescript
let notSure: any = 4;
notSure.ifItExists(); // okay, ifItExists might exist at runtime
notSure.toFixed(); // okay, toFixed exists (but the compiler doesn't check)

let prettySure: Object = 4;
prettySure.toFixed(); // Error: Property 'toFixed' doesn't exist on type 'Object'.

```

## void

某种程度上来说，`void`类型像是与`any`类型相反，它表示没有任何类型。 当一个函数没有返回值时，你通常会见到其返回值类型是`void`：

```typescript
function warnUser(): void {
  alert("This is my warning message");
}

```

声明一个`void`类型的变量没有什么大用，因为你只能为它赋予`undefined`和`null`：

```typescript
let unusable: void = undefined;

```

## undefined

`undefined`类型的变量只能赋给`undefined`值。

```typescript
let u: undefined = undefined;

```



## null

`null`类型的变量只能赋给`null`值。

默认情况下`null`和`undefined`是所有类型的子类型。例如可以把`null`和`undefined`赋值给`number`类型的变量。

然而，当你指定了`--strictNullChecks`标记，`null`和`undefined`只能赋值给`void`类型和它们各自类型的变量。 这能避免很多常见的问题。 在这种情况下，如果你想在某处传入一个`string`或`null`或`undefined`，你就要使用联合类型`string | null | undefined`。

## never

`never`类型表示的是那些永不存在的值的类型。 例如，`never`类型是那些总是会抛出异常或根本就不会有返回值的函数表达式或箭头函数表达式的返回值类型； 变量也可能是`never`类型，当它们被永不为`true`的类型守卫（Guard）所约束时。

`never`类型是任何类型的子类型，也可以赋值给任何类型；然而，没有类型是`never`的子类型或可以赋值给`never`类型（除了`never`本身之外）。 即使`any`也不可以赋值给`never`。

下面是一些返回never类型的函数：

```typescript
// 返回never的函数必须存在无法达到的终点
function error(message: string): never {
  throw new Error(message);
}

// 推断的返回值类型为never
function fail() {
  return error("Something failed");
}

// 返回never的函数必须存在无法达到的终点
function infiniteLoop(): never {
  while (true) {
  }
}


```

## 字符串字面量类型

字符串字面量类型，它的类型名是一个字符串，它的实例就是它本身。

字符串字面量类型可以与联合类型，类型检查和类型别名很好的配合。 通过结合使用这些特性，你可以实现类似枚举类型的字符串。

```typescript
type Easing = "ease-in" | "ease-out" | "ease-in-out";  // Easing是联合类型的别名
class UIElement {
  animate(dx: number, dy: number, easing: Easing) {
    if (easing === "ease-in") {
      // ...
    }
    else if (easing === "ease-out") {
    }
    else if (easing === "ease-in-out") {
    }
    else {
      // error! should not pass null or undefined.
    }
  }
}

let button = new UIElement();
button.animate(0, 0, "ease-in"); // 只能从三种允许的字符串中选择其一来做为参数传递
button.animate(0, 0, "uneasy"); // error: "uneasy" is not allowed here

```

字符串字面量类型用于函数重载：

```typescript
function createElement(tagName: "img"): HTMLImageElement;
function createElement(tagName: "input"): HTMLInputElement;
// ... more overloads ...
function createElement(tagName: string): Element {
  // ... code goes here ...
}

```

## 符号类型

符号类型的值是通过`Symbol`构造函数创建的。

```typescript
let sym1 = Symbol();
let sym2 = Symbol("key"); // 可选的字符串key

```

符号是不可改变且唯一的。

```typescript
let sym2 = Symbol("key");
let sym3 = Symbol("key");
sym2 === sym3; // false, symbols是唯一的

```

像字符串一样，符号也可以被用做对象属性的键：

```typescript
let sym = Symbol();
let obj = {
	[sym]: &quot;value&quot;
};
console.log(obj[sym]); // "value"

```

作为类成员：

```typescript
const getClassNameSymbol = Symbol();

class C {
  [getClassNameSymbol](){
    return "C";
  }
}

let c = new C();
let className = c[getClassNameSymbol](); // "C"

```

### Symbol

Symbol.hasInstance：

方法，会被`instanceof`运算符调用。构造器对象用来识别一个对象是否是其实例。

Symbol.isConcatSpreadable：

布尔值，表示当在一个对象上调用`Array.prototype.concat`时，这个对象的数组元素是否可展开。

Symbol.iterator：

方法，被*for-of*语句调用。返回对象的默认迭代器。

Symbol.match：

方法，被`String.prototype.match`调用。正则表达式用来匹配字符串。

Symbol.replace：

方法，被`String.prototype.replace`调用。正则表达式用来替换字符串中匹配的子串。

Symbol.search：

方法，被`String.prototype.search`调用。正则表达式返回被匹配部分在字符串中的索引。

Symbol.species：

函数值，为一个构造函数。用来创建派生对象。

Symbol.split：

方法，被`String.prototype.split`调用。正则表达式来用分割字符串。

Symbol.toPrimitive：

方法，被`ToPrimitive`抽象操作调用。把对象转换为相应的原始值。

Symbol.toStringTag：

方法，被内置方法`Object.prototype.toString`调用。返回创建对象时默认的字符串描述。

Symbol.unscopables：

对象，它自己拥有的属性会被`with`作用域排除在外。

## this

`this`类型表示的是某个包含类或接口的子类型。它是多态的，即**F-bounded多态性**。

```typescript
class BasicCalculator {
  public constructor(protected value: number = 0) { }
  public currentValue(): number {
  	return this.value;
  }
  public add(operand: number): this {
    this.value += operand;
    return this;
  }
  public multiply(operand: number): this {
    this.value *= operand;
    return this;
  }
  // ... other operations go here ...
}

let v = new BasicCalculator(2)
              .multiply(5)
              .add(1)
              .currentValue();

class ScientificCalculator extends BasicCalculator {
  public constructor(value = 0) {
    super(value);
  }

  public sin() {
    this.value = Math.sin(this.value);
    return this;
  }
  // ... other operations go here ...
}

let v = new ScientificCalculator(2)
              .multiply(5)
              .sin()  // 这是ScientificCalculator新增的方法
              .add(1)
              .currentValue();

```

# 声明

## 变量

### var

在函数、模块、命名空间或全局作用域内部，通过var声明的变量的作用域总是包含它的整个函数、模块、命名空间或全局作用域，而不管声明是否位于某个块内。这种作用域规则有时又称为函数作用域，函数参数也是函数作用域。

在`var`声明之前，可以访问该变量，只不过值是`undefined`。

```typescript
function f(shouldInitialize: boolean) {
  // alert(x);  //允许在x声明之前读或写它，但它还没初始化，因此x此时的值为undefined。
  if (shouldInitialize) {
  	var x = 10;
  }

  return x;
}

f(true);  // returns '10'
f(false); // returns 'undefined'

```

> 在ES5中，函数定义也会被提升，但ES6中则使用块级作用域。

函数作用域允许多次声明同一个变量，后声明的覆盖之前声明的。但如果先用`let`声明，则不能再用`var`声明同一变量。反之也一样。

```typescript
function sumMatrix(matrix: number) {
  var sum = 0;
  for (var i = 0; i < matrix.length; i++) {
    var currentRow = matrix[i];
    for (var i = 0; i < currentRow.length; i++) {
      sum += currentRow[i];
    }
  }
  return sum;
}

```

函数作用域的怪异行为：

例1：

```typescript
var fns = [];

for (var i = 0; i < 5; i += 1) {
  fns.push(function() {
    console.log(i);
  })
}

fns.forEach(fn => fn());

```

输出：

```bash
5
5
5
5
5

```

上例的问题在于`console.log(i)`是在for循环结束之后，在`fns.forEach()`调用中才执行的。

例2：

```typescript
for (var i = 0; i < 10; i++) {
  setTimeout(function() { console.log(i); }, 100 * i);
}

```

输出：

```bash
10
10
10
…

```

`setTimeout`在若干毫秒后执行一个函数，并且是在for循环结束后。 for循环结束后，i的值为`10`。 所以当函数被调用的时候，它会打印出`10`！

一个通常的解决方法是使用立即执行的函数表达式（IIFE）来捕获每次迭代时i的值：

```typescript
for (var i = 0; i < 10; i++) {
  // capture the current state of 'i'
  // by invoking a function with its current value
  (function(i) {  //参数i会覆盖for循环里的i
    setTimeout(function() { console.log(i); }, 100 * i);
  })(i);
}

```



### let

let声明使用的是词法作用域或**块作用域**，它只在所声明的块（例如：块语句、函数体、模块、命名空间等）中可访问，如果不在任何块中声明，则它的作用域是全局的。这是let与var的区别之处。

不能在同一个块作用域里多次声明同一个变量或常量（不管这些变量原来是var声明、let声明或者函数参数）。

```typescript
function f(x) {
  let x = 100; // error: interferes with parameter declaration
}

function g() {
  let x = 100;
  var x = 100; // error: can't have both declarations of 'x'
}

```

在嵌套块作用域中，内层作用域变量会屏蔽同名的外层作用域变量。

```typescript
function f(condition, x) {
  if (condition) {
    let x = 100;
    return x;
  }

  return x;
}

f(false, 0); // returns 0
f(true, 0);  // returns 100

```

拥有块级作用域的变量的另一个特点是，它们不能在被声明之前读或写。

```typescript
a++; // illegal to use 'a' before it's declared;
let a;

```

注意：我们仍然可以在一个拥有块作用域变量被声明前获取它。 只是我们不能在变量声明前去调用那个函数。

```typescript
function foo() {
  // okay to capture 'a'
  return a;
}

// 不能在'a'被声明前调用'foo'
// 运行时应该抛出错误
foo();

let a;

```

## 常量

```typescript
const numLivesForCat = 9;

```

`const`声明是使用块级作用域。

## 别名*

可以使用`import`来给任意标识符创建别名，也包括导入的模块中的对象。

```typescript
namespace Shapes {
  export namespace Polygons {
    export class Triangle { }
    export class Square { }
  }
}

import polygons = Shapes.Polygons;
let sq = new polygons.Square(); // Same as "new Shapes.Polygons.Square()"

```

这与使用`var`相似，但它还适用于类型和导入的具有命名空间含义的符号。 重要的是，对于值来讲，`import`会生成与原对象不同的引用，所以改变别名的属性值并不会影响原对象的属性值。

## 解构

解构类似于其他语言中的模式匹配。

### 解构数组

```typescript
let input = [1, 2];
let [first, second] = input;
console.log(first); // outputs 1
console.log(second); // outputs 2

```

交换两个变量：

```typescript
[first, second] = [second, first];

```

作用于函数参数：

```typescript
function f([first, second]: [number, number]) {
  console.log(first);
  console.log(second);
}

f(input);

```

可以在数组里使用`...`语法创建剩余变量：

```typescript
let [first, ...rest] = [1, 2, 3, 4];
console.log(first); // outputs 1
console.log(rest); // outputs [ 2, 3, 4 ]

```

可以忽略你不关心的尾随元素：

```typescript
let [first] = [1, 2, 3, 4];
console.log(first); // outputs 1

```

或其它元素：

```typescript
let [, second, , fourth] = [1, 2, 3, 4];

```

### 解构对象

```typescript
let o = {
  a: "foo",
  b: 12,
  c: "bar"
}

let {a, b} = o;

```

就像数组解构，你可以不经过声明就直接赋值：

```typescript
({a, b} = {a: "baz", b: 101});

```

> 注意，我们需要用括号将它括起来，因为Javascript通常会将以 `{` 起始的语句解析为一个块。

可以在对象里使用`...`语法创建剩余变量：

```typescript
let { a, ...passthrough } = o;
let total = passthrough.b + passthrough.c.length;

```

#### 属性别名

可以给属性以不同的名字：

```typescript
let {a: newName1, b: newName2} = o;

```

这里“:”不是指示类型，而是表示表示别名。即`newName1`是`a`的别名。上面的语句，加上类型指示，则变成：

```typescript
let {a: newName1, b: newName2}: {a: string, b: number} = o;

```

#### 默认值

默认值可以让你在属性为 `undefined` 时使用缺省值：

```typescript
function keepWholeObject(wholeObject: {a: string, b?: number}) {
  let {a, b = 1001} = wholeObject;
}

```

当`b`为 `undefined`时，使用默认值`1001`。

### 函数声明中的解构

解构也能用于函数声明。

```typescript
type C = { a: string, b?: number }

function f({ a, b = 0 }: C = { a: "" }): void {
    // ...
}
// 或者
//function f({ a, b } = { a: "", b: 0 }): void {
    // ...
//}

f({ a: "yes" }); // ok, default b = 0
f(); // ok, default to {a: ""}, which then defaults b = 0
f({}); // error, 'a' is required if you supply an argument

```

## 展开

展开操作符正与解构相反。 它允许你将一个数组展开为另一个数组，或将一个对象展开为另一个对象。 例如：

```typescript
let first = [1, 2];
let second = [3, 4];
let bothPlus = [0, ...first, ...second, 5];

```

这会令`bothPlus`的值为`[0, 1, 2, 3, 4, 5]`。 展开操作创建了 `first`和`second`的一份浅拷贝。

还可以展开对象：

```typescript
let defaults = { food: "spicy", price: "$$", ambiance: "noisy" };
let search = { ...defaults, food: "rich" };

```

`search`的值为`{ food: "rich", price: "$$", ambiance: "noisy" }`。在展开时，如果存在同名属性（例如：`food`属性），则后面的属性会覆盖前面的属性。

展开对象时，只会将对象自身的可枚举属性展开到当前位置，而不包含它的方法：

```typescript
class C {
  p = 12;
  m() {
  }
}
let c = new C();
let clone = { ...c };
clone.p; // ok
clone.m(); // error!

```

TypeScript编译器不允许展开泛型函数上的类型参数。 这个特性会在TypeScript的未来版本中考虑实现。

## 声明合并

“声明合并”是指编译器将针对同一个名字的多个独立声明合并为单一声明。 合并后的声明同时拥有原先多个声明的特性。

### 接口合并

接口合并的机制是把双方的成员放到一个同名的接口里。

接口的非函数的成员必须是唯一的。 如果两个接口中同时声明了同名的非函数成员编译器则会报错。

```typescript
interface Box {
  height: number;
  width: number;
}

interface Box {
  scale: number;
}

let box: Box = {height: 5, width: 6, scale: 10};

```

对于函数成员，每个同名函数声明都会被当成这个函数的一个重载。 同时需要注意，当接口A与后来的接口A合并时，后面的接口具有更高的优先级。

```typescript
interface Cloner {
  clone(animal: Animal): Animal;
}

interface Cloner {
  clone(animal: Sheep): Sheep;
}

interface Cloner {
  clone(animal: Dog): Dog;
  clone(animal: Cat): Cat;
}

```

这三个接口合并成一个声明：

```typescript
interface Cloner {
  clone(animal: Dog): Dog;
  clone(animal: Cat): Cat;
  clone(animal: Sheep): Sheep;
  clone(animal: Animal): Animal;
}

```

注意每组接口里的声明顺序保持不变，但各组接口之间的顺序是越后来接口的重载方法出现在越靠前位置。

这个规则有一个例外是当出现特殊的函数签名时。 如果签名里有一个参数的类型是单一的字符串字面量（比如，不是字符串字面量的联合类型），那么它将会被提升到重载列表的最顶端。

```typescript
interface Document {
  createElement(tagName: any): Element;
}

interface Document {
  createElement(tagName: "div"): HTMLDivElement;
  createElement(tagName: "span"): HTMLSpanElement;
}

interface Document {
  createElement(tagName: string): HTMLElement;
  createElement(tagName: "canvas"): HTMLCanvasElement;
}

```

合并后的`Document`将会像下面这样：

```typescript
interface Document {
  createElement(tagName: "canvas"): HTMLCanvasElement;
  createElement(tagName: "div"): HTMLDivElement;
  createElement(tagName: "span"): HTMLSpanElement;
  createElement(tagName: string): HTMLElement;
  createElement(tagName: any): Element;
}

```

### 命名空间合并

```typescript
namespace Animals {
  export class Zebra { }
}

namespace Animals {
  export interface Legged { numberOfLegs: number; }
  export class Dog { }
}

```

等同于：

```typescript
namespace Animals {
  export interface Legged { numberOfLegs: number; }
  export class Zebra { }
  export class Dog { }
}

```

非导出成员仅在其原有的（合并前的）命名空间内可见。这就是说合并之后，从其它命名空间合并进来的成员无法访问非导出成员。

### 命名空间与其他声明合并

## 外部声明

# 表达式和运算符

## 算术运算符

## 比较运算符

## 逻辑运算符

## 位运算符

## 赋值运算符

## 条件运算符

# 语句

## 条件语句

## 多分支语句

## 迭代语句

### while循环

### do-while循环

### 迭代器

当一个对象实现了`Symbol.iterator`方法时，我们认为它是可迭代的。

 一些内置的类型如`Array`，`Map`，`Set`，`String`，`Int32Array`，`Uint32Array`等都已经实现了各自的`Symbol.iterator`。 

### for循环

### for-in循环

### for-of循环

for-of语句通过调用可迭代对象上的`Symbol.iterator`方法，来遍历它。

```typescript
let list = [4, 5, 6];

for (let i of list) {
  console.log(i); // "4", "5", "6"
}

```

for-of语句与for-in均可迭代一个列表，它们的区别：

1）它们用于迭代的值却不同，for-in迭代的是对象的键的列表，而for-of则迭代对象的键对应的值。

```typescript
for (let i in list) {
	console.log(i); // "0", "1", "2",
}

```

2）for-in可以操作任何对象，而for-of只专注于可迭代对象。

注意：当生成目标为ES5或ES3，迭代器只允许在Array类型上使用。 在非数组值上使用for-of语句会得到一个错误，就算这些非数组值已经实现了`Symbol.iterator`属性。只有目标是ECMAScipt 2015或更高才能使用上面所有特性。

# 函数

## 函数定义

### 函数声明

```typescript
function add(x: number, y: number): number {
  return x + y;
}
```

由于TypeScript有类型推断能力，因此，上面的类型指示都可以省略：

```typescript
function add(x, y) {
  return x + y;
}
```

函数声明在函数定义之前，就可以调用该函数。例如：

```typescript
myFunc();
…
function myFunc() {
  console.log("This is a statement.");
}
```



### 函数表达式

```typescript
let myAdd = function(x: number, y: number): number { return x+y; };
```

或者

```typescript
let myAdd = function(x, y) { return x + y; };
```

与函数声明不同，函数表达式在定义之前不能调用。

### 箭头函数（Lambda）

```typescript
(param1, param2, …, paramN) => { statements }
(param1, param2, …, paramN) => expression

// equivalent to: (param1, param2, …, paramN) => { return expression; }
// Parentheses are optional when there's only one parameter:
(singleParam) => { statements }
singleParam => { statements }

// A function with no parameters requires parentheses:
() => { statements }
() => expression // equivalent to: () => { return expression; }

// Parenthesize the body to return an object literal expression:
params => ({foo: bar})

// Rest parameters and default parameters are supported
(param1, param2, ...rest) => { statements }
(param1 = defaultValue1, param2, …, paramN = defaultValueN) => { statements }

// Destructuring within the parameter list is also supported
var f = ([a, b] = [1, 2], {x: c} = {x: a + b}) => a + b + c;
f();  // 6
```

## 调用函数

在JavaScript中调用函数时，提供的实参数量不必匹配函数的形参数量。如果调用函数时提供的实参数量少于形参数量，则那些没有对应实参的形参的值都是`undefined`。如果调用的实参数量多于形参数量，则多余的实参将被忽略。因此，在JavaScript中不能创建名称相同但参数数目不同的两个函数（即重载）。相反，如果定义了两个具有相同名称的函数，则第二个定义将自动替换第一个定义。而在TypeScript里，形参与实参的个数必须一致，因而函数也可以重载。

## 函数类型

函数的类型只是由参数类型和返回值组成的。 函数中使用的捕获变量不会体现在类型里。 实际上，这些变量是函数的隐藏状态并不是组成API的一部分。

### 箭头式

```typescript
let myAdd: (baseValue:number, increment:number) => number =
  function(x: number, y: number): number { return x + y; };
```

只要参数类型是匹配的，而不在乎参数名是否一致。

没有参数和返回值的函数类型写成：

```typescript
() => void
```

### 接口式

接口也可以描述函数类型。

为了使用接口表示函数类型，我们需要给接口定义一个调用签名。 它就像是一个只有参数列表和返回值类型的函数定义。参数列表里的每个参数都需要名字和类型。

```typescript
interface SearchFunc {
  (source: string, subString: string): boolean;
}

let mySearch: SearchFunc;

mySearch = function(source: string, subString: string) {
  let result = source.search(subString);
  if (result == -1) {
    return false;
  }
  else {
    return true;
  }
}
```

对于函数类型的类型检查来说，函数的参数名不需要与接口里定义的名字相匹配。另外，由于类型推断，`mySearch`的函数参数类型也可以不显式指定：

```typescript
mySearch = function(src, sub) {
  let result = src.search(sub);
  if (result == -1) {
    return false;
  }
  else {
    return true;
  }
}
```



## 可选参数

JavaScript里，每个参数都是可选的，可传也可不传。 没传参的时候，它的值就是`undefined`。但在TypeScript里，默认情况形参与实参的个数必须一致。

```typescript
function buildName(firstName: string, lastName: string) {
  return firstName + " " + lastName;
}

let result1 = buildName("Bob");                  // error, too few parameters
let result2 = buildName("Bob", "Adams", "Sr.");  // error, too many parameters
let result3 = buildName("Bob", "Adams");         // ah, just right

```

在TypeScript里我们可以在参数名旁使用`?`实现可选参数的功能：

```typescript
function buildName(firstName: string, lastName?: string) {
  if (lastName)
    return firstName + " " + lastName;
  else
    return firstName;
}

let result1 = buildName("Bob");  // works correctly now
let result2 = buildName("Bob", "Adams", "Sr.");  // error, too many parameters
let result3 = buildName("Bob", "Adams");  // ah, just right

```

可选参数必须跟在必须参数后面。

## 参数默认值

```typescript
function buildName(firstName: string, lastName = "Smith") {
  return firstName + " " + lastName;
}

let result1 = buildName("Bob");                  // works correctly now, returns "Bob Smith"
let result2 = buildName("Bob", undefined);       // still works, also returns "Bob Smith"
let result3 = buildName("Bob", "Adams", "Sr.");  // error, too many parameters
let result4 = buildName("Bob", "Adams");         // ah, just right

```

只有当用户没有传递这个参数或传递的值是`undefined`时，才会使用默认值。

参数默认值既可以是一个常量，也可以是函数调用：

```javascript
function calcTaxES6(income, state = getDefaultState()) {
  console.log("ES6. Calculating tax for the resident of " + state + " with the income " + income);
}

function getDefaultState() {
  return "Florida";
}

calcTaxES6(50000);

```

参数默认值与解构：

```typescript
function f({a, b} = {a: "", b: 0}): void {
  // ...
}

f(); // ok, default to {a: "", b: 0}

function f({a, b = 0} = {a: ""}): void {
  // ...
}

f({a: "yes"}) // ok, default b = 0
f() // ok, default to {a: ""}, which then defaults b = 0
f({}) // error, 'a' is required if you supply an argument

```

在所有必须参数后面的带默认值的参数都是可选的，与可选参数一样，在调用函数的时候可以省略。 也就是说可选参数与末尾的默认参数共享参数类型。

```typescript
function buildName(firstName: string, lastName?: string) {
  // ...
}

```

和

```typescript
function buildName(firstName: string, lastName = "Smith") {
  // ...
}

```

共享同样的类型`(firstName: string, lastName?: string) => …`。 

与普通可选参数不同的是，带默认值的参数不需要放在必须参数的后面。 如果带默认值的参数出现在必须参数前面，用户必须明确的传入`undefined`值来获得默认值。

```typescript
function buildName(firstName = "Will", lastName: string) {
  return firstName + " " + lastName;
}

let result1 = buildName("Bob");                  // error, too few parameters
let result2 = buildName("Bob", "Adams", "Sr.");  // error, too many parameters
let result3 = buildName("Bob", "Adams");         // okay and returns "Bob Adams"
let result4 = buildName(undefined, "Adams");     // okay and returns "Will Adams"

```

## 剩余参数

在JavaScript里，可以使用`arguments`来访问多余参数。在TypeScript里，可以使用剩余参数来接收不确定个数的参数（即可以一个都没有，也可以有任意个）。

剩余参数由“...”后跟参数名组成。剩余参数必须是函数定义的最后一个形参。

剩余参数实际上就是一个数组。

```typescript
function buildName(firstName: string, ...restOfName: string[]) {
  return firstName + " " + restOfName.join(" ");
}

let employeeName = buildName("Joseph", "Samuel", "Lucas", "MacKinzie");
let buildNameFun: (fname: string, ...rest: string[]) => string = buildName;

```

## 闭包

在TypeScript里，函数可以使用函数体外部的变量。 当函数这么做时，我们说它‘捕获’了这些变量。

```typescript
let z = 100;

function addToZ(x, y) {
  return x + y + z;
}

```

## 重载

```typescript
let suits = ["hearts", "spades", "clubs", "diamonds"];
function pickCard(x: {suit: string; card: number; }[]): number;
function pickCard(x: number): {suit: string; card: number; };
function pickCard(x): any {
  // Check to see if we're working with an object/array
  // if so, they gave us the deck and we'll pick the card
  if (typeof x == "object") {
    let pickedCard = Math.floor(Math.random() * x.length);
    return pickedCard;
  }
  // Otherwise just let them pick the card
  else if (typeof x == "number") {
    let pickedSuit = Math.floor(x / 13);
    return { suit: suits[pickedSuit], card: x % 13 };
  }
}

let myDeck = [{ suit: "diamonds", card: 2 }, { suit: "spades", card: 10 }, { suit: "hearts", card: 4 }];
let pickedCard1 = myDeck[pickCard(myDeck)];
console.log("card: " + pickedCard1.card + " of " + pickedCard1.suit);
let pickedCard2 = pickCard(15);
console.log("card: " + pickedCard2.card + " of " + pickedCard2.suit);

```

注意，`function pickCard(x): any`并不是重载列表的一部分，因此这里只有两个重载：一个是接收对象另一个接收数字。 以其它参数调用`pickCard`会产生错误。

# 接口

## 接口定义

```typescript
interface LabelledValue {
  label: string; // TypeScript接口可以只有属性
}

function printLabel(labelledObj: LabelledValue) {
  console.log(labelledObj.label);
}

let myObj = {size: 10, label: "Size 10 Object"};
printLabel(myObj); // 鸭子类型

```

## 可选属性

接口里的属性不全都是必需的。 有些是只在某些条件下存在，或者根本不存在。

可选属性名字后面要加一个`?`符号。

```typescript
interface SquareConfig {
  color?: string;
  width?: number;
}

function createSquare(config: SquareConfig): {color: string; area: number} {
  let newSquare = {color: "white", area: 100};
  if (config.color) {
  	newSquare.color = config.color;
  }
  if (config.width) {
    newSquare.area = config.width * config.width;
  }
  return newSquare;
}

let mySquare = createSquare({color: "black"});

```

可选属性的好处之一是可以对可能存在的属性进行预定义，好处之二是可以捕获引用了不存在的属性时的错误。 

### 额外属性检查

```typescript
interface SquareConfig {
  color?: string;
  width?: number;
}

function createSquare(config: SquareConfig): { color: string; area: number } {
  // ...
}

// error: 'colour' not expected in type 'SquareConfig'
let mySquare = createSquare({ colour: "red", width: 100 });

```

注意传入`createSquare`的参数拼写为`colour`而不是`color`。 虽然TypeScript使用鸭子类型，但在这里它仍会报错。因为对象字面量会被特殊对待而且会经过额外属性检查，当将它们赋值给含可变属性的类型的变量或作为参数传递的时候。 如果一个对象字面量存在任何“目标类型”不包含的属性时，你会得到一个错误。

绕开这些检查非常简单。 最简便的方法是使用类型断言：

```typescript
let mySquare = createSquare({ width: 100, opacity: 0.5 } as SquareConfig);

```

还可以通过添加一个字符串索引签名方式绕开这些检查：

```typescript
interface SquareConfig {
  color?: string;
  width?: number;
  [propName: string]: any;
}

```

`SquareConfig`带有上面定义的类型的`color`和`width`属性，并且还会带有任意数量的其它属性。

最后一种跳过这些检查的方式，就是将这个对象赋值给一个另一个变量： 因为`squareOptions`不会经过额外属性检查，所以编译器不会报错。

```typescript
let squareOptions = { colour: "red", width: 100 };
let mySquare = createSquare(squareOptions);

```

## 只读属性

可以在属性名前用`readonly`来定义只读属性：

```typescript
interface Point {
  readonly x: number;
  readonly y: number;
}

```

只读属性必须在声明时或构造函数里被初始化，之后不能被修改：

```typescript
let p1: Point = { x: 10, y: 20 };
p1.x = 5; // error!

```

最简单判断该用`readonly`还是`const`的方法是看要把它做为变量使用还是做为一个属性。 做为变量使用的话用`const`，若做为属性则使用`readonly`。

在类中使用`readonly`：

```typescript
class Octopus {
  readonly name: string;
  readonly numberOfLegs: number = 8;
  constructor (theName: string) {
    this.name = theName;
  }
}

let dad = new Octopus("Man with the 8 strong legs");
dad.name = "Man with the 3-piece suit"; // error! name is readonly.

```

### 只读数组——`ReadonlyArray<T>`

`ReadonlyArray<T>`类型，它与`Array<T>`相似，只是把所有可变方法去掉了，因此可以确保数组创建后再也不能被修改：

```typescript
let a: number[] = [1, 2, 3, 4];
let ro: ReadonlyArray<number> = a;
ro[0] = 12; // error!
ro.push(5); // error!
ro.length = 100; // error!
a = ro; // error!

```

上面代码的最后一行，可以看到就算把整个`ReadonlyArray`赋值到一个普通数组也是不可以的。 但是你可以用类型断言重写：

```typescript
a = ro as number[];

```

## 索引签名

```typescript
interface StringArray {
	[index: number]: string;
}

let myArray: StringArray;
myArray = ["Bob", "Fred"];
let myStr: string = myArray[0];

```

`StringArray`接口具有索引签名。 这个索引签名表示了当用`number`去索引`StringArray`时会得到`string`类型的返回值。

共支持两种索引签名：字符串和数字。 可以同时使用两种类型的索引，但是数字索引的返回值必须是字符串索引返回值类型的子类型。 这是因为当使用`number`来索引时，JavaScript会将它转换成`string`然后再去索引对象。 也就是说用`100`（一个`number`）去索引等同于使用`"100"`（一个`string`）去索引，因此两者需要保持一致。

```typescript
class Animal {
  name: string;
}

class Dog extends Animal {
  breed: string;
}

interface Okay {
  [x: number]: Dog;
  [x: string]: Animal;
}

```

由于JavaScript中访问对象属性可以使用`obj.property`和`obj["property"]`两种形式。因此，声明了索引签名的接口，也会约束接口的其他属性的类型必须是索引签名类型的子类型。

```typescript
interface NumberDictionary {
  [index: string]: number;
  length: number;    // 可以，length是number类型
  name: string       // 错误，`name`的类型不是索引类型的子类型
}

```

最后，你可以将索引签名设置为只读，这样就防止了给索引赋值：

```typescript
interface ReadonlyStringArray {
  readonly [index: number]: string;
}

let myArray: ReadonlyStringArray = ["Alice", "Bob"];
myArray[2] = "Mallory"; // error!

```

## 构造器类型

构造器类型与函数类型类似，只是多了一个`new`：

```typescript
interface ClockConstructor {
  new(hour: number, minute: number);
}

```

用接口描述的构造器类型不能被类所实现。因为当一个类实现了一个接口时，只对其实例部分进行类型检查。 `constructor`存在于类型的静态部分，所以不在检查的范围内。

```typescript
interface ClockInterface {
  tick();
}

function createClock(ctor: ClockConstructor, hour: number, minute: number): ClockInterface {
  return new ctor(hour, minute);
}

class DigitalClock implements ClockInterface {
  constructor(h: number, m: number) { }
  tick() {
    console.log("beep beep");
  }
}

class AnalogClock implements ClockInterface {
  constructor(h: number, m: number) { }
  tick() {
    console.log("tick tock");
  }
}

let digital = createClock(DigitalClock, 12, 17);
let analog = createClock(AnalogClock, 7, 32);

```

## 方法

在接口、类或对象字面量里定义的函数，称为方法。定义方法时，要省略`function`关键字。

## 扩展接口

和类一样，接口也可以相互扩展。并且，一个接口可以继承多个接口：

```typescript
interface Shape {
  color: string;
}

interface PenStroke {
  penWidth: number;
}

interface Square extends Shape, PenStroke {
  sideLength: number;
}

let square = <Square>{};
square.color = "blue";
square.sideLength = 10;
square.penWidth = 5.0;

```

## 扩展类

TypeScript中的类可以当作接口使用。因此，接口可以继承一个类，它会继承类的成员（包括`private`和`protected`成员）但不包括其实现。 当你创建了一个接口继承了一个拥有私有或受保护的成员的类时，这个接口类型只能被这个类或其子类所实现。

```typescript
class Control {
  private state: any;
}

interface SelectableControl extends Control {
  select(): void;
}

class Button extends Control {
  select() { }
}

class Image {
  select() { }
}

```

在上面的例子里，`SelectableControl`包含了`Control`的所有成员，包括私有成员`state`。 因为`state`是私有成员，所以只能够是`Control`的子类们才能实现`SelectableControl`接口。 因为只有`Control`的子类才能够拥有一个声明于`Control`的私有成员`state`，这对私有成员的兼容性是必需的。但`Image`类并不是这样的。

## 混合类型

接口可以混合使用函数类型、构造器类型、索引签名等。这时它的对象可以同时做为函数和对象使用：

```typescript
interface Counter {
  (start: number): string;
  interval: number;
  reset(): void;
}

function getCounter(): Counter {
  let counter = <Counter>function (start: number) { };
  counter.interval = 123;
  counter.reset = function () { };
  return counter;
}

let c = getCounter();
c(10);
c.reset();
c.interval = 5.0;

```

# 类

## 类的定义

```typescript
class Greeter {
  greeting: string; //属性声明可以省略
  
  constructor(message: string) {
    this.greeting = message; 
  }

  greet() { //方法定义不需要function关键字
    return "Hello, " + this.greeting; //要引用对象其他成员要使用this关键字
  }
}
```

## 创建对象

### 使用`new`关键字

```typescript
let greeter = new Greeter("world");
```

### 使用对象字面量

```typescript
let greeter = {
  greeting: "world",
  greet: function() {
    return "Hello, " + this.greeting;
  }
};
```

## 继承和多态

```typescript
class Animal {
  name:string;
  constructor(theName: string) { this.name = theName; }
  move(distanceInMeters: number = 0) {
    console.log(`${this.name} moved ${distanceInMeters}m.`);
  }
}

class Snake extends Animal {
  constructor(name: string) { super(name); }
  move(distanceInMeters = 5) {
    console.log("Slithering...");
    super.move(distanceInMeters);
  }
}

class Horse extends Animal {
  constructor(name: string) { super(name); }
  move(distanceInMeters = 45) {
    console.log("Galloping...");
    super.move(distanceInMeters);
  }
}

let sam = new Snake("Sammy the Python");
let tom: Animal = new Horse("Tommy the Palomino");
sam.move();
tom.move(34);
```

输出：

```bash
Slithering...
Sammy the Python moved 5m.
Galloping...
Tommy the Palomino moved 34m.
```

### Object

```typescript
let myData = new Object();
myData.name = "Adam";
myData.weather = "sunny";
console.log("Hello " + myData.name + ".");
console.log("Today is " + myData.weather + ".");
```



## 实现接口

```typescript
interface ClockInterface {
  currentTime: Date;
  setTime(d: Date);
}

class Clock implements ClockInterface {
  currentTime: Date;
  setTime(d: Date) {
    this.currentTime = d;
  }
  constructor(h: number, m: number) { }
}
```

## 访问修饰符

注意：接口成员不能使用访问修饰符。

| 访问修饰符  | 说明   |
| ----------- | ------ |
| `public`    | 缺省。 |
| `private`   |        |
| `protected` |        |

`protected`的成员可以在所属的类内部访问，还可以在子类内部访问。

构造函数也可以被标记成`protected`。 这意味着这个类不能在包含它的类外被实例化，但是能被继承。比如，

```typescript
class Person {
  protected name: string;
  protected constructor(theName: string) { this.name = theName; }
}

// Employee can extend Person
class Employee extends Person {
  private department: string;
  constructor(name: string, department: string) {
    super(name);
    this.department = department;
  }
  public getElevatorPitch() {
    return Hello, my name is ${this.name} and I work in ${this.department}.;
  }
}

let howard = new Employee("Howard", "Sales");
let john = new Person("John"); // Error: The 'Person' constructor is protected

```

## 参数属性

参数属性是在构造器中声明的，**显式**带有访问修饰符的参数（在这种情况，`public`访问修饰符也不能省略）。参数属性会自动在类中创建一个相同的属性，并将值赋值给它：

```typescript
class Animal {
  constructor(private name: string) { }
  move(distanceInMeters: number) {
    console.log(${this.name} moved ${distanceInMeters}m.);
  }
}

```

相当于：

```typescript
class Animal {
  private name: string;
  constructor(theName: string) { this.name = theName; }
  move(distanceInMeters: number) {
    console.log(`${this.name} moved ${distanceInMeters}m.`);
  }
}

```

## 存取器

```typescript
let passcode = "secret passcode";

class Employee {
  private _fullName: string;
  get fullName(): string {
    return this._fullName;
  }
  set fullName(newName: string) {
    if (passcode && passcode == "secret passcode") {
      this._fullName = newName;
    } else {
      console.log("Error: Unauthorized update of employee!");
    }
  }
}

let employee = new Employee();
employee.fullName = "Bob Smith";
if (employee.fullName) {
  alert(employee.fullName);
}

```

存取器要求你将编译器设置为输出ECMAScript 5或更高。 不支持降级到ECMAScript 3。 其次，只带有`get`不带有`set`的存取器自动被推断为`readonly`。

## 静态属性

静态属性属于类而不属于实例：

```typescript
class Grid {
  static origin = {x: 0, y: 0};
  calculateDistanceFromOrigin(point: {x: number; y: number;}) {
    let xDist = (point.x - Grid.origin.x);
    let yDist = (point.y - Grid.origin.y);
    return Math.sqrt(xDist * xDist + yDist * yDist) / this.scale;
  }
  constructor (public scale: number) { }
}

```

注意：接口不能声明静态属性。

## 抽象类

抽象类做为其它派生类的基类使用。 它们一般不会直接被实例化。 不同于接口，抽象类可以包含成员的实现细节。

抽象类中的抽象方法不包含具体实现并且必须在派生类中实现。 抽象方法的语法与接口方法相似。 两者都是定义方法签名但不包含方法体。 然而，抽象方法必须包含`abstract`关键字并且可以包含访问修饰符。

```typescript
abstract class Animal {
  abstract makeSound(): void;
  move(): void {
    console.log('roaming the earch...');
  }
}

```

## `this`

`this`的含义取决于函数的调用形式，以及是否是严格模式。例外情况是在箭头函数中

### `apply`或`call`调用

```typescript
function hello(thing) {  
  console.log(this + " says hello " + thing);
}

hello.call("Yehuda", "world") //=> Yehuda says hello world  

```

`this`引用`call`方法的第一个参数。

### 函数调用

```typescript
function hello(thing) {  
  console.log("Hello " + thing);
}

hello("world")  // 相当于：hello.call(window, "world");

```

 如果ES5使用严格模式，则相当于：`hello.call(undefined, "world");`。

对于内联函数也是一样的：`(function() {})();`。相当于：`(function() {}).call(window);` 。ES5严格模式则为：`(function() {}).call(undefined);`。

### 方法调用

```typescript
function hello(thing) {  
  console.log(this + " says hello " + thing);
}

person = { name: "Brendan Eich" }  
person.hello = hello;
person.hello("world") // 相当于：person.hello.call(person, "world")
hello("world") // this为window或undefined 

```

### 构造器调用

`this`引用由该构造器创建的对象。

### 箭头函数

箭头函数在设计中使用的是Lexical this，即箭头函数能保存函数创建时的`this`值，而不是调用时的值。这是唯一一个`this`不由调用决定的场景。

```typescript
function printThis() {
  let print = () => console.log(this);
  print();
}

printThis.call([1]); // this引用[1]
printThis.call([2]); // this引用[2]

```

# 泛型

> 注意，无法创建泛型枚举和泛型命名空间。

## 泛型函数

```typescript
function identity<T>(arg: T): T {
  return arg;
}

let output = identity<string>("myString"); 

```

或者利用类型推断：

```typescript
let output = identity("myString");

```

泛型函数的类型：

```typescript
let myIdentity: <U>(arg: U) => U = identity;

```

注意：类型变量的名称可以与泛型函数中的不一样。

还可以使用匿名接口的方式来表示泛型函数的类型：

```typescript
let myIdentity: {<T>(arg: T): T} = identity;

```

或者

```typescript
interface GenericIdentityFn {
  <T>(arg: T): T;
}

let myIdentity: GenericIdentityFn = identity;

```

或者

```typescript
interface GenericIdentityFn<T> {
  (arg: T): T;
}

let myIdentity: GenericIdentityFn<number> = identity;

```

## 泛型变量

在使用类型变量（不带泛型约束）声明的泛型变量时，应该将它们当作是任意类型来使用，而不能使用特定类型的API：

```typescript
function loggingIdentity<T>(arg: T): T {
  console.log(arg.length);  // Error: T doesn't have .length
  return arg;
}

```

下面的例子则是正确的：

```typescript
function loggingIdentity<T>(arg: T[]): T[] {
  console.log(arg.length);  // Array has a .length, so no more error
  return arg;
}

```

## 泛型类

```typescript
class GenericNumber<T> {
  zeroValue: T;
  add: (x: T, y: T) => T;
}

let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function(x, y) { return x + y; };

```

注意：泛型类指的是实例部分的类型，所以类的静态属性不能使用这个泛型类型。

在TypeScript使用泛型创建工厂函数时，需要引用构造函数的类类型。比如，

```typescript
function create<T>(c: {new(): T; }): T {
  return new c();
}

```

一个更高级的例子，使用原型属性推断并约束构造函数与类实例的关系。

```typescript
class BeeKeeper {
  hasMask: boolean;
}

class ZooKeeper {
  nametag: string;
}

class Animal {
  numLegs: number;
}

class Bee extends Animal {
  keeper: BeeKeeper;
}

class Lion extends Animal {
  keeper: ZooKeeper;
}

function findKeeper<A extends Animal, K> (a: {new(): A; prototype: {keeper: K}}): K {
  return a.prototype.keeper;
}

findKeeper(Lion).nametag;  // typechecks!

```

## 泛型约束

```typescript
interface Lengthwise {
  length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
  console.log(arg.length);  // Now we know it has a .length property, so no more error
  return arg;
}

```

你可以声明一个类型参数，且它被另一个类型参数所约束。比如，

```typescript
function find<T, U extends Findable<T>>(n: T, s: U) {
  // ...
}

find (giraffe, myAnimals);

```



# 类型系统

## 类型转换

### 类型断言

类型断言好比其它语言里的类型转换，但是不进行特殊的数据检查和解构。 它没有运行时的影响，只是在编译阶段起作用。 TypeScript会假设你——程序员，已经进行了必要的检查。

类型断言有两种形式。 其一是“尖括号”语法：

```typescript
let someValue: any = "this is a string";
let strLength: number = (<string>someValue).length;

```

另一个为`as`断言：

```typescript
let someValue: any = "this is a string";
let strLength: number = (someValue as string).length;

```

两种形式是等价的。 至于使用哪个大多数情况下是凭个人喜好；然而，当你在TypeScript里使用JSX时，只有`as`断言是被允许的。

### 其他显式类型转换

#### 将数值转换为字符串

| 方法或函数        | 说明                                                         |
| ----------------- | ------------------------------------------------------------ |
| String()          |                                                              |
| .toString()       | 返回一个以十进制表示数值的字符串。                           |
| .toString(2)      | 返回一个以二进制表示数值的字符串。                           |
| .toString(8)      | 返回一个以八进制表示数值的字符串。                           |
| .toString(16)     | 返回一个以十六进制表示数值的字符串。                         |
| .toFixed(n)       | 返回一个精确到小数点n位实数的字符串。                        |
| .toExponential(n) | 返回一个采用科学记数法（小数点前有1位数字，小数点后有n位数字）表示数值的字符串。 |
| .toPrecision(n)   | 返回一个用n位有效数字表示数值（根据需要使用科学记数法）的字符串。 |

```typescript
let myData1 = (5).toString() + String(5);
```

#### 将字符串转换为数值

| 函数            | 说明                                                         |
| --------------- | ------------------------------------------------------------ |
| Number(str)     | 将字符串转换为一个整数或实数值。                             |
| parseInt(str)   | 将字符串转换为一个整数值，并且会忽略字符串末尾的非数字字符。 |
| parseFloat(str) | 将字符串转换为一个实数值，并且会忽略字符串末尾的非数字字符。 |



### 隐式类型转换

## 类型守卫（Type Guards）

类型守卫就是一些表达式，它们会在运行时检查变量是否是某个类型，如果满足，则确保在守卫范围里，该变量自动推断为该类型，而不需要再显式类型转换。

### 自定义类型守卫

自定义类型保护就是返回一个类型谓词的函数。

```typescript
interface Bird {
  fly();
  layEggs();
}

interface Fish {
  swim();
  layEggs();
}

function isFish(pet: Fish | Bird): pet is Fish { //pet为Fish时，函数体才会执行
  return (<Fish>pet).swim !== undefined;
}

if (isFish(pet)) {
  pet.swim();
} else {
  pet.fly();
}

```

`参数名 is 类型` 是类型谓词，`参数名`必须是来自于当前函数签名里的一个参数名。

注意：TypeScript不仅知道在`if`分支里`pet`是`Fish`类型； 它还清楚在`else`分支里，一定不是`Fish`类型，一定是`Bird`类型。

### `typeof`类型守卫

`typeof`类型守卫只有两种形式能被识别：`typeof 表达式 === "类型名"`和`typeof 表达式 !== "类型名"`，`"类型名"`必须是`"number"`，`"string"`，`"boolean"`或`"symbol"`。 但是TypeScript并不会阻止你与其它字符串比较，语言不会把那些表达式识别为类型守卫。

```typescript
function padLeft(value: string, padding: string | number) {
  if (typeof padding === "number") {
    return Array(padding + 1).join(" ") + value;
  }

  if (typeof padding === "string") {
    return padding + value;
  }

  throw new Error(Expected string or number, got '${padding}'.);
}

```

### `instanceof`类型守卫

```typescript
interface Padder {
  getPaddingString(): string
}

class SpaceRepeatingPadder implements Padder {
  constructor(private numSpaces: number) { }

  getPaddingString() {
    return Array(this.numSpaces + 1).join(" ");
  }
}

class StringPadder implements Padder {
  constructor(private value: string) { }

  getPaddingString() {
    return this.value;
  }
}

function getRandomPadder() {
  return Math.random() < 0.5 ?
    new SpaceRepeatingPadder(4) :
  	new StringPadder("  ");
}

// 类型为SpaceRepeatingPadder | StringPadder
let padder: Padder = getRandomPadder();

if (padder instanceof SpaceRepeatingPadder) {
  padder; // 类型细化为'SpaceRepeatingPadder'
}

if (padder instanceof StringPadder) {
  padder; // 类型细化为'StringPadder'
}

```

`instanceof`的右侧要求是一个构造函数。

## 类型兼容——结构性子类型

TypeScript使用的是结构性类型系统。 当我们比较两种不同的类型时，并不在乎它们从何处而来，如果所有成员的类型都是兼容的，我们就认为它们的类型是兼容的。

```typescript
function printLabel(labelledObj: { label: string }) {
  console.log(labelledObj.label);
}

let myObj = { size: 10, label: "Size 10 Object" };
printLabel(myObj);

let x: {label:string};
x = myObj;

```

### 含`private`或`protected`成员的类型兼容

当我们比较带有`private`或`protected`成员的类型的时候，情况就不同了。 如果其中一个类型里包含一个`private`成员，那么只有当另外一个类型中也存在这样一个`private`成员， 并且它们都是来自同一处声明时（例如，有继承关系），我们才认为这两个类型是兼容的。 对于`protected`成员也使用这个规则。

```typescript
class Animal {
    private name: string;
    constructor(theName: string) { this.name = theName; }
}

class Rhino extends Animal {
    constructor() { super("Rhino"); }
}

class Employee {
    private name: string;
    constructor(theName: string) { this.name = theName; }
}

let animal = new Animal("Goat");
let rhino = new Rhino();
let employee = new Employee("Bob");

animal = rhino;
animal = employee; // Error: Animal and Employee are not compatible

```

尽管Employee里也有一个私有成员name，但它明显不是Animal里面定义的那个。

### 函数类型的兼容

参数个数不一样：

```typescript
let x = (a: number) => 0;
let y = (b: number, s: string) => 0;

y = x; // OK
x = y; // Error

```

返回类型不一样：

```typescript
let x = () => ({name: 'Alice'});
let y = () => ({name: 'Alice', location: 'Seattle'});

x = y; // OK
y = x; // Error because x() lacks a location property

```

类型系统强制源函数的返回值类型必须是目标函数返回值类型的子类型。

函数重载：

对于有重载的函数，源函数的每个重载都要在目标函数上找到对应的函数签名。 这确保了目标函数可以在所有源函数可调用的地方调用。

### 枚举类型的兼容

枚举类型与数值类型兼容，并且数值类型与枚举类型兼容。不同枚举类型之间是不兼容的。

### 类的兼容

类有静态部分和实例部分的类型。 比较两个类类型的对象时，只有实例的成员会被比较。 静态成员和构造函数不在比较的范围内。

### 泛型的兼容

```typescript
interface Empty<T> {}

let x: Empty<number>;
let y: Empty<string>;

x = y;  // okay, y matches structure of x

```

上面代码里，`x`和`y`是兼容的，因为它们的结构是相同，即使类型参数不一样。这里类型参数并不影响类型的结构。

```typescript
interface NotEmpty<T> {
  data: T;
}

let x: NotEmpty<number>;
let y: NotEmpty<string>;

x = y;  // error, x and y are not compatible

```

本例中，类型参数会影响类型的结构。因此，`x`与`y`不兼容。

对于没指定泛型类型的泛型参数时，会把所有泛型参数当成`any`比较。 然后用结果类型进行比较，就像上面第一个例子。

比如：

```typescript
let identity = function<T>(x: T): T {
	// ...
}

let reverse = function<U>(y: U): U {
  // ...
}

identity = reverse;  // Okay because (x: any)=>any matches (y: any)=>any

```

## 类型推断

## 类型别名

```typescript
type Name = string;
type NameResolver = () => string;
type NameOrResolver = Name | NameResolver;

function getName(n: NameOrResolver): Name {
  if (typeof n === 'string') {
    return n;
  } else {
    return n();
  }
}

```

声明类型别名不会新建一个类型，只是创建了一个新名字来引用那个类型。

类型别名不能被`extends`和`implements`，即使类型别名引用的是一个接口。

类型别名也不能`extends`和`implements`其它类型。 

类型别名也可以是泛型：

```typescript
type Container<T> = { value: T };

```

类型别名能在右侧的属性中引用它自己：

```typescript
type LinkedList<T> = T & { next: LinkedList<T> };

interface Person {
  name: string;
}

var people: LinkedList<Person> = …;
var s = people.name;
var s = people.next.name;
var s = people.next.next.name;
var s = people.next.next.next.name;

```

除此之外，类型别名不能出现在右侧的任何地方：

```typescript
type Yikes = Array<Yikes>; // error

```

## 外部类型

外部类型允许我们为现有的JavaScript类库提供额外的类型定义。通过这种方式来给编译器提供提示。

<https://github.com/DefinitelyTyped/DefinitelyTyped> 预定义了许多外部类型。另外还有一个叫作`typings`的工具可以用来维护这些定义。可以用npm来安装它：

```bash
$ npm install -g typings

```

typings的配置文件定义在`typings.json`，默认情况，所有安装的外部类型都放在`./typings`目录中。

```bash
$ typings init

```

创建基础配置的`typings.json`文件。

```bash
$ typings install angularjs --ambient --save

```

安装angularjs 1.x的外部类型定义。

`--save`表示在`typings.json`中添加安装的外部类型记录。

# 高级类型

## 交集类型（Intersection Types）

交集类型是将多个类型合并为一个类型。 这让我们可以把现有的多种类型叠加到一起成为一种类型，它包含了叠加的所有类型的特性。 例如，`Person & Serializable & Loggable`同时是`Person`和`Serializable`和`Loggable`。 就是说这个类型的对象同时拥有了这三种类型的所有成员。（可以理解为交集类型`Person & Serializable & Loggable`同时是`Person`、`Serializable`和`Loggable`的子类型）

```typescript
function extend<T, U>(first: T, second: U): T & U {
  let result = <T & U>{};

  for (let id in first) {
    (<any>result)[id] = (<any>first)[id];
  }

  for (let id in second) {
    if (!result.hasOwnProperty(id)) {
      (<any>result)[id] = (<any>second)[id];
    }
  }

  return result;
}

class Person {
  constructor(public name: string) { }
}

interface Loggable {
  log(): void;
}

class ConsoleLogger implements Loggable {
  log() {
    // ...
  }
}

var jim = extend(new Person("Jim"), new ConsoleLogger());
var n = jim.name;
jim.log();

```

## 联合类型（Union Types）

联合类型表示一个值可以是几种类型之一。 我们用竖线`|`分隔每个类型，所以`number | string | boolean`表示一个值可以是`number`，`string`，或`boolean`。（可以理解为联合类型`number | string | boolean`是类型`number`、`string`和`boolean`的父类型）

如果一个值是联合类型，我们只能访问此联合类型的所有类型里共有的成员，除非我们使用类型断言或类型保护。

```typescript
interface Bird {
  fly();
  layEggs();
}

interface Fish {
  swim();
  layEggs();
}

function getSmallPet(): Fish | Bird {
  // ...
}

let pet = getSmallPet();
pet.layEggs(); // okay
pet.swim();    // errors

// 仍然会报错
if (pet.swim) {
  pet.swim();
}

// 使用了类型断言则不会报错
if ((<Fish>pet).swim) {
  (<Fish>pet).swim();
}

```

## 可辨识联合（Discriminated Unions）

结合字符串字面量类型，联合类型，类型检查和类型别名可创建一个叫做可辨识联合，它也称做标签联合或代数数据类型。

```typescript
interface Square {
  kind: "square";
  size: number;
}

interface Rectangle {
  kind: "rectangle";
  width: number;
  height: number;
}

interface Circle {
  kind: "circle";
  radius: number;
}

type Shape = Square | Rectangle | Circle;

function area(s: Shape) {
  switch (s.kind) {
    case "square": return s.size * s.size;
    case "rectangle": return s.height * s.width;
    case "circle": return Math.PI * s.radius ** 2;
  }
}

```

#### 完整性检查

在switch语句中，如果希望对是否涵盖可辨识联合的所有取值进行检查，有两种方法：

1、启用`--strictNullChecks`并且指定一个返回值类型

```typescript
type Shape = Square | Rectangle | Circle | Triangle;

function area(s: Shape): number { // error: returns number | undefined
  switch (s.kind) {
    case "square": return s.size * s.size;
    case "rectangle": return s.height * s.width;
    case "circle": return Math.PI * s.radius ** 2;
  }
}

```

2、使用`never`类型，在编译时就可以进行完整性检查

```typescript
function assertNever(x: never): never { //需要这个额外函数
  throw new Error("Unexpected object: " + x);
}

function area(s: Shape) {
  switch (s.kind) {
    case "square": return s.size * s.size;
    case "rectangle": return s.height * s.width;
    case "circle": return Math.PI * s.radius ** 2;
    default: return assertNever(s); // error here if there are missing cases
  }
}

```

# 命名空间

命名空间是位于全局命名空间下的一个普通的带有名字的JavaScript对象。 

命名空间的作用与模块类似。

模块里最好不要嵌套命名空间（即导出命名空间）。

```typescript
namespace Validation {
  export interface StringValidator {
    isAcceptable(s: string): boolean;
  }

  const lettersRegexp = /^[A-Za-z]+$/; // 不导出，命名空间之外无法访问。
  const numberRegexp = /^[0-9]+$/;

  export class LettersOnlyValidator implements StringValidator {
    isAcceptable(s: string) {
      return lettersRegexp.test(s);
    }
  }

  export class ZipCodeValidator implements StringValidator {
    isAcceptable(s: string) {
      return s.length === 5 && numberRegexp.test(s);
    }
  }
}

// Some samples to try
let strings = ["Hello", "98052", "101"];

// Validators to use
let validators: { [s: string]: Validation.StringValidator; } = {};
validators["ZIP code"] = new Validation.ZipCodeValidator();
validators["Letters only"] = new Validation.LettersOnlyValidator();

// Show whether each string passed each validator
strings.forEach(s => {
  for (let name in validators) {
    console.log(`"${ s }" - ${ validators[name].isAcceptable(s) ? "matches" : "does not match" } ${ name }`);
  }
});

```

## 分离到多个文件

当应用变得越来越大时，我们可以将一个命名空间的代码分割到多个文件中。尽管是不同的文件，它们仍是同一个命名空间，并且在使用的时候就如同它们在一个文件中定义的一样。不过要通过编译指令来告诉编译器这些文件之间的关联（而不是使用导入声明）。

Validation.ts：

```typescript
namespace Validation {
  export interface StringValidator {
    isAcceptable(s: string): boolean;
  }
}

```

LettersOnlyValidator.ts

```typescript
/// <reference path="Validation.ts" />
namespace Validation {
  const lettersRegexp = /^[A-Za-z]+$/;
  export class LettersOnlyValidator implements StringValidator {
    isAcceptable(s: string) {
      return lettersRegexp.test(s);
    }
  }
}

```

ZipCodeValidator.ts

```typescript
/// <reference path="Validation.ts" />
namespace Validation {
  const numberRegexp = /^[0-9]+$/;
  export class ZipCodeValidator implements StringValidator {
    isAcceptable(s: string) {
      return s.length === 5 && numberRegexp.test(s);
    }
  }
}

```

Test.ts：

```typescript
/// <reference path="Validation.ts" />
/// <reference path="LettersOnlyValidator.ts" />
/// <reference path="ZipCodeValidator.ts" />

// Some samples to try
let strings = ["Hello", "98052", "101"];

// Validators to use
let validators: { [s: string]: Validation.StringValidator; } = {};
validators["ZIP code"] = new Validation.ZipCodeValidator();
validators["Letters only"] = new Validation.LettersOnlyValidator();

// Show whether each string passed each validator
strings.forEach(s => {
  for (let name in validators) {
    console.log(""" + s + "" " + (validators[name].isAcceptable(s) ? " matches " : " does not match ") + name);
  }
});

```

当涉及到多文件时，我们必须确保所有编译后的代码都被加载了。 我们有两种方式。

第一种方式，把所有的输入文件编译为一个输出文件，需要使用--outFile标记：

```bash
$ tsc --outFile sample.js Test.ts

```

编译器会根据源码里的编译指令自动地编译依赖文件，并对输出按正确的顺序进行排序。

第二种方式，我们可以编译每一个文件（默认方式），那么每个源文件都会对应生成一个JavaScript文件。 然后，在页面上通过`<script>`标签把所有生成的JavaScript文件按正确的顺序引进来，比如：

MyTestPage.html：

```html
<script src="Validation.js" type="text/javascript" />
<script src="LettersOnlyValidator.js" type="text/javascript" />
<script src="ZipCodeValidator.js" type="text/javascript" />
<script src="Test.js" type="text/javascript" />

```

## 外部命名空间

```typescript
declare namespace D3 {
  export interface Selectors {
    select: {
      (selector: string): Selection;
      (element: EventTarget): Selection;
    };
  }
  export interface Event {
    x: number;
    y: number;
  }
  export interface Base extends Selectors {
    event: Event;
  }
}

declare let d3: D3.Base;

```



# 模块

JavaScript模块用于管理Web应用程序中的依赖项，这意味着您无需管理大量单独的代码文件，以确保浏览器下载应用程序的所有代码。相反，在编译过程中，应用程序所需的所有JavaScript文件都组合成一个更大的文件，称为bundle，这就是浏览器所要下载的。

TypeScript与ECMAScript 2015一样，任何TypeScript或JavaScript文件都被当成一个模块。

定义在一个模块里的变量，函数，类等等在模块外部是不可见的，除非你明确地使用export导出它们。 相反，如果想使用其它模块导出的变量，函数，类，接口等的时候，你必须要导入它们。

## 导出

### 导出声明

任何声明（比如变量，函数，类，类型别名或接口）都能够通过添加`export`关键字来导出。

```typescript
export interface StringValidator {
  isAcceptable(s: string): boolean;
}

export const numberRegexp = /^[0-9]+$/;

```

### 导出语句

导出语句要放在相关的声明之后，通常放在最后。

```typescript
class ZipCodeValidator implements StringValidator {
  isAcceptable(s: string) {
    return s.length === 5 && numberRegexp.test(s);
  }
}

export { ZipCodeValidator };

```

可以一次导出多个名字：

```typescript
export {square, log10, PI};

```

可以对导出的名字重命名：

```typescript
export { ZipCodeValidator as mainValidator };

```

重新导出：

```typescript
export {ZipCodeValidator as RegExpBasedZipCodeValidator} from "./ZipCodeValidator";
export * from "./StringValidator";  //重新导出所有名字

```

重新导出功能并不会在当前模块导入那个模块或定义一个新的局部变量。

### 默认导出

每个模块都可以有一个default导出。 默认导出使用default关键字标记；并且一个模块只能够有一个default导出。

ZipCodeValidator.ts：

```typescript
export default class ZipCodeValidator {
  static numberRegexp = /^[0-9]+$/;
  isAcceptable(s: string) {
    return s.length === 5 && ZipCodeValidator.numberRegexp.test(s);
  }
}

```

导入default导出时，import后不加花括号：

```typescript
import validator from "./ZipCodeValidator";

let myValidator = new validator();

```

默认导出的类和函数的名字可以与导入时不一样。上面导入默认导出时，validate是可以任意取的，而且不能使用花括号包围。如果要使用花括号包围，则应该使用default来作为默认导出的函数的名称，而不能使用它声明的validator：

```typescript
import {default as validator} from "./ZipCodeValidator";

```

甚至，默认导出时可以省略类和函数名：

StaticZipCodeValidator.ts：

```typescript
const numberRegexp = /^[0-9]+$/;
export default function (s: string) {
  return s.length === 5 && numberRegexp.test(s);
}

```

Test.ts：

```typescript
import validate from "./StaticZipCodeValidator";  
// 或者 import {default as validate} from "./StaticZipCodeValidator";  

let strings = ["Hello", "98052", "101"];

// Use function validate
strings.forEach(s => {
  console.log(`"${s}" ${validate(s) ? " matches" : " does not match"}`);
});

```

默认导出还可以导出一个值：

OneTwoThree.ts：

```typescript
export default "123";

```

Log.ts：

```typescript
import num from "./OneTwoThree";

console.log(num); // "123"

```

同时导入默认导出和其他导出：

math3.ts：

```typescript
export default function cube(x) {…}
export function square(x) {…}

```

app4.ts：

```typescript
import cube, {square} from './math3';

```

### CommonJS和AMD的导出

TypeScript模块支持`export =`语法以支持传统的CommonJS和AMD的工作流模型。

`export =`语法定义一个模块的导出对象。 它可以是类，接口，命名空间，函数或枚举。

ZipCodeValidator.ts：

```typescript
let numberRegexp = /^[0-9]+$/;

class ZipCodeValidator {
    isAcceptable(s: string) {
        return s.length === 5 && numberRegexp.test(s);
    }
}

export = ZipCodeValidator;

```

Test.ts：

```typescript
import zip = require("./ZipCodeValidator");

// Some samples to try
let strings = ["Hello", "98052", "101"];

// Validators to use
let validator = new zip();

// Show whether each string passed each validator
strings.forEach(s => {
  console.log(`"${ s }" - ${ validator.isAcceptable(s) ? "matches" : "does not match" }`);
});

```

## 导入

导入一个名字：

```typescript
import { ZipCodeValidator } from "./ZipCodeValidator";

```

导入多个名字：

```typescript
import {square, log10} from './math';

```

导入一个模块中所有名字：

```typescript
import * from './math';

```

### 重命名导入

```typescript
import { ZipCodeValidator as ZCV } from "./ZipCodeValidator";
import * as validator from "./ZipCodeValidator";

```

### 具有副作用的导入

尽管不推荐这么做，一些模块会设置一些全局状态供其它模块使用。 这些模块可能没有任何的导出或用户根本就不关注它的导出。 使用下面的方法来导入这类模块：

```typescript
import "./my-module.js";

```

### 导入默认导入

### 导入Common和AMD导出

若要导入一个使用了`export =`的模块时，必须使用TypeScript提供的特定语法`import … = require("module")`。

其实，使用导出声明或导出语句的模块，也可以使用这种方式来导入。

## 生成模块代码

根据编译时指定的模块目标参数（例如：`--module commonjs`），编译器会生成相应的供Node.js (CommonJS)，Require.js (AMD)，isomorphic (UMD), SystemJS或ECMAScript 2015 native modules (ES6)模块加载系统使用的代码。

SimpleModule.ts：

```typescript
import m = require("mod");

export let t = m.something + 1;

```

AMD / RequireJS SimpleModule.js：

```typescript
define(["require", "exports", "./mod"], function (require, exports, mod_1) {
  exports.t = mod_1.something + 1;
});

```

CommonJS / Node SimpleModule.js：

```typescript
let mod_1 = require("./mod");

exports.t = mod_1.something + 1;

```

UMD SimpleModule.js：

```typescript
(function (factory) {
  if (typeof module === "object" && typeof module.exports === "object") {
    let v = factory(require, exports); if (v !== undefined) module.exports = v;
  } else if (typeof define === "function" && define.amd) {
    define(["require", "exports", "./mod"], factory);
  }
})(function (require, exports) {
  let mod_1 = require("./mod");
  exports.t = mod_1.something + 1;
});

```

System SimpleModule.js：

```typescript
System.register(["./mod"], function(exports_1) {
  let mod_1;
  let t;
  return {
    setters:[
      function (mod_1_1) {
        mod_1 = mod_1_1;
      }],
    execute: function() {
      exports_1("t", t = mod_1.something + 1);
    }
  }
});

```

Native ECMAScript 2015 modules SimpleModule.js：

```typescript
import { something } from "./mod";

export let t = something + 1;

```

## 动态加载

通过`require`调用模块加载器，可以实现按条件动态加载模块，并且还不会损失类型安全（通过`typeof`）。

编译器会检测是否每个模块都会在生成的JavaScript中用到。 如果一个模块标识符只在类型注解部分使用，并且完全没有在表达式中使用时，就不会生成`require`这个模块的代码。

示例：Node.js里的动态模块加载

```typescript
declare function require(moduleName: string): any;

import { ZipCodeValidator as Zip } from "./ZipCodeValidator";

if (needZipValidation) {
  let ZipCodeValidator: typeof Zip = require("./ZipCodeValidator");
  let validator = new ZipCodeValidator();
  if (validator.isAcceptable("...")) { /* ... */ }
}

```

示例：require.js里的动态模块加载

```typescript
declare function require(moduleNames: string[], onLoad: (...args: any[]) => void): void;

import  * as Zip from "./ZipCodeValidator";

if (needZipValidation) {
  require(["./ZipCodeValidator"], (ZipCodeValidator: typeof Zip) => {
    let validator = new ZipCodeValidator.ZipCodeValidator();
    if (validator.isAcceptable("...")) { /* ... */ }
  });
}

```

示例：System.js里的动态模块加载

```typescript
declare const System: any;

import { ZipCodeValidator as Zip } from "./ZipCodeValidator";

if (needZipValidation) {
  System.import("./ZipCodeValidator").then((ZipCodeValidator: typeof Zip) => {
    var x = new ZipCodeValidator();
    if (x.isAcceptable("...")) { /* ... */ }
  });
}

```

## 外部模块

要想使用非TypeScript编写的JavaScript库，必须使用`declare`暴露这些库的API。

这些声明通常是在`.d.ts`文件中定义的。

例如：

node.d.ts：

```typescript
declare module "url" {
  export interface Url {
    protocol?: string;
    hostname?: string;
    pathname?: string;
  }
  export function parse(urlStr: string, parseQueryString?, slashesDenoteHost?): Url;
}

declare module "path" {
  export function normalize(p: string): string;
  export function join(...paths: any[]): string;
  export let sep: string;
}

```

现在我们可以使用编译指令`/// <reference>`来依赖该声明文件，并且使用`import`加载外部模块。

```typescript
/// <reference path="node.d.ts"/>
import * as URL from "url";

let myUrl = URL.parse("http://www.typescriptlang.org");

```

### 外部模块的简写

假如你不想在使用一个新外部模块之前花时间去编写声明，你可以采用声明的简写形式以便能够快速使用它。

```typescript
declarations.d.ts：

declare module "hot-new-module";

```

简写模块里所有导出的类型将是`any`。

### 模块声明通配符

某些模块加载器如SystemJS 和AMD支持导入非JavaScript内容。 它们通常会使用一个前缀或后缀来表示特殊的加载语法。 模块声明通配符可以用来表示这些情况。

```typescript
declare module "*!text" {
  const content: string;
  export default content;
}

// Some do it the other way around.
declare module "json!*" {
  const value: any;
  export default value;
}

```

现在你可以就导入匹配"*!text"或"json!*"的内容了。

```typescript
import fileContent from "./xyz.txt!text";
import data from "json!http://example.com/data.json";

console.log(data, fileContent);

```

### UMD模块

有些模块被设计成兼容多个模块加载器，或者不使用模块加载器（全局变量）。 它们以UMD或Isomorphic模块为代表。 这些库可以通过导入的形式或全局变量的形式访问。 

例如：

math-lib.d.ts：

```typescript
export const isPrime(x: number): boolean;

export as namespace mathLib;

```

之后，这个库可以在某个模块里通过导入来使用：

```typescript
import { isPrime } from "math-lib";

isPrime(2);
mathLib.isPrime(2); // ERROR: can't use the global definition from inside a module

```

它同样可以通过全局变量的形式使用，但只能在某个脚本里。 （脚本是指一个不带有导入或导出的文件。）

```typescript
mathLib.isPrime(2);

```

## 模块解析

共有两种可用的模块解析策略：`Node`和`Classic`。 你可以使用`--moduleResolution`标记指定使用哪种模块解析策略。 若未指定，那么在使用了--module AMD | System | ES2015时的默认值为`Classic`，其它情况时则为`Node`。

根据模块引用是相对的还是非相对的，模块解析策略会以不同的方式解析。

模块导入中，以`/`，`./`或`../`开头的是相对导入。例如：

```typescript
import Entry from "./components/Entry";
import { DefaultHeaders } from "../constants/http";
import "/mod";

```

所有其它形式的导入被当作非相对的。 例如：

```typescript
import * as $ from "jQuery";
import { Component } from "angular2/core";

```

相对导入解析时是相对于导入它的文件来的，并且不能解析为一个外部模块声明。 你应该为你自己写的模块使用相对导入，这样能确保它们在运行时的相对位置。

非相对模块的导入可以相对于`baseUrl`或通过下文会讲到的路径映射来进行解析。 它们还可以被解析能外部模块声明。 使用非相对路径来导入你的外部依赖。

### Classic解析策略

#### 相对导入的模块

相对导入的模块是相对于导入它的文件进行解析的。 因此`/root/src/folder/A.ts`文件里的`import { b } from "./moduleB"`会使用下面的查找流程：

- /root/src/folder/moduleB.ts
- /root/src/folder/moduleB.tsx
- /root/src/folder/moduleB.d.ts

#### 非相对导入的模块

非相对模块的导入，编译器则会从包含导入文件的目录开始依次向上级目录遍历。因此`/root/src/folder/A.ts`文件里的`import { b } from "moduleB"`会使用下面的查找流程：

- /root/src/folder/moduleB.ts
- /root/src/folder/moduleB.tsx
- /root/src/folder/moduleB.d.ts
- /root/src/moduleB.ts
- /root/src/moduleB.tsx
- /root/src/moduleB.d.ts
- /root/moduleB.ts
- /root/moduleB.tsx
- /root/moduleB.d.ts
- /moduleB.ts
- /moduleB.tsx
- /moduleB.d.ts

### Node解析策略

#### 相对导入的模块

比如，有一个导入语句`import { b } from "./moduleB"`在`/root/src/moduleA.ts`里，会以下面的流程来定位`"./moduleB"`：

- /root/src/moduleB.ts
- /root/src/moduleB.tsx
- /root/src/moduleB.d.ts
- /root/src/moduleB/package.json (如果指定了"typings"属性)
- /root/src/moduleB/index.ts
- /root/src/moduleB/index.tsx
- /root/src/moduleB/index.d.ts

#### 非相对导入的模块

假设`/root/src/moduleA.js`里使用的是非相对路径导入`var x = require("moduleB");`。会以下面的顺序去解析`moduleB`：

- /root/src/node_modules/moduleB.ts
- /root/src/node_modules/moduleB.tsx
- /root/src/node_modules/moduleB.d.ts
- /root/src/node_modules/moduleB/package.json (如果指定了"typings"属性)
- /root/src/node_modules/moduleB/index.ts
- /root/src/node_modules/moduleB/index.tsx
- /root/src/node_modules/moduleB/index.d.ts 
- /root/node_modules/moduleB.ts
- /root/node_modules/moduleB.tsx
- /root/node_modules/moduleB.d.ts
- /root/node_modules/moduleB/package.json (如果指定了"typings"属性)
- /root/node_modules/moduleB/index.ts
- /root/node_modules/moduleB/index.tsx
- /root/node_modules/moduleB/index.d.ts 
- /node_modules/moduleB.ts
- /node_modules/moduleB.tsx
- /node_modules/moduleB.d.ts
- /node_modules/moduleB/package.json (如果指定了"typings"属性)
- /node_modules/moduleB/index.ts
- /node_modules/moduleB/index.tsx
- /node_modules/moduleB/index.d.ts

### 额外的模块解析标记

#### Base URL

#### 路径映射

#### 利用`rootDirs`指定虚拟目录

### 跟踪模块解析——`--traceResolution`

# 装饰器

ES2015中，装饰器只能装饰类、属性、方法、存取器，而TypeScript还可以装饰函数、参数。