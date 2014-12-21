# ECMAScript 6 <sup>[中文版](https://github.com/techird/es6features/blob/master/README.ZH_CN.md)</sup>

## 简介

ECMAScript 6 是 ECMAScript 的下一代标准。该标准希望在 2015 年 6 月通过批准。ES6 对 ESMAScript 来说有重要的意义，并且是从 2009 年发布 ES5 标准之后的第一次更新。支持 ES6 新特性的 Javascript 引擎已经在[开发中](http://kangax.github.io/es5-compat-table/es6/)。

可以参考完整的[ES6标准草案](https://people.mozilla.org/~jorendorff/es6-draft.html)。

ES6 包括下列新特性：
- [arrows](#arrows)
- [classes](#classes)
- [enhanced object literals](#enhanced-object-literals)
- [template strings](#template-strings)
- [destructuring](#destructuring)
- [default + rest + spread](#default--rest--spread)
- [let + const](#let--const)
- [iterators + for..of](#iterators--forof)
- [generators](#generators)
- [comprehensions](#comprehensions)
- [unicode](#unicode)
- [modules](#modules)
- [module loaders](#module-loaders)
- [map + set + weakmap + weakset](#map--set--weakmap--weakset)
- [proxies](#proxies)
- [symbols](#symbols)
- [subclassable built-ins](#subclassable-built-ins)
- [promises](#promises)
- [math + number + string + object APIs](#math--number--string--object-apis)
- [binary and octal literals](#binary-and-octal-literals)
- [reflect api](#reflect-api)
- [tail calls](#tail-calls)

## ECMAScript 6 特性

### 推导语法

推导语法使用 `=>` 符号来表示一个函数。该语法类似于 C#、Java8 和 CoffeeScript 的用法。推导语法支持简单的表达式以及语句块。跟普通函数不一样，推导语法中的 `this` 跟推导语法的上下文是一致的。

```JavaScript
// 表达式形式
var odds = evens.map(v => v + 1);
var nums = evens.map((v, i) => v + i);

// 语句块形式
nums.forEach(v => {
  if (v % 5 === 0)
    fives.push(v);
});

// 使用上下文 this
var bob = {
  _name: "Bob",
  _friends: [],
  printFriends() {
    this._friends.forEach(f =>
      console.log(this._name + " knows " + f));
  }
}
```

### 类

ES6 里的类是基于 prototype 模式的面向对象语法的语法糖。使用独立并且方便的定义方式使得类定义更加易用和互通。类支持基于 prototype 的继承、调用父类函数、定义实例方法、静态方法以及构造函数。

```JavaScript
class Animal {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  },
  
  speak() {
    // 字符串模板请参考后文
    return `My name is ${this.name}, aged ${this.age}. `;
  }
}

class Dog extends Animal {
  constructor(name, age, color) {
    super(name, age);
    this.color = color;
  },
  
  speak() {
    return super() + `A little ${this.color} dog`;
  },
  
  static create() {
    return new Dog('Tom', 1, 'white');
  }
}

console.log(Dog.create().speak());

```

### 增强的字面量写法

增强的字面量对象：

* 支持在创建时指定对象的原型
* 支持 `foo: foo` 的简写
* 支持定义方法并且调用父类
* 支持动态属性名
 
这些特性使得字面量定义和类定义的方式更加一致，并且使基于对象的设计更加方便。

```JavaScript
var obj = {
    // __proto__ 指向对象的原型
    __proto__: theProtoObj,
    //‘handler: handler’ 的简写
    handler,
    // 对象上的方法
    toString() {
     // 调用原型对象上的同名方法
     return "d " + super.toString();
    },
    // 动态属性名
    [ 'prop_' + (() => 42)() ]: 42
};
```

### 字符串模板
Template strings provide syntactic sugar for constructing strings.  This is similar to string interpolation features in Perl, Python and more.  Optionally, a tag can be added to allow the string construction to be customized, avoiding injection attacks or constructing higher level data structures from string contents.

字符串模板是一个构建字符串的语法糖，跟 Perl、Python 等语言的字符串模板类似。使用 TODO

```JavaScript
// 基本使用
`In JavaScript '\n' is a line-feed.`

// 多行字符串
`In JavaScript this is
 not legal.`

// Construct a DOM query
var name = "Bob", time = "today";
`Hello ${name}, how are you ${time}?`

// Construct an HTTP request prefix is used to interpret the replacements and construction
GET`http://foo.org/bar?a=${a}&b=${b}
    Content-Type: application/json
    X-Credentials: ${credentials}
    { "foo": ${foo},
      "bar": ${bar}}`(myOnReadyStateChangeHandler);
```

### 解构

解构使用模式匹配来查找对象或数组的值。解构操作在匹配失败时不会报错，跟标准的对象属性查找 `foo["bar"]` 一样，在查找失败是返回 `undefined`。

```JavaScript
// 数组匹配
var [a, , b] = [1,2,3];

// 对象匹配
var { op: a, lhs: { op: b }, rhs: c }
       = getASTNode()

// 快捷方式
// 在作用域中绑定 `op`, `lhs` 和 `rhs`
var {op, lhs, rhs} = getASTNode()

// 还可以在参数中使用
function g({name: x}) {
  console.log(x);
}
g({name: 5})

// 解构失败
var [a] = [];
a === undefined;

// 解构失败使用默认值
var [a = 1] = [];
a === 1;
```

### 函数参数增强（默认值、剩余参数、参数展开）
定义参数时允许定义默认值。调用函数时允许把数组作为连续的参数传入。允许把剩余的参数转换为数组。剩余参数代替了 `arguments` 的需求，并且在大多数场合下更加直接。

```JavaScript
function f(x, y = 12) {
  // y 的值是 12 如果没有传入 y 参数（或者传入 undefined）
  return x + y;
}
f(3) == 15
```
```JavaScript
function f(x, ...y) {
  // y 是一个数组
  return x * y.length;
}
f(3, "hello", true) == 6
```
```JavaScript
function f(x, y, z) {
  return x + y + z;
}
// 把数组的元素作为参数调用，相当于 f(1, 2, 3) 和 f.apply(nul, [1, 2, 3]
f(...[1,2,3]) == 6
```

### Let 和 Const 关键字
允许使用 `let` 和 `const` 关键字来定义块级变量和常量。使用 `const` 关键字定义常量，常量在定义之前，不允许使用。


```JavaScript
function f() {
  {
    let x;
    {
      // OK 块级常量
      const x = "sneaky";
      // 错误，x 是常量，不允许辅助
      x = "foo";
    }
    // 错误，块内已定义 x
    let x = "inner";
  }
}
```

### 迭代器和 For..Of

迭代器允许定义自定义的迭代操作（跟 CLR 中的 IEnumerable 或 Java 中的 Iteratable类似）。`for..in` 操作和自定义迭代操作可以统一使用 `for..of` 来操作。迭代操作可以不用真正产生一个数组，允许像 LINQ 一样的 LAZY 设计（在迭代时才真正计算）。

```JavaScript
let fibonacci = {
  [Symbol.iterator]() {
    let pre = 0, cur = 1;
    return {
      next() {
        [pre, cur] = [cur, pre + cur];
        return { done: false, value: cur }
      }
    }
  }
}

for (var n of fibonacci) {
  // 在 1000 处结束
  if (n > 1000)
    break;
  print(n);
}
```

迭代器的定义基于“鸭子类型”的接口检测机制，下面的描述是基于[TypeScripty](http://typescriptlang.org) 的（只是基于，ES6 并不支持 interface 定义）。
```TypeScript
interface IteratorResult {
  done: boolean;
  value: any;
}
interface Iterator {
  next(): IteratorResult;
}
interface Iterable {
  [Symbol.iterator](): Iterator
}
```

### Generators

Generator 使用 `function*` 和 `yield` 关键字来简化迭代器的编写。一个使用 `function*` 定义的函数返回一个 Generator 的实力。Generator 是迭代器的子类型，包含额外的 `next` 和 `throw` 方法。这些方法允许把值回传到 Generator 中，而 `yield` 则是接收回传值（或扔出的异常）的一个表达式。

备注：Generator 同样可以用于 `await` 模式的异步编程，请参照 ES7 中的 `await` 提案。

> 译者注：Generator 还有很多丰富的特性，并不只是在迭代器使用。请参照 [function*]( https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*) 的资料。

```JavaScript
var fibonacci = {
  [Symbol.iterator]: function*() {
    var pre = 0, cur = 1;
    for (;;) {
      var temp = pre;
      pre = cur;
      cur += temp;
      yield cur;
    }
  }
}

for (var n of fibonacci) {
  // truncate the sequence at 1000
  if (n > 1000)
    break;
  print(n);
}
```

Generator 的接口如下 (使用 [TypeScript](http://typescriptlang.org) 语法来解释):

```TypeScript
interface Generator extends Iterator {
    next(value?: any): IteratorResult;
    throw(exception: any);
}
```

### Comprehensions
Array and generator comprehensions provide simple declarative list processing similar as used in many functional programming patterns.

```JavaScript
// Array comprehensions
var results = [
  for(c of customers)
    if (c.city == "Seattle")
      { name: c.name, age: c.age }
]

// Generator comprehensions
var results = (
  for(c of customers)
    if (c.city == "Seattle")
      { name: c.name, age: c.age }
)
```

### Unicode
Non-breaking additions to support full Unicode, including new unicode literal form in strings and new RegExp `u` mode to handle code points, as well as new APIs to process strings at the 21bit code points level.  These additions support building global apps in JavaScript.

```JavaScript
// same as ES5.1
"𠮷".length == 2

// new RegExp behaviour, opt-in ‘u’
"𠮷".match(/./u)[0].length == 2

// new form
"\u{20BB7}"=="𠮷"=="\uD842\uDFB7"

// new String ops
"𠮷".codePointAt(0) == 0x20BB7

// for-of iterates code points
for(var c of "𠮷") {
  console.log(c);
}
```

### Modules
Language-level support for modules for component definition.  Codifies patterns from popular JavaScript module loaders (AMD, CommonJS). Runtime behaviour defined by a host-defined default loader.  Implicitly async model – no code executes until requested modules are available and processed.

```JavaScript
// lib/math.js
export function sum(x, y) {
  return x + y;
}
export var pi = 3.141593;
```
```JavaScript
// app.js
module math from "lib/math";
alert("2π = " + math.sum(math.pi, math.pi));
```
```JavaScript
// otherApp.js
import {sum, pi} from "lib/math";
alert("2π = " + sum(pi, pi));
```

Some additional features include `export default` and `export *`:

```JavaScript
// lib/mathplusplus.js
export * from "lib/math";
export var e = 2.71828182846;
export default function(x) {
    return Math.exp(x);
}
```
```JavaScript
// app.js
module math from "lib/mathplusplus";
import exp from "lib/mathplusplus";
alert("2π = " + exp(math.pi, math.e));
```

### Module Loaders
Module loaders support:
- Dynamic loading
- State isolation
- Global namespace isolation
- Compilation hooks
- Nested virtualization

The default module loader can be configured, and new loaders can be constructed to evaluated and load code in isolated or constrained contexts.

```JavaScript
// Dynamic loading – ‘System’ is default loader
System.import('lib/math').then(function(m) {
  alert("2π = " + m.sum(m.pi, m.pi));
});

// Create execution sandboxes – new Loaders
var loader = new Loader({
  global: fixup(window) // replace ‘console.log’
});
loader.eval("console.log('hello world!');");

// Directly manipulate module cache
System.get('jquery');
System.set('jquery', Module({$: $})); // WARNING: not yet finalized
```

### Map + Set + WeakMap + WeakSet
Efficient data structures for common algorithms.  WeakMaps provides leak-free object-key’d side tables.

```JavaScript
// Sets
var s = new Set();
s.add("hello").add("goodbye").add("hello");
s.size === 2;
s.has("hello") === true;

// Maps
var m = new Map();
m.set("hello", 42);
m.set(s, 34);
m.get(s) == 34;

// Weak Maps
var wm = new WeakMap();
wm.set(s, { extra: 42 });
wm.size === undefined

// Weak Sets
var ws = new WeakSet();
ws.add({ data: 42 });
// Because the added object has no other references, it will not be held in the set
```

### Proxies
Proxies enable creation of objects with the full range of behaviors available to host objects.  Can be used for interception, object virtualization, logging/profiling, etc.

```JavaScript
// Proxying a normal object
var target = {};
var handler = {
  get: function (receiver, name) {
    return `Hello, ${name}!`;
  }
};

var p = new Proxy(target, handler);
p.world === 'Hello, world!';
```

```JavaScript
// Proxying a function object
var target = function () { return 'I am the target'; };
var handler = {
  apply: function (receiver, ...args) {
    return 'I am the proxy';
  }
};

var p = new Proxy(target, handler);
p() === 'I am the proxy';
```

There are traps available for all of the runtime-level meta-operations:

```JavaScript
var handler =
{
  get:...,
  set:...,
  has:...,
  deleteProperty:...,
  apply:...,
  construct:...,
  getOwnPropertyDescriptor:...,
  defineProperty:...,
  getPrototypeOf:...,
  setPrototypeOf:...,
  enumerate:...,
  ownKeys:...,
  preventExtensions:...,
  isExtensible:...
}
```

### Symbols
Symbols enable access control for object state.  Symbols allow properties to be keyed by either `string` (as in ES5) or `symbol`.  Symbols are a new primitive type. Optional `name` parameter used in debugging - but is not part of identity.  Symbols are unique (like gensym), but not private since they are exposed via reflection features like `Object.getOwnPropertySymbols`.


```JavaScript
(function() {

  // module scoped symbol
  var key = Symbol("key");

  function MyClass(privateData) {
    this[key] = privateData;
  }

  MyClass.prototype = {
    doStuff: function() {
      ... this[key] ...
    }
  };

})();

var c = new MyClass("hello")
c["key"] === undefined
```

### Subclassable Built-ins
In ES6, built-ins like `Array`, `Date` and DOM `Element`s can be subclassed.

Object construction for a function named `Ctor` now uses two-phases (both virtually dispatched):
- Call `Ctor[@@create]` to allocate the object, installing any special behavior
- Invoke constructor on new instance to initialize

The known `@@create` symbol is available via `Symbol.create`.  Built-ins now expose their `@@create` explicitly.

```JavaScript
// Pseudo-code of Array
class Array {
    constructor(...args) { /* ... */ }
    static [Symbol.create]() {
        // Install special [[DefineOwnProperty]]
        // to magically update 'length'
    }
}

// User code of Array subclass
class MyArray extends Array {
    constructor(...args) { super(...args); }
}

// Two-phase 'new':
// 1) Call @@create to allocate object
// 2) Invoke constructor on new instance
var arr = new MyArray();
arr[1] = 12;
arr.length == 2
```

### Math + Number + String + Object APIs
Many new library additions, including core Math libraries, Array conversion helpers, and Object.assign for copying.

```JavaScript
Number.EPSILON
Number.isInteger(Infinity) // false
Number.isNaN("NaN") // false

Math.acosh(3) // 1.762747174039086
Math.hypot(3, 4) // 5
Math.imul(Math.pow(2, 32) - 1, Math.pow(2, 32) - 2) // 2

"abcde".contains("cd") // true
"abc".repeat(3) // "abcabcabc"

Array.from(document.querySelectorAll('*')) // Returns a real Array
Array.of(1, 2, 3) // Similar to new Array(...), but without special one-arg behavior
[0, 0, 0].fill(7, 1) // [0,7,7]
[1,2,3].findIndex(x => x == 2) // 1
["a", "b", "c"].entries() // iterator [0, "a"], [1,"b"], [2,"c"]
["a", "b", "c"].keys() // iterator 0, 1, 2
["a", "b", "c"].values() // iterator "a", "b", "c"

Object.assign(Point, { origin: new Point(0,0) })
```

### Binary and Octal Literals
Two new numeric literal forms are added for binary (`b`) and octal (`o`).

```JavaScript
0b111110111 === 503 // true
0o767 === 503 // true
```

### Promises
Promises are a library for asynchronous programming.  Promises are a first class representation of a value that may be made available in the future.  Promises are used in many existing JavaScript libraries.

```JavaScript
function timeout(duration = 0) {
    return new Promise((resolve, reject) => {
        setTimeout(resolve, duration);
    })
}

var p = timeout(1000).then(() => {
    return timeout(2000);
}).then(() => {
    throw new Error("hmm");
}).catch(err => {
    return Promise.all([timeout(100), timeout(200)]);
})
```

### Reflect API
Full reflection API exposing the runtime-level meta-operations on objects.  This is effectively the inverse of the Proxy API, and allows making calls corresponding to the same meta-operations as the proxy traps.  Especially useful for implementing proxies.

```JavaScript
// No sample yet
```

### Tail Calls
Calls in tail-position are guaranteed to not grow the stack unboundedly.  Makes recursive algorithms safe in the face of unbounded inputs.

```JavaScript
function factorial(n, acc = 1) {
    'use strict';
    if (n <= 1) return acc;
    return factorial(n - 1, n * acc);
}

// Stack overflow in most implementations today,
// but safe on arbitrary inputs in eS6
factorial(100000)
```
