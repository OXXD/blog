---
title: 使用 zx 编写脚本快速完成任务
description: 
date: 2022-12-11
tag: Tooling
---

# 使用 zx 编写脚本快速完成任务

我们通常会开发一些简易的脚本来完成一些重复工作，`bash` 和 `Node.js` 都是不错的选择，`Node.js` 一些编程语言特性和 `JavaScript` 已经是前端程序员必备技能在开发脚本工具时会有一些优势。本文介绍一个 Google 开源的工具 [zx](https://github.com/google/zx) 可以让我们更方便快速高效的使用 `Node.js` 编写脚本工具。

## 安装

```bash
npm i -g zx
```

## 示例

```javascript
#!/usr/bin/env zx

await $`cat package.json | grep name`

let branch = await $`git branch --show-current`
await $`dep deploy --branch=${branch}`

await Promise.all([
  $`sleep 1; echo 1`,
  $`sleep 2; echo 2`,
  $`sleep 3; echo 3`,
])

let name = 'foo bar'
await $`mkdir /tmp/${name}`
```

通过这个简单的示例我们可以看到首先我们需要给文件头部添加 `shebang` 让改脚本执行时候知道要使用 `zx` 来执行而不是其他 `shell` 环境。

并且 `zx` 帮我们自动全局导入了一些方法不需要我们手动导入（比如 `$ 执行命令`，`cd 切换路径`，`fetch 网络请求` 等 ），还有许多其他协助方法。

### $`command`

将命令放在 `${...}` 中可以执行系统上的命令

```javascript
let name = 'foo & bar'
await $`mkdir ${name}`
```

### cd()

改变当前路径

```javascript
cd('/tmp')
await $`pwd` // => /tmp
```

### fetch()

网络请求，不需要额外安装 `node-fetch` 或者其他包。`API` 也同 `fetch` 一致

```javascript
let resp = await fetch('https://medv.io')
```

### sleep()

```javascript
await sleep(1000)
```

其他更多可以见[文档](https://github.com/google/zx#functions)

## 使用

### 1. 文件头部添加 shebang

```bash
#!/usr/bin/env zx
```

### 2. 修改文件权限，并且直接运行文件执行脚本

```bash
# 修改文件权限
chmod +x ./script.mjs
# 后续可以直接运行改文件执行脚本
./script.mjs
```

### 3. 或者通过全局 zx 命令执行脚本

```bash
zx ./script.mjs
```

## 实际示例

这里我们用一个简单的需求来写一个示例脚本。需求为根据域名列表，重复调取远程接口，每轮接口请求中可以设置间隔时间（接口这里是示例代码并不存在）。使用 `zx` 可以快速实现。

```javascript
#!/usr/bin/env zx

const domains = [
  'a.com', 
  'b.com'
]

const seconds = 10000

const refreshCdn = async (item) => {
  echo`${item} 刷新 cdn`
  const response = await fetch(`http://localhost:3000/api/refresh?domain=${item}`)
  const res = await response.json()
  echo(item, res.success)
}

for (let i = 0; i < domains.length; i++){       
  setTimeout(function () {   
    refreshCdn(domains[i])
  }, i * seconds)
}
```

然后执行 `zx ./script.mjs` 即可

## 参考链接

- [zx](https://github.com/google/zx)