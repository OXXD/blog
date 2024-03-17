---
title: 使用 Prisma 删除关联记录
description: 
date: 2023-03-17
tag: Uncategorized
---

# 使用 Prisma 删除关联记录

使用 `Prisma` 这个 `ORM` 删除记录时会遇到存在关联记录的情况。一般的做法是同时删除关联的记录，可以保证数据的完整、避免冗余数据。

本文介绍使用 `Prisma` 删除记录时如何同时删除关联记录。

## 方案一：使用事务

使用两次 `SQL` 任务来执行删除，可以删除指定的记录和关联的记录。这里我们使用事务可以保证所有 `SQL` 都一起执行成功，避免只删除其中某一项。

```typescript
const deletePosts = prisma.post.deleteMany({
  where: {
    authorId: 7,
  },
})

const deleteUser = prisma.user.delete({
  where: {
    id: 7,
  },
})

const transaction = await prisma.$transaction([deletePosts, deleteUser])
```

## 方案二：使用 `Referential actions`

[Referential actions](https://www.prisma.io/docs/orm/prisma-schema/data-model/relations/referential-actions#cascade) 是 `Prisma` 在 `2.26.0` 版本之后提供的一个功能，可以在 `schema.prisma` 中定义更新或者删除记录时关联的记录需要执行的操作。

用法如下，在 `schema.prisma` 中定义 `onDelete` 需要执行什么操作。除了 `Cascade` 同时删除关联记录，还有其他选项 `Restrict`, `NoAction`, `SetNull`, `SetDefault`

```diff
model Post {
  id       Int    @id @default(autoincrement())
  title    String
++  author   User   @relation(fields: [authorId], references: [id], onDelete: Cascade)
--  author   User   @relation(fields: [authorId], references: [id])
  authorId Int
}

model User {
  id    Int    @id @default(autoincrement())
  posts Post[]
}
```

需要注意，更新 `schema` 之后需要执行 `npx prisma migrate dev` 来生成数据库迁移记录或者 `npx prisma db push` 来同步数据库。

## 参考链接

- [Cascading deletes (deleting related records)](https://www.prisma.io/docs/orm/prisma-client/queries/crud#cascading-deletes-deleting-related-records)
- [Referential actions](https://www.prisma.io/docs/orm/prisma-schema/data-model/relations/referential-actions#cascade)
