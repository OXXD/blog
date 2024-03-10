---
title: 使用 Framer Motion 实现滚动动画
description: 
date: 2024-03-10
tag: React
---

# 使用 Framer Motion 实现滚动动画

[Framer Motion](https://www.framer.com/motion/) 是一个强大的 `React` 动画库，它提供了简单、强大和可扩展的 API，用于创建各种类型的动画效果。

本文介绍[Framer Motion](https://www.framer.com/motion/) 的基本使用，和如何使用其提供的 `API` 快速实现滚动动画。

## 基本使用

`Framer Motion` 的核心是提供了 `HTML` 元素和 `SVG` 元素对应的 [motion](https://www.framer.com/docs/component/)组件，将对应的元素替换为 `motion` 组件，并且属性配置简单的初始状态、目标状态和过渡效果即可实现动画效果。

```tsx
import { motion } from 'framer-motion';

const App = () => {
  return (
    <motion.div
      initial={{ opacity: 0, scale: 0 }}
      animate={{ opacity: 1, scale: 1}}
      transition={{ duration: 0.5 }}
    >
      Hello Framer Motion!
    </motion.div>
  );
};
```

在这个示例中，我们使用 `motion.div` 包裹一个 `<div>` 元素，然后定义了 `initial`、`animate` 和 `transition` 属性来指定初始状态、目标状态和过渡效果。在初始状态下，元素的透明度（opacity）和缩放比例（scale）都是 0，然后通过 `animate` 属性指定了目标状态，透明度和缩放比例都是 1。`transition` 属性定义了过渡效果的持续时间为 0.5 秒。

## 滚动动画

实现常见的页面滚动到指定元素时展示动画，只需要使用 `whileInView` 这个属性定义元素出现时的样式，比如透明度，大小，偏移位置等。

示例代码如下，定义刚开始时透明度为 0（即不可见），当滚动到元素时设置为透明度 1，元素可见并且会一淡入的动画效果展示。

```tsx
<motion.div
  initial={{ opacity: 0 }}
  whileInView={{ opacity: 1 }}
/>
```

实现效果图：

![react-framer-motion](/images/minigame/react-framer-motion.gif)

通过设置其他更多样式和属性，我们可以更自由的配置想要的动画效果。如初始位置为 `30px`，元素可见时从上往下展示，通过 `viewport` 和 `transition` 属性设置动画的展示时机和方式。

```tsx
<motion.div
  initial={{ opacity: 0, y: 30 }}
  whileInView={{ opacity: 1, y: 0 }}
  viewport={{ once: true }}
  transition={{ duration: 1 }}
>
```

[Framer Motion](https://www.framer.com/motion/) 是一个强大的动画库，能实现各种简单和复杂的动画效果，更多的使用方式和动画效果可以参考[Framer Motion 文档](https://www.framer.com/motion/)

## 参考链接

- [Framer Motion](https://www.framer.com/motion/)
- [Scroll-triggered animations](https://www.framer.com/motion/scroll-animations/#scroll-triggered-animations)