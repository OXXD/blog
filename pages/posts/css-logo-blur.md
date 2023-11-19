---
title: 使用 CSS 实现 Logo 阴影特效
description:
date: 2023-11-19
tag: Web, CSS
---

# 使用 CSS 实现 Logo 阴影特效

## 实现效果

![css-logo-blur](/images/minigame/css-logo-blur.png)

[在线演示](https://codepen.io/oxxd/pen/rNPJMKx)

## 实现

HTML 元素：

```html
<figure>
  <section class="img-bg"></section>
  <img height="320" width="320" src="https://vitejs.dev/logo-with-shadow.png" alt="Vite Logo" />
</figure>
```

`CSS` 样式代码：

```css
.img-bg {
  position: absolute;
  background-image: linear-gradient(-45deg, #bd34fe 50%, #47caff 50%);
  border-radius: 50%;
  filter: blur(72px);
  z-index: -1;
  animation: pulse 4s cubic-bezier(0, 0, 0, 0.5) infinite;
}

@keyframes pulse {
  50% {
    transform: scale(1.5);
  }
}
```

实现 Logo 阴影特效的核心样式就在这几行 `CSS` 中。以下分部解释每行样式的作用：

### 1. 增加背景

```css
  background-image: linear-gradient(-45deg, #bd34fe 50%, #47caff 50%);
```

![css-logo-blur](/images/minigame/css-logo-blur-1.png)

### 2. 将背景设置为圆形

```css
  border-radius: 50%;
```

![css-logo-blur](/images/minigame/css-logo-blur-2.png)

### 3. 加入关键的 [filter 属性](https://developer.mozilla.org/zh-CN/docs/Web/CSS/filter) 将模糊的图形效果应用于元素

```css
  filter: blur(72px);
```

![css-logo-blur](/images/minigame/css-logo-blur-3.png)

### 4. 将背景至于图形底部

```css
  z-index: -1;
```

![css-logo-blur](/images/minigame/css-logo-blur-4.png)

### 5. 加入动画效果

```css
  animation: pulse 4s cubic-bezier(0, 0, 0, 0.5) infinite;
```

## 参考链接

- [参考自 Twitter](https://twitter.com/_davideast/status/1586002781777039360)
- [Vite](https://vitejs.dev/)
- [filter 属性](https://developer.mozilla.org/zh-CN/docs/Web/CSS/filter)
