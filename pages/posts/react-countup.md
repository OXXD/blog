---
title: 使用 react-countup 组件实现数值滚动特效
description:
date: 2024-01-07
tag: Web, React
---

# 使用 react-countup 组件实现数值滚动特效

## 实现效果

![实现效果](https://user-images.githubusercontent.com/5080854/43985960-0a7fb776-9d0c-11e8-8082-975b1e8bf51c.gif)

[在线演示](https://codesandbox.io/p/sandbox/github/glennreyes/react-countup/tree/master/demo)

## 如何使用

### 安装

```bash
npm install react-countup
```

### 基本使用

最基础的使用，只需要引入组件，并且指定一个滚动停止的数值即可。

```jsx
import CountUp from 'react-countup';

<CountUp end={100} />
```

### 当组件可见时才开始滚动

在一些情况下，可能数值并非在首屏幕可见范围，这个时候就需要指定组件滚动到可视范围时才开始滚动效果。`react-countup` 组件也提供了这个功能，只需要指定 `enableScrollSpy` 即可

```jsx
<CountUp 
  end={100} 
  enableScrollSpy // 当组件可见时才开始滚动
  scrollSpyOnce // 只展示一次滚动效果
/>
```

其他更多用法可以参考 [react-countup 文档](https://github.com/glennreyes/react-countup)，或者其底层使用的 [CountUp.js](https://inorganik.github.io/countUp.js/)

## 参考链接

- [react-countup](https://github.com/glennreyes/react-countup)
- [CountUp.js](https://inorganik.github.io/countUp.js/)
