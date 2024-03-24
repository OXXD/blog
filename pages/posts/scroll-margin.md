---
title: 如何解决使用 id 跳转页面标题时存在固定头部导航遮盖
description:
date: 2024-01-21
tag: Web
---

# 如何解决使用 #id 跳转页面标题时存在固定头部导航遮盖

在较长的页面中，使用 `#id` 可以快速跳转到指定 `id` 的段落是一个很常见的技巧。但是常见的页面一般都是有导航栏和正文组成，如果直接这样跳转可能会出现被导航栏遮挡的效果，如下图所示：

![scroll-margin-1](/public/images/minigame/scroll-margin-1.gif)

页面结构如下：

```html
    <style>
      nav {
        height: 40px;
        position: fixed;
        top: 0;
        left: 0;
        right: 0;
        background-color: white;
        border-bottom: 1px solid #000;
      }
      aside {
        position: fixed;
        top: 0;
        right: 0;
      }
      main {
        margin-top: 40px;
      }
      h2 {
        margin: 0;
      }
    </style>
    <nav>导航栏</nav>
    <aside>
      <div>
        <a href="#title1">跳转标题1</a>
      </div>
      <div>
        <a href="#title2">跳转标题2</a>
      </div>
    </aside>
    <main>
      <div>
        <h2 id="title1">标题1</h2>
        <!-- ...正文内容 -->
      </div>
      <div>
        <h2 id="title2">标题2</h2>
        <!-- ...正文内容 -->
      </div>
    </main>
```

## 解决方案

其实解决方案也十分简单，只需要给标题标签加上 [scroll-margin](https://developer.mozilla.org/en-US/docs/Web/CSS/scroll-margin) 即可。

按照 `ChatGPT` 解释：`scroll-margin` 是一个用于 `CSS` 的属性，它允许你定义在滚动容器（如滚动条所在的元素）和滚动容器内部元素之间的外边距，以调整滚动容器滚动时元素的可见性。这个属性可以用于创建“滚动吸附”效果，使元素在滚动到特定位置时停止，并且在滚动时预留一定的空间。

使用方式和 `margin` 基本一致，通过指定四个值（上、右、下、左）来设置外边距。这些值可以是长度、百分比、或 auto。

```css
h2 {
  scroll-margin: 10px 20px 30px 40px;
}
```

要解决被固定导航栏遮挡，只需要指定 `scroll-margin-top` 即可

```css
h2 {
  scroll-margin-top: 40px;
}
```

加上这个样式之后，跳转就和我们所需要的效果一致：

![scroll-margin-2](/public/images/minigame/scroll-margin-2.gif)

## 参考链接

- [scroll-margin](https://developer.mozilla.org/en-US/docs/Web/CSS/scroll-margin)
