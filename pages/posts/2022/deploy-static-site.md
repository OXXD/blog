---
title: 快速部署前端项目的几种方式
description: 本文介绍一些常见的快速部署前端项目的几种方式（或者说是服务），提供部署服务、二级域名、自定义域名、部署预览、CI/CD 等功能，适用于部署简单官网、个人网站、文档站点等不和后端结合的场景。
date: 2022-12-03
tag: Tooling
---

# 快速部署前端项目的几种方式

常见的前端项目最后都是或者会打包生成 `HTML` 和静态资源文件，可以独立部署到任意静态资源服务器之后简单配置就能访问。本文介绍一些常见的快速部署前端项目的几种方式（或者说是服务），提供部署服务、二级域名、自定义域名、部署预览、CI/CD 等功能，适用于部署简单官网、个人网站、文档站点等不和后端结合的场景。

这里以一个简单的 `Vite` 模版项目示例，该项目存放在 [Github](https://github.com/OXXD/vite-demo)

## Vercel

[Vercel](https://vercel.com/) 和 [Netlify](netlify.com) 是这些服务中比较常见和操作简单功能齐全的两个选项。这里先介绍 `Vercel` 如何部署。

1. [创建新项目](https://vercel.com/new)，这里推荐选择从 `Github` 引入，后续代码推送可以直接触发部署，不需要额外配置

![vercel-step-1](/images/minigame/vercel-step-1.png)
![vercel-step-1-1](/images/minigame/vercel-step-1-1.png)

2. 新建项目成功导入代码成功后，点击部署按钮

![vercel-step-2](/images/minigame/vercel-step-2.png)

3. 等待 `Vercel` 进行打包和部署、分配域名，一段时间后就可以看到项目部署成功

![vercel-step-3](/images/minigame/vercel-step-3.png)

4. 直接访问部署域名即可，也可以于设置中修改域名。后续代码推送到 `Github` 的主分支会自动部署正式版，推送到其他分支会部署预览版。

### Vercel 命令行

`Vercel` 也提供命令行操作。

```bash
# 全局安装
npm i -g vercel

# 初始化一个项目
vercel init vite

# 部署
vercel deploy
```

## Netlify

`Netlify` 的部署方式与 `Vercel` 基本相同

1. [创建新项目](https://app.netlify.com/start)，选择从 `Github` 引入

![netlify-step-1](/images/minigame/netlify-step-1.png)
![netlify-step-2](/images/minigame/netlify-step-2.png)

2. 配置部署参数和部署命令等

![netlify-step-3](/images/minigame/netlify-step-3.png)

3. 稍等一会就会看到部署成功，可以直接访问，也可以再进行配置域名、设置项目等操作

![netlify-step-4](/images/minigame/netlify-step-4.png)

### Netlify 命令行

同样，`Netlify` 也提供命令行操作，可以选择从终端来部署项目

```bash
# 全局安装
$ npm install -g netlify-cli

# 初始化项目
$ ntl init

# 部署
$ ntl deploy
```

## Surge

另一个简单的服务是 [Surge](https://surge.sh/)，只需要一两行简单的命令即可部署项目，可以免去界面操作

```bash
# 全局安装
npm install --global surge

# 部署项目，跟着指导完成
surge
```

下次需要更新部署项目，也只需要简单的命令行操作

```bash
# 重复部署需要带上指定域名
surge --domain [之前生成的域名]

# 或者将域名保存到 CNAME 文件
echo vite-demo.surge.sh > CNAME
surge
```

## Github Pages 和 Gitlab Pages

除了以上方案，其他的选择也有很多，比如 `Github Pages` 和 `GitLab Pages` 也提供一些类似的功能，并且由于可以和项目代码放在一起不需要再选择其他服务存在优势，但是会需要一些配置和相比以上方案操作繁琐容易出错，这里就不详细介绍。如果觉得这个场景更适合你也可以参考文档研究。

## 小技巧

本地也有一些可以快速启动服务预览项目的一些工具。

`Node.js` 环境下常见的是 [serve](https://github.com/vercel/serve) 和 [http-server](https://github.com/http-party/http-server)

```bash
# 全局安装
npm i -g serve
# 启动
serve
```

```bash
# 全局安装
npm i -g http-server
# 启动
http-server
```

如果有安装 `Python` 环境（`macOS` 一般都带有 `Python` 环境）

```bash
python -m http.server [<portNo>]
```

## 参考链接

- [Vercel](https://vercel.com/)
- [Netlify](netlify.com)
- [Surge](https://surge.sh/)
- [Deploying a Static Site](https://vitejs.dev/guide/static-deploy.html)