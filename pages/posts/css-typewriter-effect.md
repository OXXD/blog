---
title: 使用 CSS 实现打字机效果
description:
date: 2023-10-22
tag: Web, CSS
---

# 使用 CSS 实现打字机效果

## 实现效果

![css-typewriter-effect](/images/minigame/css-typewriter-effect.gif)

[在线演示](https://codepen.io/oxxd/pen/NWoWgZa)

## 实现

HTML 元素：

```html
<div class="typewriter">
  <h1 class="typing">The cat and the hat.</h1>
</div>
```

实现打字机效果的关键是两个动画效果，文字出现的动画，和光标闪烁出现的动画。

这两个动画的实现方式也很简单，文字出现的动画只通过控制元素长度即可。光标闪烁出现可以通过添加右边框，并且给边框添加颜色动画实现。

```css
/* 打字机动画 */
@keyframes typing {
  from { width: 0 }
  to { width: 100% }
}
```

```css
/* 光标动画 */
@keyframes blink-caret {
  from, to { border-color: transparent }
  50% { border-color: orange }
}
```

然后将动画效果添加到指定的元素上即可。

```css {9-11}
.typewriter .typing {
  color: #fff;
  font-family: monospace;
  overflow: hidden; /* 保证文字在动画之前隐藏 */
  border-right: .15em solid orange; /* 使用边框实现光标 */
  white-space: nowrap;
  margin: 0 auto;
  letter-spacing: .15em;
  animation:  /** 动画效果 */
    typing 3.5s steps(30, end),
    blink-caret .5s step-end infinite;
}
```

结合 `Javascript` 来控制样式类显示隐藏时机，可以轻松为不同文本实现打字机效果。

## 参考链接

- [CSS Tricks - Typewriter Effect](https://css-tricks.com/snippets/css/typewriter-effect/)
- [Typed.css](https://typedcss.com)
