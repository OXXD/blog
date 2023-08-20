---
title: 使用 next-themes 两行代码为 Next.js 项目添加暗黑模式
description: 
date: 2023-08-06
tag: React, Next.js
---

# 使用 next-themes 两行代码为 Next.js 项目添加暗黑模式

之前写过文章介绍 [React 暗黑模式的实现方式](https://juejin.cn/post/7160144602144440328)，其原理基本和目前主流的暗黑模式实现方案相似，主要用到的技术有：`CSS Variables`, 媒体查询, `window.matchMedia`, `React Context` 等。但是每个项目都需要重复实现会带来冗余的代码和额外的维护成本。

于是发现社区已有了封装好的方案：[next-themes](https://github.com/pacocoursey/next-themes)，口号是 `Perfect Next.js dark mode in 2 lines of code. `经过试用之后发现其接入简单、功能丰富、支持自定义扩展度高、实现原理基本类似，确实能够快速的为 `Next.js` 项目添加暗黑模式支持。

本文介绍 `next-themes` 的使用方式。实现效果可以见[在线演示](https://next-themes-example.vercel.app/)

## 使用

### 1. 安装

```bash
npm install next-themes
```

### 2. 在 pages/_app.js 中加入 ThemeProvider

这里就是所谓的”两行代码“，只需要引入 `ThemeProvider` 和使用 `ThemeProvider` 包裹组件即可。

当然其实更多的自定义样式和样式切换会需要额外的代码，但也可以使用提供的封装好的 `useTheme hook` 快速实现。 

```jsx
import { ThemeProvider } from 'next-themes'

function MyApp({ Component, pageProps }) {
  return (
    <ThemeProvider>
      <Component {...pageProps} />
    </ThemeProvider>
  )
}

export default MyApp
```

### 3. 基于 [data-theme='dark'] 属性选择器改变样式

默认情况下，`next-themes` 会修改 `html` 元素上的 `data-theme` 属性，可以轻松地使用该属性来设置样式：

```css
:root {
  /* Your default theme */
  --background: white;
  --foreground: black;
}

[data-theme='dark'] {
  --background: black;
  --foreground: white;
}
```

### 4. 使用 useTheme 获取当前主题和改变主题

```jsx
import { useTheme } from 'next-themes'

const ThemeChanger = () => {
  const { theme, setTheme } = useTheme()

  return (
    <div>
      The current theme is: {theme}
      <button onClick={() => setTheme('light')}>Light Mode</button>
      <button onClick={() => setTheme('dark')}>Dark Mode</button>
    </div>
  )
}
```

## Tailwind CSS 中使用方式

`TailWind CSS` 中是基于 `dark:` 来切换暗黑模式样式的，`next-themes` 也可以结合 `TailWind CSS` 一起使用。

实现效果可以看[在线演示](https://next-themes-tailwind.vercel.app/)

### 1. 将 Tailwind 的暗黑模式配置为基于 `class`

```javascript
// tailwind.config.js
module.exports = {
  darkMode: 'class'
}
```

### 2. 将 `data-attribute` 方式改为 `class`

```jsx
// pages/_app.js
<ThemeProvider attribute="class">
```

### 3. 基于 `dark:` 来切换暗黑模式下的样式

```jsx
<h1 className="text-black dark:text-white">
```

更多使用方式可见[next-themes 项目文档](https://github.com/pacocoursey/next-themes)

## 参考链接

- [next-themes](https://github.com/pacocoursey/next-themes)
- [Tailwind CSS Dark Mode](https://tailwindcss.com/docs/dark-mode)
- [React 通过 CSS Variables 实现暗黑模式](https://juejin.cn/post/7160144602144440328)
