---
type: FrontEnd
title: Understanding the For…of Loop In JavaScript
link: https://blog.bitsrc.io/understanding-the-for-of-loop-in-javascript-8aded97d7ef8
author: [kurtwanger40](https://blog.bitsrc.io/@kurtwanger40)
---

## 理解 JavaScript 中的循环

在`JavaScript`的世界中，我们可以使用很多种循环表达式：

- `while` 表达式
- `do...while` 表达式
- `for` 表达式
- `for...in` 表达式
- `for...of` 表达式

所有这些表达式都有一个基本的功能：它们会重复一件事情直到一个具体的条件出现。

在这篇文章中，我们将深入 `for...of` 表达式，去了解它是如何工作的，以及在我们的应用中可以使用它来优化代码的地方。

<!-- **Tip**: 使用 [**Bit** (open-source)](https://github.com/teambit/bit) 快速构建 `JS` 应用。它可以让你快速发现、分享和跨应用安装组件/模块。试试吧。

![img](https://cdn-images-1.medium.com/max/1600/1*Yhkh7jbS5Mx9uP96Y88pZg.gif)

React spinner 组件：选择一个 spinner，学习如何使用。

[**Component Discovery and Collaboration · Bit**
*Bit is where developers share components and collaborate to build amazing software together. Discover components shared…*bit.dev](https://bit.dev/) -->

### for…of

`for...of` 是一种 `for` 表达式，用来迭代 `iterables(iterable objects)`直到终止条件出现。
下面是一个基础的例子：

```js
let arr = [2,4,6,8,10]
for(let a of arr) {
    log(a)
}
// It logs:
// 2
// 4
// 6
// 8
// 10
```

使用比`for`循环更好的代码，我们遍历了`arr`数组。

```js
let myname = "Nnamdi Chidume"
for (let a of myname) {
    log(a)
}
// It logs:
// N
// n
// a
// m
// d
// i
//
// C
// h
// i
// d
// u
// m
// e
```

你知道如果我们使用`for`循环，我们将必须使用数学和逻辑去判断何时我们将会达到`myname`的末尾并且停止循环。但是正如你所见的，使用`for...of`循环之后，我们将会避免这些烦人的事情。

`for...of`有以下通用的使用方法：

```js
for ( variable of iterable) {
    //...
}
```

`variable` - 保存每次迭代过程中的迭代对象的属性值
`iterable` - 我们进行迭代的对象

### Iterables（可迭代的对象） and Iterator（迭代器）

在`for...of`循环的定义中，我们说它是“迭代 *iterables(iterable objects)*”。这个定义告诉我们`for...of`循环只能用于可迭代对象。

那么， 什么是可迭代的对象（`iterables`）？

简单来说的话，可迭代对象（Iterables）是可以用于迭代的对象。在`ES6`中增加了几个特性。这些特性包含了新的协议,其中就有迭代器（Iterator）协议和可迭代（Iterable）协议。

参考`MDN`的描述，“可迭代协议允许`JS`对象定义或者修改它们的迭代行为，比如哪些值可以被`for...of`循环到。”同时“为了变成可迭代的，对象必须实现`@@iterator`方法，这意味着这个对象（或者其原型链上的对象）必须包含一个可以使用`Symbol.iterator`常量访问的`@@iterator`的属性”

说人话就是，对于可以使用`for...of`循环的对象来说，它必须是可迭代的，换句话就是它必须有`@@iterator`属性。这就是可迭代协议。

所以当对象有`@@iterator`属性的时候就可以被`for...of`循环迭代，`@@iterator`方法被`for...of`调用，并且返回一个迭代器。

同时，迭代器协议定义了一种对象中的值如何返回的方式。一个迭代器对象必须实现`next`方法，next 方法需要遵循以下准则：

- 必须返回一个包含 done, value 属性的对象
- done 是一个布尔值，用来表示循环是否结束
- value 是当前循环的值

举个例子:

```js
const createIterator = function () {
    var array = ['Nnamdi','Chidume']
    return  {
        next: function() {
            if(this.index == 0) {
                this.index++
                return { value: array[this.index], done: false }
            }
            if(this.index == 1) {
                return { value: array[this.index], done: true }
            }
        },
        index: 0
    }
}
const iterator = createIterator()
log(iterator.next()) // Nnamdi
log(iterator.next()) // Chidume
```

基本上，`@@iterator` 方法回返回一个迭代器，`for...of` 循环正是使用这个迭代器去循环操作目标对象从而得到值。因此，如果一个对象没有`@@iterator`方法同时这个返回值是一个迭代器，`for...of` 循环将不会生效。

```js
const nonIterable = //...
 for( let a of nonIterable) {
     // ...
 }
for( let a of nonIterable) {
               ^
TypeError: nonIterable is not iterable
```

内置的可迭代对象有有以下这些：

- String
- Map
- TypedArray
- Array
- Set
- Generator

注意，Object 不是可迭代的。如果我们尝试使用`for...of`去迭代对象的属性：

```js
let obj {
    firstname: "Nnamdi",
    surname: "Chidume"
}
for(const a of obj) {
    log(a)
}
```

将会抛出一个异常：

```js
for(const a of obj) {
               ^
TypeError: obj is not iterable
```

我们可以用下面的方式检查一个对象是否可迭代：

```js
const str = new String('Chidume');
log(typeof str[Symbol.iterator]);
function
```

看到了吧，打印的结果是`function`, 这意味着`@@iterator`属性存在，并且是函数类型。如果我们在 Object 上面进行尝试：

```js
const obj = {
    surname: "Chidume"
}
log(typeof obj[Symbol.iterator]);
undefined
```

哇！`undefined` 表示不存在。

### for…of: Array

数组是可迭代对象。

```js
log(typeof new Array("Nnamdi", "Chidume")[Symbol.iterator]);
// function
```

这是我们可以对它使用`for...of`循环的原因。

```js
const arr = ["Chidume", "Nnamdi", "loves", "JS"]
for(const a of arr) {
    log(a)
}
// It logs:
// Chidume
// Nnamdi
// loves
// JS
const arr = new Array("Chidume", "Nnamdi", "loves", "JS")
for(const a of arr) {
    log(a)
}
// It logs:
// Chidume
// Nnamdi
// loves
// JS
```

### for…of: String

字符串也是可迭代的。

```js
const myname = "Chidume Nnamdi"
for(const a of myname) {
    log(a)
}
// It logs:
// C
// h
// i
// d
// u
// m
// e
//
// N
// n
// a
// m
// d
// i
const str = new String("The Young")
for(const a of str) {
    log(a)
}
// It logs:
// T
// h
// e
//
// Y
// o
// u
// n
// g
```

### for…of: Map

```js
const map = new Map([["surname", "Chidume"],["firstname","Nnamdi"]])
for(const a of map) {
    log(a)
}
// It logs:
// ["surname", "Chidume"]
// ["firstname","Nnamdi"]
for(const [key, value] of map) {
    log(`key: ${key}, value: ${value}`)
}
// It logs:
// key: surname, value: Chidume
// key: firstname, value: Nnamdi
```

### for…of: Set

```js
const set = new Set(["Chidume","Nnamdi"])
for(const a of set) {
    log(a)
}
// It logs:
// Chidume
// Nnamdi
```

### for…of: TypedArray

```js
const typedarray = new Uint8Array([0xe8, 0xb4, 0xf8, 0xaa]);
for (const a of typedarray) {
  log(a);
}
// It logs:
// 232
// 180
// 248
// 170
```

### for…of: arguments

arguments is iterable? Well, let’s check it out:

```js
// testFunc.js
function testFunc(arg) {
    log(typeof arguments[Symbol.iterator])
}
testFunc()
$ node testFunc
function
```

Well, that settles it. If we investigate further, arguments is actually of type IArguments and the class implementing the IArguments interface has the @@iterator property which makes arguments iterable.

```js
// testFunc.js
function testFunc(arg) {
    log(typeof arguments[Symbol.iterator])
    for(const a of arguments) {
        log(a)
    }
}
testFunc("Chidume")
// It:
// Chidume
```

### for…of: Custom Iterables

Like we demonstrated in the previous sections we can create a custom iterable that can be iterated by `for..of`.

```js
var obj = {}
obj[Symbol.iterator] = function() {
    var array = ["Chidume", "Nnamdi"]
    return {
        next: function() {
            let value = null
            if (this.index == 0) {
                value = array[this.index]
                this.index++
                    return { value, done: false }
            }
            if (this.index == 1) {
                value = array[this.index]
                this.index++
                    return { value, done: false }
            }
            if (this.index == 2) {
                return { done: true }
            }
        },
        index: 0
    }
};
```

I created an object `obj` and to make it iterable, I assigned a `@@iterator`property to it using the `[Symbol.iterator]`. Then, I made the function to return an iterator.

```js
//...
return {
    next: function() {...}
}
//...
```

Remember, an iterator must have a `next()` function.

Inside the next function, I implemented the values will be returning to for..of during iteration. Looking at it above, you will see that what I did is quite clear.

Let’s test this our `obj` against a for..of to see what will happen:

```js
// customIterableTest.js
//...
for (let a of obj) {
    log(a)
}
$ node customIterableTest
Chidume
Nnamdi
```

Yea!!! You see it worked!

### Making Object and plain objects iterable

Plain objects are not iterable and also objects from `Object` are not iterable.

We can by-pass this by adding @@iterator to the Object.prototype with a custom iterator.

```js
Object.prototype[Symbol.iterator] = function() {
    let properties = Object.keys(this)
    let count = 0
    let isdone = false
    let next = () => {
        let value = this[properties[count]]
        if (count == properties.length) {
            isdone = true
        }
        count++
        return { done: isdone, value }
    }
    return { next }
}
```

The `properties` variable holds the properties of the object gotten using the `Object.keys()` call. In the next function, we simply return each value from the properties variable and update the count so as to get the next value from the properties variable using the count variable as the index. When the count equals the length of the properties we set done to true, so the iteration stops.

Testing using Object:

```js
let o = new Object()
o.s = "SK"
o.me = 'SKODA'
for (let a of o) {
    log(a)
}
SK
SKODA
```

It works!!!

With plain objects:

```js
let dd = {
    shit: 900,
    opp: 800
}
for (let a of dd) {
    log(a)
}
900
800
```

Tada!! :)

So we can add this as a polyfill so we can use for..of on objects where ever we want in our app.

### Using for…of on ES6 classes

We can use for..of to iterate through a list of data in an instance of a class.

```js
class Profiles {
    constructor(profiles) {
        this.profiles = profiles
    }
}
const profiles = new Profiles([
    {
        firstname: "Nnamdi",
        surname: "Chidume"
    },
    {
        firstname: "Philip",
        surname: "David"
    }
])
```

The class Profiles has a property `profile` that holds an array of users. We may need to display this data in our app using for...of. If we do this:

```js
//...
for(const a of profiles) {
    log(a)
}
```

Obviously, it won’t work

```js
for(const a of profiles) {
               ^
TypeError: profiles is not iterable
```

To make `profiles` iterable remember the rules:

- The object must have the `@@iterator` property.
- The `@@iterator` function must return an iterator.
- The `iterator` must implement the `next()` function.

We define the @@iterator property using the familiar constant `[Symbol.iterator]`.

```js
class Profiles {
    constructor(profiles) {
            this.profiles = profiles
        }
        [Symbol.iterator]() {
            let props = this.profiles
            let propsLen = this.profiles.length
            let count = 0
            return {
                next: function() {
                    if (count < propsLen) {
                        return { value: props[count++], done: false }
                    }
                    if (count == propsLen) {
                        return { done: true }
                    }
                }
            }
        }
}
```

然后，如果我们这样运行：

```js
//...
for(const a of profiles) {
    log(a)
}
$ node profile.js
{ firstname: 'Nnamdi', surname: 'Chidume' }
{ firstname: 'Philip', surname: 'David' }
```

我们可以显示 profiles 的属性

### Async Iterator

ECMAScript 2018 引入了一个新的语法，可以循环遍历一个 Promise 数组，它就是 `for-await-of` 和新的 Symbol `Symbol.asyncIterator`。

iterable 中的 `Symbol.asyncIterator` 函数需要返回一个返回 Promise 的迭代器。

```js
const f = {
    [Symbol.asyncIterator]() {
        return new Promise(...)
    }
}
```

`[Symbol.iterator]` 和 `[Symbol.asyncIterator]` 的区别在于前者返回的是 `{ value, done }` 而后者返回的是一个 `Promise`，只不过当 Promise resolve 的时候传入了 `{ value, done }`。

我们上面的那个 f 例子将如下所示：

```js
const f = {
    [Symbol.asyncIterator]() {
        return {
            next: function() {
                if (this.index == 0) {
                    this.index++
                        return new Promise(res => res({ value: 900, done: false }))
                }
                return new Promise(res => res({ value: 1900, done: true }))
            },
            index: 0
        }
    }
}
```

这个 `f` 是可异步迭代的，你看它总是返回一个 Promise ，而只有在迭代时 Promise 的 resolve 才返回真正的值。

要遍历 `f` ，我们不能使用 `for..of` 而是要使用新的 `for-await-of`，它看起来会是这样：

```js
// ...
async function fAsyncLoop(){
    for await (const _f of f) {
        log(_f)
    }
}
fAsyncLoop()
$ node fAsyncLoop.js
900
```

我们也可以使用 `for-await-of` 来循环遍历一个 Promise 数组：

```js
const arrayOfPromises = [
    new Promise(res => res("Nnamdi")),
    new Promise(res => res("Chidume"))
]
async function arrayOfPromisesLoop(){
    for await (const p of arrayOfPromises) {
        log(p)
    }
}
arrayOfPromisesLoop()
$ node arrayOfPromisesLoop.js
Nnamdi
Chidume
```

### Conclusion

在这篇文章中我们深入研究了 `for...of` 循环，我们首先定义理解什么是 `for...of`，然后看看什么是可迭代的。`for...of` 为我们节省了许多复杂性和逻辑，并有助于使我们的代码看起来很清晰易读，如果你还没有尝试过，我认为现在正是时候。
