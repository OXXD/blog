---
title: 制作一个响应式头部导航栏（Tailwind CSS + Headless UI）
description: 
date: 2023-07-30
tag: React, Tailwind CSS
---

# 制作一个响应式头部导航栏（Tailwind CSS + Headless UI）

头部导航栏几乎是每个网站必备的元素，可以提供快速的页面跳转和主要信息展示。在移动设备为主的当前环境下，制作一个网站首先需要考虑移动端用户的体验，那么使用响应式方案来实现就是首选方案。

借助前端社区快速发展的各种工具，比如 [Tailwind CSS](https://headlessui.com/) 原子化样式方案可以实现任意样式（以及暗黑模式、响应式等）, [Headless UI](https://headlessui.com/) 基于无样式组件提供的可扩展可自定样式的各种交互组件，我们可以快速高效的实现一个优秀好看可维护的响应式头部导航栏。

本文介绍以 `Next.js` 框架，结合 `Tailwind CSS`, `Headless UI` 如何制作一个响应式头部导航栏。

实现效果：

![responsive-navbar](/images/minigame/responsive-navbar.gif)

## 一. 准备

### 1. 初始化 Next.js 项目，选择 Tailwind CSS 模版

```bash
npx create-next-app@latest
```

```bash
What is your project named? my-app
Would you like to use TypeScript? No / Yes
Would you like to use ESLint? No / Yes
Would you like to use Tailwind CSS? No / Yes
Would you like to use `src/` directory? No / Yes
Would you like to use App Router? (recommended) No / Yes
Would you like to customize the default import alias? No / Yes
```

### 2. 安装 Headless UI 和图标库

这里我们使用 [Headless UI 的 Dialog](https://headlessui.com/react/dialog) 组件来实现移动端的收起展开菜单交互效果，也可以使用其他无样式组件（如 [Radix UI](https://www.radix-ui.com/)）或者基于样式自行实现。图标也是同理，使用了 `heroicons` 中的菜单图标。

```bash
npm i @headlessui/react @heroicons/react
```

### 3. 修改默认全局样式

这里我们先不考虑暗黑模式和其他全局样式。先采用统一默认全局背景黑色，字体颜色白色。

`src/styles/globals.css`

```css
:root {
  --color-foreground: #fff;
  --color-background: #000;
}

body {
  color: var(--color-foreground);
  background: var(--color-background);
}
```

### 4. 自定义一些 Tailwind CSS 颜色配置

即使 `Tailwind CSS` 提供了很多默认配置和默认颜色，但是每个项目不同，肯定都会有自己的颜色和配置需求，这些在 `Tailwind CSS` 中也方便扩展和配置，更多配置可以[参考文档](https://tailwindcss.com/docs/adding-custom-styles)

`tailwind.config.js` 在 `extend.colors` 中扩展一些黑色的颜色，后续使用可以直接使用 `bg-black-50`, `bg-black-100` 等

```diff
+  colors: {
+    black: {
+      50: "#1A1A1A",
+      100: "#222222",
+      200: "#333333",
+    },
+  }
```

## 制作导航栏组件

我们可以参考 [Tailwind Components](https://tailwindui.com/components/marketing/elements/headers) 提供的一些组件来实现导航栏组件

### 1. 创建 Navbar 组件

`src/components/Navbar.tsx`

```tsx
export default function Navbar() {
  return <header></header>
}
```

### 2. 布局

这里我们使用语义化标签 `header`, `nav` 来作为主要的父元素，并且加上背景、`flex`、左右靠边、上下居中等样式，划分好需要哪些子元素，以及子元素所在的位置。

```tsx
<header className="bg-black-100">
  <nav
    className="mx-auto flex items-center justify-between p-2 lg:px-8"
    aria-label="Global"
  >
    {/* 左侧 Logo */}
    {/* 左侧 Title */}
    {/* 菜单列表 */}
    {/* 右侧菜单 */}
    {/* 移动端可见的右侧展开收起图标按钮 */}
  </nav>
  {/* 移动端可见的展开后的菜单列表 Dialog */}
</header>
```

### 3. 实现左侧 `Logo` 和左侧 `Title`

`Logo` 和 `Title` 就是简单的图片和文字，这里样式比较简单，实际实现是可以用 `next/image` 组件代替 `img` 标签。

```tsx
{/* 左侧 Logo */}
 <div className="flex">
  <Link href="/" className="ml-4">
    <span className="sr-only">Haunted Dorm</span>
    <img
      className="h-10 w-auto rounded-lg lg:h-16"
      src="https://res.minigame.vip/gc-assets/haunted-dorm/haunted-dorm_icon.png"
      alt="logo"
    />
  </Link>
</div>
{/* 左侧 Title */}
<div className="mx-2 text-white lg:mx-8">Haunted Dorm</div>
```

### 4. 实现菜单列表

需要注意的是，在 `Tailwind CSS` 中，实现样式时是[以移动端样式为主](https://tailwindcss.com/docs/responsive-design#working-mobile-first)，然后根据不同响应式断点来添加更多的不同尺寸下的样式，所以我们的菜单列表在移动端是隐藏的，需要一开始就加上 `hidden` 隐藏样式，在 `lg` 下加上 `flex` 让他可见。

并且我们把菜单数据提取成变量，基于此循环出菜单列表，更利于代码复用。

![responsive-design-tailwindcss](/images/minigame/responsive-design-tailwindcss.png)

```tsx
const menus = [
  {
    title: "Home Page",
    href: "/",
  },
  {
    title: "Game Details",
    href: "/detail",
  },
  {
    title: "Game Strategy",
    href: "/guide",
  },
  {
    title: "Interactive Community",
    href: "/community",
  },
];

{/* 菜单列表 */}
<div className="hidden lg:flex lg:gap-x-12">
  {menus.map((menu) => (
    <Link
      key={menu.title}
      href={menu.href}
      className="text-sm font-bold leading-6 text-white"
    >
      {menu.title}
    </Link>
  ))}
</div>
```

### 5. 实现右侧菜单和展开收起图标按钮

右侧菜单展开收起图标按钮的样式也比较简单，只需要加上 `flex-end` 让其靠右。展开收起图标按钮只有在移动端可见，并且我们需要加上点击事件来控制移动端菜单列表是否展开状态，这里使用简单的 `useState` 来控制即可。

```tsx
import { Bars3Icon } from "@heroicons/react/24/outline"; // 引入图标
import { useState } from "react";

export default function Navbar() {
const [mobileMenuOpen, setMobileMenuOpen] = useState(false);

return (
  // ...省略以上部分代码 
    {/* 右侧菜单 */}
    <div className="flex flex-1 justify-end">
      <a
        href="https://play.google.com/store/apps/details?id=com.iogame.chopio&hl=en"
        className="text-sm font-semibold leading-6 text-white"
      >
        <span className="sr-only">Download</span>
        Download
      </a>
    </div>
          
    {/* 移动端可见的右侧展开收起图标按钮 */}
    <div className="flex lg:hidden">
      <button
        type="button"
        className="inline-flex items-center justify-center rounded-md p-2.5 text-white"
        onClick={() => setMobileMenuOpen(true)}
      >
        <span className="sr-only">Open main menu</span>
        <Bars3Icon className="h-8 w-8" aria-hidden="true" />
      </button>
    </div>
  )
}
```

### 6. 实现移动端的菜单列表

这里我们使用[Headless UI Dialog](https://headlessui.com/react/dialog) 组件来展示移动端的菜单列表，因为其需要基于展示收起状态切换显示，也可以自行实现这一组件。样式方面需要给他在 `lg` 下隐藏

```tsx
import { Dialog } from "@headlessui/react";

// ...省略部分代码
<Dialog
  as="div"
  className="lg:hidden"
  open={mobileMenuOpen}
  onClose={setMobileMenuOpen}
>
  <Dialog.Panel className="fixed right-0 top-16 z-10 w-full overflow-y-auto bg-black-200 p-4">
    <div className="space-y-4">
      {menus.map((menu) => (
        <Link
          key={menu.title}
          href={menu.href}
          className="block p-3 text-center text-base font-bold leading-7 text-white"
        >
          {menu.title}
        </Link>
      ))}
    </div>
  </Dialog.Panel>
</Dialog>
```

## 参考链接

- [Next.js Getting Started](https://nextjs.org/docs/getting-started/installation)
- [Tailwind CSS - Responsive Design](https://tailwindcss.com/docs/responsive-design)
- [Tailwind Components Headers](https://tailwindui.com/components/marketing/elements/headers)
- [Headless UI Dialog](https://headlessui.com/react/dialog)
- [Radix UI](https://www.radix-ui.com/)