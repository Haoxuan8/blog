# diff算法

## 虚拟DOM

`React` 与 `Vue` 都使用了 `Virtual DOM` 来提高页面的渲染效率。他将页面状态抽象为 `js` 对象，与真实的 `DOM` 一一对应。

### 优点

- 减少直接 `DOM` 操作，通过比较前后 `Virtual DOM` 来统一进行 `DOM` 操作。
- 跨平台，从 `Virtual DOM` 渲染到其他平台。

## React diff 算法
待完成