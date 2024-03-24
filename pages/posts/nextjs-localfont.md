---
title: Next.js 如何使用本地自定义字体
description: 
date: 2024-03-24
tag: React, Next.js
---

# Next.js 如何使用本地自定义字体

## 本地字体

使用本地字体文件十分简单，只需要指定好字体文件的加载路径，然后通过 `className` 设置给需要自定义字体的元素即可。

`app/layout.tsx`

```tsx
import localFont from 'next/font/local'
 
// Font files can be colocated inside of `app`
const myFont = localFont({
  src: './my-font.woff2',
})
 
export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en" className={myFont.className}>
      <body>{children}</body>
    </html>
  )
}
```

## 结合 Tailwind CSS

首先加载自定义字体文件，并且通过 `variable` 属性指定 `CSS variable`

`app/layout.tsx`

```tsx
export const myFont = localFont({
  src: './my-font.woff2',
  variable: '--font-my',
});

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html
      lang="en"
      className={cn(
        myFont.variable,
      )}
    >
      <body>
       {children}
      </body>
    </html>
  );
}
```

然后在 `Tailwind` 配置中配置扩展自定义字体

`tailwind.config.js`

```js
/** @type {import('tailwindcss').Config} */
module.exports = {
  theme: {
    extend: {
      fontFamily: {
        my: ['var(--font-my)'],
      },
    },
  },
}
```

在需要使用该自定义字体的地方都可以通过 `className="font-my"` 使用

## 参考链接

- [Optimizing: Fonts | Next.js](https://nextjs.org/docs/app/building-your-application/optimizing/fonts#local-fonts)
- [Components: Font | Next.js](https://nextjs.org/docs/app/api-reference/components/font)