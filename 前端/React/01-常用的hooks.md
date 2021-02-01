# 常用的Hooks

于此记录收集到的或是自己自定义的react hook。

## useOnScreen

用于监听元素是否滚动到可见区域，用了 `Intersection Observer` 接口。

由于部分浏览器暂不支持此接口，可通过 `yarn add intersection-observer` 或是 `npm install intersection-observer` 安装ployfill。

### Code

```javascript
const useOnScreen = (rootMargin = '0px') => {
    const [isIntersecting, setIntersecting] = useState(false);

    const ref = useRef();

    useEffect(() => {
        const observer = new IntersectionObserver(
            ([entry]) => {
                setIntersecting(entry.isIntersecting);
            },
            {
                rootMargin
            }
        );

        const node = ref.current;
        if (node) {
            observer.observe(node);

            return () => {
                observer.unobserve(node);
            }
        }
    }, [ref.current]);

    return [ref, isIntersecting];
}

```

### Usage

以图片滚动到显示区域再加载为例

```javascript
const ImageWrapper = props => {
    const {
        url,
    } = props;

    const [visible, setVisible] = useState(false);
    const [wrapperRef, onScreen] = Hooks.useOnScreen();

    useEffect(() => {
        if (onScreen) {
            const temp = new Image();
            temp.onload = () => {
                setVisible(true);
            }
            temp.src = url;
        }
    }, [url, onScreen]);

    return (
        <div ref={wrapperRef}>
            {
                visible
                    ? <img src={url} />
                    : null
            }
        </div>
    )
}
```

### Resources

- 更加完善的相关库 [react-intersection-observer](https://github.com/thebuilder/react-intersection-observer)


## useEventListener

用于给组件添加事件监听和移除事件监听。

在写 `class` 组件时，通常需要在 `componentWillUnmount` 生命周期中移除事件监听，`react` 的函数组件淡化了生命周期的概念，所以将添加与移除事件监听的逻辑都写在了此 `hook` 里。

### Code

```javascript
const useEventListener = (eventName, handler, element = window) => {
    const saveHandler = useRef();

    useEffect(() => {
        saveHandler.current = handler;
    }, [handler]);

    useEffect(
        () => {
            const isSupported = element && element.addEventListener;
            if (!isSupported) return;

            const eventListener = event => saveHandler.current(event);
            element.addEventListener(eventName, eventListener);

            return () => {
                element.removeEventListener(eventName, eventListener);
            };
        },
        [eventName, element]);
};
```

### Resources

- 来源 [donavon/use-event-listener](https://github.com/donavon/use-event-listener)

## useDeepEffect

用于想在依赖变量深比较发生变化时再执行副作用。例如父组件给子组件直接传递了一个数组，每次渲染数组的引用的都发生了变化，但是不希望执行子组件的相应副作用，可用此 `hook` 解决此问题。

深比较用了 `lodash` 的 `isEqual` 方法。

### code

```javascript
const useDeepEffect = (callback, deps) => {
    const isFirst = useRef(true);
    const prevDeps = useRef(deps);

    useEffect(() => {
        const isSame = prevDeps.current.every((obj, index) => _.isEqual(obj, deps[index]));

        if (isFirst.current || !isSame) {
            callback();
        }

        isFirst.current = false;
        prevDeps.current = deps;
    }, deps);
}
```

### Resources

- 相关实现库 [kentcdodds/use-deep-compare-effect](https://github.com/kentcdodds/use-deep-compare-effect)