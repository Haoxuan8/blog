# JS 基础知识

## 数据类型与运算

### 原始 \(Primitive\) 类型

js有6种原始类型:

* `boolean`
* `null`
* `undefined`
* `number`
* `string`
* `symbol`

原始类型存储的都是值，没有函数可以调用。

### 对象 \(Object\) 类型

除了原始类型其他的都是对象类型。原始类型存的是值，对象类型存的是指针。

```javascript
const a = []
const b = a
b.push(1)
```

结果b和a都会发生改变。

```javascript
function test(person) {
    person.age = 26
    person = {
        name: 'yyy',
        age:30
    }
    return person
}

const p1 = {
    name: 'zzz',
    age: 25
}

const p2 = test(p1)
console.log(p1.age) //26
console.log(p2.age) //30
```

函数传参是传递对象指针的拷贝。

### typeof vs instanceof

`typeof` 对于原始类型来说，除了 `null` 都可以显示正确的类型。

`typeof` 对于对象类型来说，除了 `function` 都会显示 `object`

`instanceof` 通过原型链实现。原始类型不能用 `instanceof` 判断。

```javascript
const Person = function() {}
const p1 = new Person()
p1 instanceof Person //true

var str = 'hello world'
str instanceof String // false

var str1 = new String('hello world')
str1 instanceof String // true
```



### 类型转换

JS中类型转换只有三种情况：

* 转换为布尔值
* 转换为数字
* 转换为字符串

<!-- ![](../.gitbook/assets/image.png) -->

对象在转换类型的时候回调用 `[[ToPrimitive]]` 函数：

* 如果转换字符串则调用 `x.toString()` ，转换为基础类型的话就返回转换的值。不是字符串的话就先调用 `valueOf` ，结果不是基础类型的话再调用 `toString` 

可以重写 `Symbol.toPrimitive` 

### 四则运算

加法运算中一方为字符串，把另一方也转换为字符串。如果一方不是字符串或者数字，则会将它转换为数字或者字符串。

除加法外，只要其中一方是数字，那么另一方也会被转换为数字。

```javascript
1 + '1' //'11'
true + true //2
4 + [1,2,3] // '41,2,3'
'a' + + 'b'  // 'aNaN'

4 * '3' // 12
4 * [] // 0
4 * [1,2] //NaN
```

### 比较运算符

* 如果为对象，就通过 `toPrimitive` 转换对象
* 如果为字符串，就通过 `unicode` 字符索引来比较

```javascript
let a = {
    valueOf() {
        return 0
    }
}

a > -1 // true
```

比较数字，调用 `valueOf` 转换为原始类型。

## this

指向调用它的对象。

```javascript
function foo() {
    console.log(this.a)
}
var a = 1
foo() // this = window

const obj = {
    a: 2,
    foo: foo
}
obj.foo() //this = obj

const c = new foo() // this = c this被永远绑定在c上，不会被任何方式改变
```

箭头函数 `this` 取决于包裹箭头函数的第一个普通函数的 `this` 。对箭头函数使用 `bind` 是无效的。

```javascript
let a = {}
let fn = function() {console.log(this)}
fn.bind().bind(a)() // window
```

多次 `bind` 取决于第一次 `bind` 。

## 闭包

定义：函数A内部有个函数B，函数B可以访问到函数A中的变量，函数B就是闭包。

```javascript
for(var i=1;i<=5;i++) {
    setTimeout(function timer() {
        console.log(i)
    },i*1000)
}
// 输出一堆6

for(var i=1;i<=5;i++) {
    (function(j) {
        setTimeout(function timer(){
            console.log(j)
        },1000)
    })(i)
}
```

## 深浅拷贝

### 浅拷贝

只拷贝一层，可以用 `Object.assign` 。如果属性值是个对象拷贝的还是地址。

```javascript
let a = {
    age: 1
}
let b = Object.assign({},a)
a.age = 2
console.log(b.age) //1

//-----------------------//

let b = { ...a }
```

### 深拷贝

```javascript
let a = {
    age: 1,
    jobs: {
        first: 'FE'
    }
}
let b = JSON.parse(JSON.stringify(a))
a.jobs.first = 'native'
console.log(b.jobs.first)  // 'FE'
```

局限性：

* 会忽略 `undefined` 
* 会忽略 `symbol` 
* 不能序列化函数
* 不能解决循环引用的对象

## 原型

每个对象有个 `__proto__` 属性指向原型，对象的 `constructor` 有 `prototype` 属性也指向原型（函数才有 `prototype`），并不是所有函数都有这个属性， `Function.prototype.bind()` 就没有这个属性。

原型链指的是多个对象通过 `__proto__` 的方式连接起来。

`Function` 和 `Object` 都是构造函数，所以他们的 `__proto__` 都是 `Function.prototype` ，`Function.prototype` 是个对象，所以 `Function.prototype.__proto__ == Object.prototype` 

## ES6 知识点

### var, let 和 const

提升：可以使用未被声明的变量，并且提升的是声明。函数提升优先于变量提升。

使用 `var` 声明的变量会被提升到作用域的顶部。 `let const` 不能在声明前使用。

```javascript
console.log(a) //f a() {}
function a() {}
var a = 1
```

### 原型继承和class继承

`class` 本质还是函数。

#### 组合继承

```javascript
function Parent(value) {
    this.val = value
}
Parent.prototype.getValue = function() {
    console.log(this.val)
}

function Child(value) {
    Parent.call(this, value)
}
Child.prototype = new Parent()

const child = new Child(1)
child.getValue() //1
child instanceof Parent //true
```

以上继承方法是在子类的构造函数中通过 `Parent.call(this)` 继承父类的属性，然后改变子类的原型为 `new Parent()` 来继承父类的函数。

这种继承方式不会与父类引用属性共享，可以复用父类的函数，但是因为调用了父类的构造函数，所以导致子类会有一些不需要的父类属性。

#### 寄生组合继承

```javascript
function Parent(value) {
    this.val = value
}
Parent.prototype.getValue = function() {
    console.log(this.val)
}

function Child(value) {
  Parent.call(this, value)
}
Child.prototype = Object.create(Parent.prototype, {
  constructor: {
    value: Child,
    enumerable: false,
    writable: true,
    configurable: true
  }
})
```

以上继承实现的核心就是将父类的原型赋值给了子类，并且将构造函数设置为子类，这样既解决了无用的父类属性问题，还能正确的找到子类的构造函数。

#### Class 继承

在 ES6 中还可以使用 `class` 继承。

```javascript
class Parent {
  constructor(value) {
    this.val = value
  }
  getValue() {
    console.log(this.val)
  }
}
class Child extends Parent {
  constructor(value) {
    super(value)
  }
}
let child = new Child(1)
child.getValue() // 1
child instanceof Parent // true
```

### 模块化

* 解决命名冲突
* 提高代码可复用性
* 提高代码可维护性

#### 立即执行函数

```javascript
(function(globalVariable){
   globalVariable.test = function() {}
   // ... 声明各种变量、函数都不会污染全局作用域
})(globalVariable)
```

#### CommonJS

```javascript
//a.js
module.exports = {a:1}

//or
exports.a = 1

//b.js
var module = require('./a.js')
module.a //log 1
```

#### ES Module

* CommonJS 支持动态导入，也就是require\(${path}/xx.js\)
* CommonJS是同步导入，因为用于服务端；ES用于浏览器，异步导入
* CommonJS导出是值拷贝；ES采用实时绑定

### Proxy

Proxy 是 ES6 新增的功能，可以用来自定义对象的操作。

```javascript
let p = new Proxy(target, handler)
```

```javascript
let onWatch = (obj, setBind, getLogger) => {
  let handler = {
    get(target, property, receiver) {
      getLogger(target, property)
      return Reflect.get(target, property, receiver)
    },
    set(target, property, value, receiver) {
      setBind(value, property)
      return Reflect.set(target, property, value)
    }
  }
  return new Proxy(obj, handler)
}

let obj = { a: 1 }
let p = onWatch(
  obj,
  (v, property) => {
    console.log(`监听到属性${property}改变为${v}`)
  },
  (target, property) => {
    console.log(`'${property}' = ${target[property]}`)
  }
)
p.a = 2 // 监听到属性a改变
p.a // 'a' = 2
```

### map, filter, reduce

`map` 作用是生成一个新数组。`map` 的回调函数接受三个参数，分别是当前索引元素，索引，原数组。

```javascript
['1','2','3'].map(parserInt) // 1 NaN NaN
```

`filter` 的作用也是生成一个新数组，返回 `true` 的元素到新数组。也接受三个参数。

`reduce` 将数组最终转换为一个值。接受两个参数，回调函数和初始值。回调函数接受四个参数，累积值。。。

```javascript
const arr = [1,2,3]
arr.reduce((acc, curr) => acc + curr, 0)
```

## JS 异步编程

### 并发 \(concurrency\) 和并行 \(parallelism\) 区别

并发：通过任务间的切换完成任务

并行：同时进行任务

### 回调函数

回调函数：传入函数指针，调用函数

回调地狱：多个请求存在依赖性，回调函数中还有回调函数

```javascript
ajax(url, () => {
    // 处理逻辑
    ajax(url1, () => {
        // 处理逻辑
        ajax(url2, () => {
            // 处理逻辑
        })
    })
})
```

### Generator

Generator 可以用来控制函数的执行。Generator 返回一个迭代器。

```javascript
function *foo(x) {
    let y = 2*(yield (x+1))
    let z = yield(y/3)
    return (x+y+z)
}
let it = foo(5)
console.log(it.next())   // => {value: 6, done: false}
console.log(it.next(12)) // => {value: 8, done: false}
console.log(it.next(13)) // => {value: 42, done: true}
```

* 第一次 `next` 函数返回 `yield(x+1) = 6`  
* 第二次 `next` 函数输入12为上一个 `yield` 返回值，所以 `y = 2*12 = 24` ，返回 `yield(y/3) = 24/3 = 8` 
* 第三次 `next` 输入13，返回 `x+y+z = 5+24+13=42` 

可以通过 `Generator` 来解决回调地狱的问题:

```javascript
function *fetch() {
    yield ajax(url, () => {})
    yield ajax(url1, () => {})
    yield ajax(url2, () => {})
}
let it = fetch()
let result1 = it.next()
let result2 = it.next()
let result3 = it.next()
```

### Promise

Promise 有三种状态：pending, resolved, rejected

一旦状态从 pending 变成其他状态就不能再改变了。每次调用 then 返回的都是一个 Promise

```javascript
ajax(url)
  .then(res => {
      console.log(res)
      return ajax(url1)
  }).then(res => {
      console.log(res)
      return ajax(url2)
  }).then(res => console.log(res))
```

### async 及 await

一个函数如果加上 `async` ，那么该函数就会范围一个 `Promise` 

`await` 将异步代码改成同步代码，如果异步代码没有依赖性却使用 `await` 会导致性能上的降低。

```javascript
let a = 0
let b = async () => {
  a = a + await 10
  console.log('2', a)
}
b()
a++
console.log('1', a) // -> '1' 1
                    // -> '2' 10
```

* 首先函数 b 执行，执行到 `await 10` 之前变量 `a` 还是0，因为 `await` 内部实现了 `generator` ，`generator` 会保留堆栈中的东西，所以这时 `a=0` 被保存了下来。
* 因为 `await` 是异步操作，表达式若不返回 `Promise` 的话，就会包装成 `Promise.reslove` ，然后执行函数外的同步代码。
* 同步代码执行完毕后开始异步代码，将保存下来的值拿来用， `a=0+10` 






















