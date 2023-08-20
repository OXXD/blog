---
title: 使用 patch-package 为 npm 依赖打补丁
description: 
date: 2023-08-13
tag: 
---

# 使用 patch-package 为 npm 依赖打补丁

项目中会引用多个不同的 NPM 依赖来辅助项目开发。然而，由于依赖包的版本更新和维护不一致，以及可能存在的漏洞，我们在使用这些依赖包时可能会遇到一些问题。当我们发现某个依赖包存在漏洞或者与我们的项目不兼容时，通常的做法是等待开发者修复问题并发布新版本。但是在等待的过程中，我们的项目可能会受到一定的影响。

[patch-package](https://github.com/ds300/patch-package) 是一个解决这类问题的工具，它允许我们在不修改依赖包源码的情况下，为依赖包打补丁，修复其中的问题。在以下场景中特别有用：修复漏洞、适配兼容性、自定义修改。

## 使用

### 1. 安装

```bash
npm i -D patch-package
```

`package.json` 中定义 `postinstall` 钩子，以自动应用补丁

```diff
 "scripts": {
+  "postinstall": "patch-package"
  }
```

### 2. 在 node_modules 中修改需要打补丁的依赖代码

```bash
vim node_modules/some-package/brokenFile.js
```

### 3. 创建补丁

```bash
npx patch-package package-name
```

创建成功后会在项目根目录生成 patches 文件夹及补丁文件

![patch-package](/images/minigame/patch-package.png)

## 参考链接

- [patch-package](https://github.com/ds300/patch-package)
- [手把手教你使用patch-package给npm包打补丁](https://juejin.cn/post/6962554654643191815)