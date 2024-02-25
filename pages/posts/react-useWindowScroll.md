---
title: 如何实现获取窗口滚动距离的 React Hook
description:
date: 2024-01-14
tag: Web, React
---

# 如何实现获取窗口滚动距离的 React Hook

获取滚动距离是页面中常见的操作，获取滚动距离之后可以根据不同滚动位置实现不同特效，如导航栏特效、动画效果等。

在原生 `DOM` 操作中一般是通过添加 `scroll` 事件监听可以获取到滚动位置，在 `React` 应用中，将这一部分逻辑封装成一个 `React Hook` 可以更方便使用、维护和复用代码逻辑。

## 实现

在 `DOM` 操作中通过添加 `scroll` 事件监听可以获取到滚动位置。封装成一个 `React Hook` 也是一样，只需要在 `useEffect`  或者 `useLayoutEffect` 中添加`scroll` 事件监听，并且将获取到的滚动值记录起来即可。

```js
export function useWindowScroll() {
  const [state, setState] = React.useState({
    x: null,
    y: null,
  });

  React.useLayoutEffect(() => {
    const handleScroll = () => {
      setState({ x: window.scrollX, y: window.scrollY });
    };
    
    // 绑定事件监听
    handleScroll();
    window.addEventListener("scroll", handleScroll);
    
    // 记得组件移除时需要去除事件监听
    return () => {
      window.removeEventListener("scroll", handleScroll);
    };
  }, []);

  return [state];
}
```

另外也可以在 `hook` 中额外封装 `scrollTo` 方法，提供滚动到任意位置的 `API`

```js
export function useWindowScroll() {
  const scrollTo = React.useCallback((...args) => {
    if (typeof args[0] === "object") {
      window.scrollTo(args[0]);
    } else if (typeof args[0] === "number" && typeof args[1] === "number") {
      window.scrollTo(args[0], args[1]);
    } else {
      throw new Error(
        `Invalid arguments passed to scrollTo. See here for more info. https://developer.mozilla.org/en-US/docs/Web/API/Window/scrollTo`
      );
    }
  }, []);
  
  return [state, scrollTo];
}
```

## 使用方法

只需要从 `useWindowScroll` 中取出需要的 `x`, `y` 值即可，也可以用封住的 `scrollTo` 方法滚动到指定位置。

```jsx
export default function App() {
  const [{ x, y }, scrollTo] = useWindowScroll();
  return (
    <section>
      <button className="link" onClick={() => scrollTo(0, 1000)}>
        Scroll To (0, 1000)
      </button>
      <span className="x">x: {x}</span>
      <span className="y">y: {y}</span>
    </section>
  );
}
```

## 参考链接

- [useHooks](https://github.com/uidotdev/usehooks/blob/main/index.js#L1310)