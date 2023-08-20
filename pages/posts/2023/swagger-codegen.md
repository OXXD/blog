---
title: 根据 Swagger 文档生成前端网络请求代码和 Typescript 类型申明
description: 
date: 2023-06-11
tag: Tooling
---

# 根据 Swagger 文档生成前端网络请求代码和 Typescript 类型申明

在具体开发中，前后端联调一直都是比较麻烦的事情，后端需要维护接口文档，前端需要根据接口文档编写网络请求代码和类型申明，接口变更时需要修改对应代码。

目前有不少工具都能简化这一流程，根据后端代码直接生成 `Swagger` 文档，根据 `Swagger` 文档直接生成前端请求代码，经过实际测试和使用，发现 [Ant Design Pro](https://pro.ant.design/zh-CN/docs/openapi) 中的 [@umijs/openapi](https://github.com/chenshuai2144/openapi2typescript) 这一个工具使用更加简单，生成的代码更简单易读贴合实际项目使用。

接下来以 [Swagger Petstore](https://petstore.swagger.io/) 这一接口文档介绍使用方式。

## Ant Design Pro 中使用

`Ant Design Pro` 中已经集成 `@umijs/plugin-openapi`，使用时只需要配置接口文档地址和生成代码地址即可。参考文档 [Ant Design Pro OpenApi 文档](https://pro.ant.design/zh-CN/docs/openapi)

在 `config/config.ts` 中配置 openAPI 的相关配置

```ts
openAPI: {
   requestLibPath: "import { request } from '@umijs/max",
   // 或者使用在线的版本
   // schemaPath: "https://petstore.swagger.io/v2/swagger.json",
   schemaPath: join(__dirname, 'oneapi.json'),
   mock: false,
   projectName: 'swagger',
 }
```

在 `package.json` 的 `scripts` 中增加一个命令

```json
"openapi": "max openapi",
```

执行命令生成代码

```bash
npm run openapi
```

生成的代码如图，可以看到代码结构清晰，每个接口都导出一个函数，并且有完整的类型声明。

![swagger-codegen](/images/minigame/swagger-codegen.png)

## 其他项目中使用

在不是使用 `Ant Design Pro` 搭建的项目，也可以手动安装和使用这一工具。使用方法基本一致。

1. 安装

```bash
npm i --save-dev @umijs/openapi
```

2. 新建文件 `openapi.config.js` 并配置

```js
const { generateService } = require('@umijs/openapi');

generateService({
  requestLibPath: "import request from '@/utils/request'",
  schemaPath: 'https://petstore.swagger.io/v2/swagger.json',
  serversPath: './src/services',
  projectName: 'swagger',
});
```

更多配置项见 [@umijs/openapi](https://github.com/chenshuai2144/openapi2typescript)

3. 在 `package.json` 的 `scripts` 中增加一个命令

```json
"openapi": "node openapi.config.js",
```

```bash
npm run openapi
```

## 注意事项

实际使用时可能会遇到一些问题，记录注意事项如下：

1. `OpenAPI` 建议使用 `3.0` 版本，`2.0` 可能容易出现格式转化报错等问题
2. 接口文档中可能会有中文，导致生成的函数名是拼音，这个需要自己权衡和与后端协商

## 其他工具

其他常见的工具还有以下这些，但是可能会有安装需要依赖复杂或者生成代码复杂等原因没有采用。

不同的工具在不同项目中有各自的适用场景，以及自身的优缺点，实际使用时需考虑项目是否适用，以及做充分的测试。

- [OpenAPI Generator](https://openapi-generator.tech/)
- [Swagger Codegen](https://swagger.io/tools/swagger-codegen/)
- [Swagger Editor](https://swagger.io/tools/swagger-editor/)

## 参考链接

- [Ant Design Pro OpenApi 文档](https://pro.ant.design/zh-CN/docs/openapi)
- [OpenAPI Generator](https://openapi-generator.tech/)
- [Swagger Codegen](https://swagger.io/tools/swagger-codegen/)
- [Swagger Editor](https://swagger.io/tools/swagger-editor/)
- [Swagger Petstore](https://petstore.swagger.io/)
- [Swagger Petstore - OpenAPI 3.0](https://petstore3.swagger.io/)
- [@umijs/openapi](https://github.com/chenshuai2144/openapi2typescript)