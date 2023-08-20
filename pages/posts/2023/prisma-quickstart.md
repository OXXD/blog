---
title: 类型安全的 ORM 工具 Prisma 入门尝试
description: 
date: 2023-03-12
tag: 
---

# 类型安全的 ORM 工具 Prisma 入门尝试

作为一个前端程序员，在项目开发过程中实际上是很少与数据库有直接的交互和关联操作的，大多都是与后端提供的 API 接口交互。作为一个 Web 开发，数据库相关的知识也是必不可少的。

从数据模型到数据交互到接口 API 到页面数据展示，这一完整链路中每一层都有自己的实现细节。如果有一种直接通过数据模型生成 API 或者对应的数据交互 API，那么开发效率和质量都能得到保障。

搜寻过相关资料，也在一些文章中阅读过关于 [Prisma](https://www.prisma.io/)、[GraphQL](https://graphql.org/) 可以提供了类型安全的访问数据库的方式，本文决定基于 [Prisma Quickstart doc](https://www.prisma.io/docs/getting-started/quickstart)，尝试学习 `Prisma` 的使用。

## 先来问问 ChatGPT，Prisma 是什么

凑个热闹，先来问问 `ChatGPT`，看能不能得到一些有效答案。

![prisma-chat-gpt-1](/images/minigame/prisma-chat-gpt-1.png)
![prisma-chat-gpt-2](/images/minigame/prisma-chat-gpt-2.png)
![prisma-chat-gpt-3](/images/minigame/prisma-chat-gpt-3.png)

## `Prisma` 的入门使用

### 创建项目

创建文件夹

```bash
mkdir hello-prisma
cd hello-prisma
```

添加 Typescript 并初始化

```bash
npm init -y
npm install typescript ts-node @types/node --save-dev
npx tsc --init
```

安装 `Prisma CLI`

```bash
npm install prisma --save-dev
```

初始化 `Prisma`，以下命令会创建 `prisma` 文件夹和和相应的 `schema` 文件

```bash
npx prisma init --datasource-provider sqlite
```

### 定义数据模型

在 `prisma/schema.prisma` 文件中定义我们的数据模型。该数据模型文件于数据库中的数据模型可对应，并且可用于生成 `Prisma Client`(`Prisma Client` 是一种类型安全的 API，用于与数据库进行交互)。

```prisma
model User {
  id    Int     @id @default(autoincrement())
  email String  @unique
  name  String?
  posts Post[]
}

model Post {
  id        Int     @id @default(autoincrement())
  title     String
  content   String?
  published Boolean @default(false)
  author    User    @relation(fields: [authorId], references: [id])
  authorId  Int
}
```

### 生成数据库数据模型

通过 `Prisma schema` 定义数据模型之后，我们还并没有数据库，通过 `prisma migrate` 可以直接生成数据库的 `User` 表和 `Post` 表。

```bash
npx prisma migrate dev --name init
```

命令执行完成之后会在 `prisma` 文件夹下生成 `SQLite` 数据库文件 `dev.db` 和在 `prisma/migrations` 文件夹下对应的 `SQL` 文件。

后续如果 `Prisma schema` 中的数据模型有变更也可以继续使用该命令来同步数据模型。

### 通过 Prisma Client 来执行数据库交互

我们可以使用 `Prisma Client` 的 `API` 来进行数据交互，由于之前定义好数据模型，所有操作都是类型安全并且有自动补全的。

![prisma-type](/images/minigame/prisma-type.png)
![prisma-type-2](/images/minigame/prisma-type-2.png)

#### 创建记录

创建新文件

```bash
touch script.ts
```

使用 `Prisma Client` 来创建一条用户记录

```typescript
import { PrismaClient } from '@prisma/client'

const prisma = new PrismaClient()

async function main() {
  const user = await prisma.user.create({
    data: {
      name: 'Alice',
      email: 'alice@prisma.io',
    },
  })
  console.log(user)
}

main()
  .then(async () => {
    await prisma.$disconnect()
  })
  .catch(async (e) => {
    console.error(e)
    await prisma.$disconnect()
    process.exit(1)
  })
```

执行代码

```bash
npx ts-node script.ts
```

#### 获取记录

```typescript
async function findAllUsers() {
  const users = await prisma.user.findMany();
  console.log("findAllUsers: \n", users);
}
```

#### 创建有关联的记录

```typescript
async function createUserWithRelation() {
  const user = await prisma.user.create({
    data: {
      name: "Bob",
      email: "bob@prisma.io",
      posts: {
        create: {
          title: "Hello World",
        },
      },
    },
  });
  console.log("createUserWithRelation: \n", user);
}
```

#### 获取有关联的记录

```typescript
async function findUserWithRelation() {
  const usersWithPosts = await prisma.user.findMany({
    include: {
      posts: true,
    },
  });
  console.log("findUserWithRelation: \n");
  console.dir(usersWithPosts, { depth: null });
}
```

### 在 Prisma Studio 中可以查看数据

```bash
npx prisma studio
```

![prisma-studio](/images/minigame/prisma-studio.png)
![prisma-studio-2](/images/minigame/prisma-studio-2.png)

## 与 Next.js, Express 等结合使用

目前前端社区、React 社区有一股全栈开发的趋势，比如 `React Server Component` 的推出可以在服务端进行数据交互后直接输出 `React` 组件，`Next.js`、`Remix` 等框架也都是支持全栈开发，这一类框架和 `Prisma` 结合使用，可以使开发体验更加流畅，全程的数据获取交互操作都是数据安全的，类型和部分 API 都能够自动生成。

与 `Next.js`、`Remix` 等框架的使用后续再整理一篇文章介绍如何基于这些技术开发一个全栈 App。

## 参考链接

- [Prisma](https://www.prisma.io/)
- [Prisma Quickstart doc](https://www.prisma.io/docs/getting-started/quickstart)，本文主要参考自这篇官方起步指南。
- [示例项目源码](https://github.com/OXXD/prisma-demo)
- [Prisma example projects](https://github.com/prisma/prisma-examples/)