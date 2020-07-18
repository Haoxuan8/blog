# ES6 知识点

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



