---
title: 使用 cypress 自动化测试（一）：安装
description: 开发完成后都需要对项目进行测试，除了人工根据测试用例测试外，我们也可以使用一些自动化测试工具来完成测试任务，可以解放人工，也可以自动化、重复执行。本文介绍该类工具中 cypress 的安装
date: 2022-12-18
tag: 
---

# 使用 cypress 自动化测试（一）：安装

开发完成后都需要对项目进行测试，除了人工根据测试用例测试外，我们也可以使用一些自动化测试工具来完成测试任务，可以解放人工，也可以自动化、重复执行。本文介绍该类工具中 [cypress](https://www.cypress.io/) 的安装，后续会再介绍其如何使用。

## 使用 create-vue 脚手架

`create-vue` 是 `Vue 3` 中官方推荐的项目初始化脚手架，其中就包含了是否安装 `cypress` 的选项。如果我们打算新建一个 `Vue 3` 的项目可以考虑使用 `create-vue` 脚手架来生成项目。

### 使用方式

1. 使用 `create-vue` 初始化项目

```bash
npm init vue@latest
```

2. 选择 `cypress`

```bash
✔ Project name: … <your-project-name>
✔ Add TypeScript? … No / Yes
✔ Add JSX Support? … No / Yes
✔ Add Vue Router for Single Page Application development? … No / Yes
✔ Add Pinia for state management? … No / Yes
✔ Add Vitest for Unit testing? … No / Yes
✔ Add Cypress for both Unit and End-to-End testing? … No / Yes
✔ Add ESLint for code quality? … No / Yes
✔ Add Prettier for code formatting? … No / Yes

Scaffolding project in ./<your-project-name>...
Done.
```

3. 进入项目安装依赖

```bash
> cd <your-project-name>
> npm install
```

注意可能因为网络情况，安装会容易出现失败。最好全程保持网络访问通畅。

### 启动测试

```json
{
  "scripts": {
    "test:e2e": "start-server-and-test preview :4173 'cypress run --e2e'",
    "test:e2e:dev": "start-server-and-test 'vite dev --port 4173' :4173 'cypress open --e2e'",
  },
}
```

可以看到 `create-vue` 脚手架帮我安装好了 `cypress` 和启动它的命令，也提供了一个案例。我们可以执行这两个命令来让 `cypress` 对打包后的产物进行自动化测试活着打开 `cypress` 的界面对当前开发中的页面进行测试。

#### 测试打包产物

测试打包产物需要先打包项目。

```bash
npm run build
npm run test:e2e
```

```bash
> vue-project@0.0.0 test:e2e
> start-server-and-test preview :4173 'cypress run --e2e'

1: starting server using command "npm run preview"
and when url "[ 'http://localhost:4173' ]" is responding with HTTP status code NaN
running tests using command "cypress run --e2e"


> vue-project@0.0.0 preview
> vite preview

  ➜  Local:   http://127.0.0.1:4173/
  ➜  Network: use --host to expose
Missing baseUrl in compilerOptions. tsconfig-paths will be skipped

====================================================================================================

  (Run Starting)

  ┌────────────────────────────────────────────────────────────────────────────────────────────────┐
  │ Cypress:        12.1.0                                                                         │
  │ Browser:        Electron 106 (headless)                                                        │
  │ Node Version:   v16.15.1 (/Users/oxxd/.nvm/versions/node/v16.15.1/bin/node)                    │
  │ Specs:          1 found (example.cy.ts)                                                        │
  │ Searched:       cypress/e2e/**/*.{cy,spec}.{js,jsx,ts,tsx}                                     │
  └────────────────────────────────────────────────────────────────────────────────────────────────┘


────────────────────────────────────────────────────────────────────────────────────────────────────
                                                                                                    
  Running:  example.cy.ts                                                                   (1 of 1)


  My First Test
    ✓ visits the app root url (619ms)


  1 passing (751ms)


  (Results)

  ┌────────────────────────────────────────────────────────────────────────────────────────────────┐
  │ Tests:        1                                                                                │
  │ Passing:      1                                                                                │
  │ Failing:      0                                                                                │
  │ Pending:      0                                                                                │
  │ Skipped:      0                                                                                │
  │ Screenshots:  0                                                                                │
  │ Video:        true                                                                             │
  │ Duration:     0 seconds                                                                        │
  │ Spec Ran:     example.cy.ts                                                                    │
  └────────────────────────────────────────────────────────────────────────────────────────────────┘


  (Video)

  -  Started processing:  Compressing to 32 CRF                                                     
  -  Finished processing: /Users/oxxd/test/vue-project/cypress/videos/example.cy.ts.m    (2 seconds)
                          p4                                                                        


====================================================================================================

  (Run Finished)


       Spec                                              Tests  Passing  Failing  Pending  Skipped  
  ┌────────────────────────────────────────────────────────────────────────────────────────────────┐
  │ ✔  example.cy.ts                            680ms        1        1        -        -        - │
  └────────────────────────────────────────────────────────────────────────────────────────────────┘
    ✔  All specs passed!                        680ms        1        1        -        -        -  
```

执行完后就可以看到 `cypress` 的测试执行情况和测试结果。

#### 测试开发环境

测试开发环境只需要运行改命令，会同时启动开发服务器和 `cypress` 的界面，可以在 `cypress` 的测试界面执行测试用例。

```bash
npm run test:e2e:dev
```

![cypress-install-1](/images/minigame/cypress-install-1.png)
![cypress-install-2](/images/minigame/cypress-install-2.png)

## 手动安装

除了利用项目脚手架安装，我们也可以选择在已有项目中手动安装。

```bash
npm install cypress --save-dev
```

安装完成后执行开启命令即可打开 `cypress` 的界面

```bash
npx cypress open
```

![cypress-install-3](/images/minigame/cypress-install-3.png)

## 参考链接

- [Installing Cypress](https://docs.cypress.io/guides/getting-started/installing-cypress)
- [Creating a Vue Application](https://vuejs.org/guide/quick-start.html#creating-a-vue-application)