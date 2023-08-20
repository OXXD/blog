---
title: 如何解决 npm 安装依赖报错 ERESOLVE unable to resolve dependency tree
description: 
date: 2022-11-18
tag: 
---

# 如何解决 npm 安装依赖报错 ERESOLVE unable to resolve dependency tree

现代前端项目开发中依赖管理已经是不可或缺的一环，然后由于各种问题，如历史原因、项目缺少维护等，前端项目在依赖管理中会遇到非常多的问题。本篇文章讨论其中一种，当 `npm install` 时遇到报错 `ERESOLVE unable to resolve dependency tree` 的问题原因以及如何解决。

## 报错信息

在一个安装了 `react@18.2.0` 的项目中安装依赖 `ali-react-table`，就会出现以下错误。仔细阅读错误原因可以得知，`ali-react-table` 中使用 `peerDependencies` 定义了依赖于`react@"^16.8.0 || ^17.0.1"` 项目，和我们项目中的 `React` 版本号冲突了。虽然这里是因为 `ali-react-table` 已经疏于维护并没有更新依赖版本信息，但是我们对第三方依赖的可控性是比较低的，除了等待第三方依赖更新或者提 `PR` 等待合并之后发版，我们还有一些其他方法可以暂时解决这个问题。

```bash
npm ERR! code ERESOLVE
npm ERR! ERESOLVE unable to resolve dependency tree
npm ERR! 
npm ERR! While resolving: vite-project@0.0.0
npm ERR! Found: react@18.2.0
npm ERR! node_modules/react
npm ERR!   react@"^18.2.0" from the root project
npm ERR! 
npm ERR! Could not resolve dependency:
npm ERR! peer react@"^16.8.0 || ^17.0.1" from ali-react-table@2.6.1
npm ERR! node_modules/ali-react-table
npm ERR!   ali-react-table@"*" from the root project
npm ERR! 
npm ERR! Fix the upstream dependency conflict, or retry
npm ERR! this command with --force, or --legacy-peer-deps
npm ERR! to accept an incorrect (and potentially broken) dependency resolution.
```

## 方案一：降级

依赖规则校验是在 `npm@7` 之后引入的，我们可以降级 `Node.js` 或者 `npm` 来绕过校验就不会报错了。

```bash
nvm use 14.17.4

## or

npm i -g npm@6
```

## 方案二：-f 或者 --legacy-peer-deps

其实我们知道 `ali-react-table` 时由于疏于维护，所以没有及时更新依赖版本信息。实际测试和我们项目里的 `react@18.2.0` 是可以运行没有问题的，那么我们就可以安装的时候带上 `--force` 参数（简写 `-f`）告诉 `npm` 强制安装。

```bash
npm install -f
```

另一个参数是 `--legacy-peer-deps`, 可以不用降级 `npm` 也让 `npm install` 的行为和旧版本一样，[参考文档](https://github.com/npm/rfcs/blob/e000b367d9e595bc694893c3d845df269f9b875f/implemented/0031-handling-peer-conflicts.md#detailed-explanation)。不过这个参数实际使用效果可能依据项目存异，需要自行测试。

```bash
npm install --legacy-peer-deps
```

## 方案三：`yarn` 的 `resolutions` 或者 `npm` 的 `overrides`

实际项目中可能不仅仅存在一个以上类似 `ali-react-table` 依赖版本和项目所需要的依赖版本不一致的问题，可能会有好多依赖都会有该问题，有时候我们知道项目的依赖版本关系，可以使用 `resolutions`(只有使用 `yarn` 才能使用，[参考文档](https://classic.yarnpkg.com/lang/en/docs/selective-version-resolutions/)) 或者 `overrides`(只有 `npm@8` 以上才能使用，[参考文档](https://docs.npmjs.com/cli/v8/configuring-npm/package-json#overrides) ) 来指定、覆盖第三方包指定的依赖版本。这个参数在其他一些场景也非常有效，比如所需要的第三方依赖缺少维护了、指定的版本是有问题的版本等。

```json
{
  "name": "project",
  "version": "1.0.0",
  "dependencies": {},
  "resolutions": {
    "react": "^18.2.0"
  }
}
```

```json
{
  "overrides": {
    "react": "^18.2.0"
  }
}
```

## 总结

依赖管理现在已是前端开发中重要的一环，除了及时关注第三方依赖版本更新、大版本更新引起 `Breaking Change` 与自身项目是否兼容以外，也要针对自身项目选择合适的第三方依赖，及时更新依赖版本，避免出现依赖版本问题影响项目开发和项目运行。遇到错误时要看清错误说明找出根本错误原因，对症下药找出适合的解决方法。

## 参考链接

- [npm overrides](https://docs.npmjs.com/cli/v8/configuring-npm/package-json#overrides)
- [yarn resolutions](https://classic.yarnpkg.com/lang/en/docs/selective-version-resolutions/)