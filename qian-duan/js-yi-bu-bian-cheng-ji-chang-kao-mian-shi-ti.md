# JS 异步编程及常考面试题

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

### 常用定时器函数







