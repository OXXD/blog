---
title: 使用 Turborepo 管理的 Monorepo 项目中如何共享代码
description: 
date: 2024-04-14
tag: Web
---

# 使用 Turborepo 管理的 Monorepo 项目中如何共享代码

在[上篇文章]((https://juejin.cn/post/7352091152583999499))中介绍了 `Turborepo` 管理 `Monorepo` 项目的基本使用。其中提到按照常见的开发约束，我们一般会将完整的应用放在 `apps` 目录下，将共享代码的部分抽取分装成多个包放在 `packages` 目录下。在实际项目开发中，当项目增多和需求复杂，在不同项目中复用和共享公用代码是十分常见的最佳实践。

本文介绍在使用 `Turborepo` 管理的 `Monorepo` 项目中如何共享代码。主要参考以下几篇官方文档：

- [Sharing Code in a monorepo – Turborepo](https://turbo.build/repo/docs/handbook/sharing-code)
- [Internal Packages – Turborepo](https://turbo.build/repo/docs/handbook/sharing-code/internal-packages)
- [Publishing Packages – Turborepo](https://turbo.build/repo/docs/handbook/publishing-packages)

## 内部库和外部库（Internal Packages, External packages）

按照 [Turborepo 文档](https://turbo.build/repo/docs/handbook/sharing-code#next-steps) ，会先将公用代码区分为内部库和外部库（[Internal Packages](https://turbo.build/repo/docs/handbook/sharing-code/internal-packages), [External Packages](https://turbo.build/repo/docs/handbook/publishing-packages)）

- 内部库（Internal Packages），指只会在同一个 `Monorepo` 项目中使用的，通常很简单搭建起来，并且不需要经过打包（打包由引用的项目打包时一起打包）、发布版本环节提供给外部项目使用，这是本文主要讨论的使用方式。
- 外部库（External packages），通常会经过打包、发布版本、推送到一个集中的 `npm` 仓库提供给不同的项目使用，在跨项目使用的场景下使用，比如团队内基础组件库

## 搭建一个内部库

### 1. 创建项目

```bash
mkdir packages/math-helpers
```

`package.json` 申明内部库名称，以及如果有需要的第三方依赖

```json
{
  "name": "math-helpers",
  "version": "0.0.1",
  "dependencies": {
    "typescript": "latest"
  }
}
```

创建 `src` 文件夹，并且导出公共代码。

如在 `packages/math-helpers/src/index.ts` 中创建 `add`, `subtract` 示例函数

```typescript
export const add = (a: number, b: number) => {
  return a + b;
};

export const subtract = (a: number, b: number) => {
  return a - b;
};
```

另外由于是 `Typescript` 项目，不要忘记配置 `tsconfig.json`

`packages/math-helpers/tsconfig.json`

```json
{
  "compilerOptions": {
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true,
    "isolatedModules": true,
    "module": "ESNext",
    "moduleResolution": "Bundler",
    "preserveWatchOutput": true,
    "skipLibCheck": true,
    "noEmit": true,
    "strict": true
  },
  "exclude": ["node_modules"]
}
```

这样一个最简单的内部库即创建完成。

### 2. 定义导出文件路径

需要在 `package.json` 中明确申明内部库文件的导出路径，这样使用内部库的应用才能找到对应文件。如定义 `exports` 这个字段，所有文件都从 `./src/index.ts` 引入。

```json
{
  "name": "math-helpers",
  "exports": {
    ".": "./src/index.ts"
  },
  "dependencies": {
    "typescript": "latest"
  }
}
```

### 3. 应用中使用内部库

首先在 `apps/web/package.json` 中加入依赖，这里由于是项目内文件，直接填 `*` 即可

```json
{
  "dependencies": {
    "math-helpers": "*"
  }
}
```

注意 `pnpm` 和 `npm`, `yarn` 不同包管理工具对于依赖引用的区别

```json
{
  "dependencies": {
    "math-helpers": "workspace:*"
  }
}
```

使用的时候，直接从包名引入，就和我们常见的 `npm` 包一样使用。

```typescript
import { add } from "math-helpers";
 
add(1, 2);
```

### 4. 配置打包工具识别和打包引用的内部库

如果我们直接启动应用，一般会遇到报错，比如 `Next.js` 的报错：

```bash
../../packages/math-helpers/src/index.ts
Module parse failed: Unexpected token (1:21)
You may need an appropriate loader to handle this file type,
currently no loaders are configured to process this file.
See https://webpack.js.org/concepts#loaders
> export const add = (a: number, b: number) => {
|   return a + b;
| };
```

我们需要配置打包工具，让他能正确识别和打包引用的内部库，不同的打包工具配置不一样，如 `Next.js` 中的 [transpilePackages 配置](https://nextjs.org/docs/app/api-reference/next-config-js/transpilePackages)

```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
  transpilePackages: ['math-helpers'],
};
 
module.exports = nextConfig;
```

`Umi` 项目中如果开启了 [mfsu](https://umijs.org/docs/guides/mfsu)，建议将内部库排除，避免内部库代码更新由于 `mfsu 缓存` 而未生效。

```javascript
{
  mfsu: {
    strategy: 'normal',
    exclude: ['math-helpers'],
  },
}
```

`Vite` 项目模式是会打包依赖的，所以不需要额外配置。

## 实践中遇到的问题

`Umi` 框架下暂时不支持解析只定义了 `exports` 导出的依赖。

解决方法：`main` 字段中定义导出文件路径，如：

```json
{
  "name": "math-helpers",
  "main": "./src/index.ts",
  "exports": {
    ".": "./src/index.ts"
  },
}
```

实际上，依赖的路径解析是一个非常复杂的话题，本文不详细展开。具体可以参考 [Node.js 文档](https://nodejs.org/api/packages.html#package-entry-points)

## 参考链接

- [使用 Turborepo 管理 Monorepo 项目](https://juejin.cn/post/7352091152583999499)
- [Turborepo](https://turbo.build/repo)
- [Sharing Code in a monorepo – Turborepo](https://turbo.build/repo/docs/handbook/sharing-code)
- [Internal Packages – Turborepo](https://turbo.build/repo/docs/handbook/sharing-code/internal-packages)
- [Publishing Packages – Turborepo](https://turbo.build/repo/docs/handbook/publishing-packages)
- [Node.js 文档 - Package entry points](https://nodejs.org/api/packages.html#package-entry-points)
