---
title: 使用 Turborepo 管理 Monorepo 项目
description: 
date: 2024-03-24
tag: Web
---

# 使用 Turborepo 管理 Monorepo 项目

[Monorepo](https://zh.wikipedia.org/wiki/Monorepo) 开发方式对比多个项目的开发方式已经有很多优秀的最佳实践和示例、配套的工具支持，在多个开源项目、大厂实践中落地。其优势和缺点已经有不少其他文章介绍过了，不是本文重点就不单独介绍了。

本文介绍使用 [Turborepo](https://turbo.build/repo) 管理 `Monorepo` 项目。

`Turborepo` 是目前众多流行的 `Monorepo` 管理工具中一种，其优势在于:

1. 多任务并行处理
2. 更快的增量构建
3. 云缓存
4. 任务管道
5. 基于约定的配置
6. ...

## 创建一个新的 Monorepo 项目

### 使用 `create-turbo` 创建项目

```bash
npx create-turbo@latest
```

`create-turbo` 会基于 [示例项目](https://github.com/vercel/turbo/tree/main/examples) 模版来生成项目代码。

创建项目成功后，可以看到提示我们成功创建了以下项目：

```bash
>>> Created a new Turborepo with the following:

apps
 - apps/docs
 - apps/web
packages
 - packages/eslint-config
 - packages/typescript-config
 - packages/ui
```

常见的开发约束，我们一般会将完整的应用放在 `apps` 目录下，可以同时存在多个应用。将应用依赖或者可以共享代码的部分抽取分装成多个包放在 `packages` 目录下。

其中的每个项目都有自己的 `package.json` ，其中可以定义这个项目的运行、打包命令、依赖的包等。在应用 `apps/docs` 和 `apps/web` 中，我们也可以看到他们添加了来自 `packages/ui` 的依赖，可以使用其提供的组件。

### 启动应用

`turbo.json` 中已经帮我们定义好了一些常用的 [pipeline](https://turbo.build/repo/docs/core-concepts/monorepos/running-tasks#turborepo-can-multitask)，根据这些配置可以执行不同的任务，可以并行、串行执行多个任务。以本地启动应用示例：

```json
{
  "pipeline": {
    "dev": {
      "cache": false,
      "persistent": true
    }
  }
}
```

```bash
turbo dev
```

### 启动某一个应用

```bash
turbo dev --filter=docs
```

### 打包项目

`turbo.json` 中定义打包任务，并且定义打包产物地址可以让 `turborepo` 帮助缓存打包任务，后续打包可以充分利用缓存机制加快打包速度。

```json
{
  "pipeline": {
    "build": {
      "outputs": [".next/**", "!.next/cache/**"]
    }
  }
}
```

```bash
turbo build
```

## 参考链接

- [Turborepo](https://turbo.build/repo)
- [Creating a new monorepo](https://turbo.build/repo/docs/getting-started/create-new)
- [Monorepo](https://zh.wikipedia.org/wiki/Monorepo)
- [monorepo.tools](https://monorepo.tools/)
