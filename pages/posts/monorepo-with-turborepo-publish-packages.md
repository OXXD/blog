---
title: 使用 Turborepo 管理的 Monorepo 项目跨项目时如何共享代码
description: 
date: 2024-05-19
tag: Web
---

# 使用 Turborepo 管理的 Monorepo 项目及跨项目时如何共享代码

在[上篇文章]((ttps://juejin.cn/post/7357006184475770916))中介绍了 `Turborepo` 管理 `Monorepo` 项目中可以通过内部库（[Internal Packages](https://turbo.build/repo/docs/handbook/sharing-code/internal-packages)）的方式共享代码。

如果在同一个 `Monorepo` 项目中使用的，那么内部库就足够了，但是如果遇到多个项目同时需要使用公共的代码，那么就会需要考虑外部库（[External Packages](https://turbo.build/repo/docs/handbook/publishing-packages)），相较于内部库而言，外部库会经过**打包**、**发布版本**、**推送到一个集中的 `npm` 仓库**提供给不同的项目使用，在跨项目使用的场景下使用，比如团队内基础组件库。

本文介绍使用 [Turborepo](https://turbo.build/repo) 管理的 `Monorepo` 项目跨项目时如何共享代码，主要介绍外部库的打包和发布版本方式。

## 打包项目

发布到 `npm` 仓库的包都需要提前打包，使用者不需要直接引用源码，不需要知道实现细节可以直接使用，也避免了出现不同项目打包工具和配置不一样导致的打包问题。目前主流的打包工具都支持打包出  [ECMAScript modules (esm)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules) 和 [CommonJS modules (cjs)](https://en.wikipedia.org/wiki/CommonJS)，主流的项目脚手架和打包工具也都支持引入和打包这两种格式的第三方包。

目前主流的打包工具有 `Webpack`, `Vite`, `Rollup` 等，有各自的优劣势，本文将介绍使用 [tsup](https://tsup.egoist.dev/) 打包项目，是 `Turborepo` 文档 [Bundling packages in a Monorepo – Turborepo](https://turbo.build/repo/docs/handbook/publishing-packages/bundling) 中推荐的，支持 `.js`, `.json`, `.mjs`, `.ts`, `.tsx` 格式的文件的**无配置**打包，在小项目中上手使用十分方便快速。

以下目录结构为例，我们创建了一个库 `math-helpers`，并希望将他打包后发布

```bash
├── apps
│   └── web
│       └── package.json
├── packages
│   └── math-helpers
│       ├── src
│       │   └── index.ts
│       ├── tsconfig.json
│       └── package.json
├── package.json
└── turbo.json
```

### 安装 tsup

```bash
npm i tsup -D
```

### 定义打包命令

在 `/packages/math-helpers/package.json` 中定义打包命令，直接使用 `tsup` 打包 `src/index.ts` 的入口文件，并且导出 `cjs`,`esm` 格式和自动生成的类型定义

```json
{
  "scripts": {
    "build": "tsup src/index.ts --format cjs,esm --dts"
  }
}
```

### 将打包后的产物 dist 文件配置一下

1. `.gitignore` 中忽略 `dist`，这部分不需要提交到 `Git` 项目
2. 将打包后的产物地址 `dist`，添加到 `turbo.json` 中的 `pipeline`，`Turborepo` 可以帮我们缓存打包结果，加快下次打包

```json
{
  "pipeline": {
    "build": {
      "outputs": ["dist/**"]
    }
  }
}
```

3. 在 `package.json` 中定义文件入口

```json
{
  "main": "./dist/index.js",
  "module": "./dist/index.mjs",
  "types": "./dist/index.d.ts"
}
```

更多 `tsup` 的使用方式可以参考 [文档](https://tsup.egoist.dev/)，或者参考使用其他打包工具，如 [Vite](https://vitejs.dev/)

## 发布项目

项目打包完成后，需要考虑如何发布版本到 `npm` 仓库。其中包含这几件事：

1. 版本管理，[Semantic Versioning](https://semver.org/) 还是 [Calendar Versioning](https://calver.org/)
2. 发布
3. `npm` 仓库，私有还是公开

在前端社区也已经有不少最佳实践和好用的工具来帮助我们完成发布项目这一流程。本文将介绍的是 `Turborepo` 文档 [Versioning and Publishing Packages in a Monorepo – Turborepo](https://turbo.build/repo/docs/handbook/publishing-packages/versioning-and-publishing) 中推荐的 [changesets](https://github.com/changesets/changesets) 这一工具和其推荐的发布流程。

### 安装并初始化

```bash
npm install @changesets/cli && npx changeset init
```

### 使用

初始化完成后，使用方式和流程基本就是以下三个命令。下面介绍下每个命令的作用

```bash
# Add a new changeset
changeset
 
# Create new versions of packages
changeset version
 
# Publish all changed packages to npm
changeset publish
```

#### 添加更新说明

```bash
changeset
```

会生成一次更新说明，并且保存文件在项目中，可以执行多次，会生成多次更新说明，后续会根据这些更新说明来生成 `CHANGELOG`

#### 当修改完成后，需要准备发布版本时

```bash
changeset version
```

会提供可交互的界面，选择需要发布的版本，按照 `semver` 规范，选择 `patch`, 'minor', 'major' 版本

这部分命令执行完后，会生成 `CHANGELOG`，和会自动更新 `package.json` 中的版本为正确的版本（需要提交到 `Git`），下一步即可发布到 `npm` 仓库

#### 发布到 npm 仓库

```bash
changeset publish
```

这个命令代替了  `npm publish` 这个命令，会发布到 `npm` 仓库。（注意记得发布之前需要打包！！！）

发布完成后，其他项目既可以通过 `npm install` 的方式安装使用。在公司内部项目，一般都会使用内部 `npm` 仓库，如何发布到内部 `npm` 仓库是另一个话题，会在之后介绍。

## 参考链接

- [使用 Turborepo 管理的 Monorepo 项目中如何共享代码](https://juejin.cn/post/7357006184475770916)
- [Turborepo](https://turbo.build/repo)
- [Sharing Code in a monorepo – Turborepo](https://turbo.build/repo/docs/handbook/sharing-code)
- [Internal Packages – Turborepo](https://turbo.build/repo/docs/handbook/sharing-code/internal-packages)
- [Publishing Packages – Turborepo](https://turbo.build/repo/docs/handbook/publishing-packages)
- [Bundling packages in a Monorepo – Turborepo](https://turbo.build/repo/docs/handbook/publishing-packages/bundling)
- [Versioning and Publishing Packages in a Monorepo – Turborepo](https://turbo.build/repo/docs/handbook/publishing-packages/versioning-and-publishing)
- [tsup](https://tsup.egoist.dev/)
- [changesets](https://github.com/changesets/changesets)