# lodash/fp

## Functional Programming(FP)

`lodash/fp` 提供了更加[函数式编程](https://en.wikipedia.org/wiki/Functional_programming)风格的方法。

传统的 `lodash` 使用方法：

```javascript
import _ from "lodash";
_.map([1,2,3], row => row*2);

_.chain([1,2,3])
    .map(row => row*2)
    .filter(row => row === 2)
    .value();
```

`fp` 的使用方法：

```javascript
import {map, filter, flow} from "lodash/fp";
map(row => row*2)([1,2,3]);

flow(
    map(row => row*2),
    filter(row => row === 2)
)([1,2,3]);
```

### `lodash/fp` 特点

- immutable
- auto-curried
- iteratee-first data-last


### 不再使用 `_.chain`

使用 `fp/flow` 代替 `chain`。

- 并不需要 `lodash` 的所有方法，`_.chain` 会导致导入整个模块，随着 `lodash` 方法逐渐增加，打包体积会越来越大。
- `_.chain` 无法加上自定义的方法。


## Code Efficiently!

- [eslint-plugin-lodash-fp](https://www.npmjs.com/package/eslint-plugin-lodash-fp)

## Resources

- [github](https://github.com/lodash/lodash/wiki/FP-Guide)
- [Why using _.chain is a mistake](https://medium.com/bootstart/why-using-chain-is-a-mistake-9bc1f80d51ba)