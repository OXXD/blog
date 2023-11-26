---
title: 如何实现前端页面权限和按钮权限
description:
date: 2023-11-26
tag: Web, React
---

# 如何实现前端页面权限和按钮权限

页面权限是后台系统中非常常见的需求，在前端实现页面权限和按钮权限是为了确保用户只能访问其有权访问的页面，并执行其有权执行的操作。本文介绍前端实现页面权限和按钮权限控制的流程和逻辑。

以下的代码以[Ant Design Pro 项目](https://pro.ant.design/zh-CN/docs/authority-management)为例，其中[封装](https://umijs.org/docs/max/access)了一些常见的权限处理方式，如路由菜单权限，按钮权限，`Access` 组件，`useAccess` 函数等。但是实现原理是类似的，其他框架也可参考该流程实现。

## 1. 获取用户当前权限并且存在全局状态中

首先需要从后端接口中获取到当前用户拥有的所有权限列表，并且存储在全局状态中（这个场景中使用全局状态库是一个很合适的方式）。

```tsx
export async function getInitialState() {
  // ...
  const userPermissions = await fetchUserPermissions();
  return {
    // ....
    userPermissions,
  };
}
```

## 2. 定义页面权限标记

需要将每个需要权限控制的页面和操作按钮提前定义好权限标记（某个 key），后续中使用用户拥有的权限和这个权限标记对比当前用户是否拥有权限。

这里直接写成一份常量维护，方便在不同组件中使用和维护。

```typescript
const PERMISSIONS = {
  PAGEA: {
    READ: "PAGEA_READ", // 页面访问权限标记
    ADD: "PAGEA_ADD" // 页面中操作按钮权限标记
    // ... 其他页面中操作按钮
  }
  // ... 其他页面权限标记
}
```

## 3. 路由和菜单的权限控制

将之前定义好的权限标记路由和对应的页面上。这里使用里自定义的字段 `meta` 中的 `permission`，后续可以直接从当前路由信息中取出这个页面的权限。

```typescript filename="routes.ts" {7,15}
export const routes = [
  {
    path: '/pageA',
    component: 'PageA',
    access: 'canAccessRoute', // 会调用 src/access.ts 中返回的 canAccessRoute 进行鉴权
    meta: {
      permission: PERMISSIONS.PAGEA.READ, // 页面访问权限标记
    },
  },
  {
    path: '/pageB',
    component: 'PageB',
    access: 'canAccessRoute', // 会调用 src/access.ts 中返回的 canAccessRoute 进行鉴权
    meta: {
      permission: PERMISSIONS.PAGEB.READ, // 页面访问权限标记
    },
  },
];
```

编写判断用户是否拥有页面权限判断逻辑，其逻辑十分简单，即判断当前路由权限标记是否在用户拥有的权限中。

```typescript filename="access.ts" {4-11}
export default function access(initialState) {
  const { userPermissions } = initialState ?? {};

  function canAccessRoute(route: IRoute) {
    // 页面对应的权限标记，需要在当前用户拥有的权限中
    if (route.meta?.permission) {
      return userPermissions.includes(route.meta.permission);
    }
    // 无页面权限标记说明改页面不需要权限也能访问
    return true;
  }

  return {
    canAccessRoute,
  };
}
```

## 4. 按钮权限控制

按钮权限的控制也可以依照之前页面权限的设计，先定义好操作按钮权限标记，然后判断用户是否拥有该权限。

```typescript filename="access.ts" {4-7,11}
export default function access(initialState) {
  // ...

  function canAccessAction(permissions: string[]) {
    // 按钮对应的权限标记，需要在当前用户拥有的权限中
    return permissions.every((permission) => userPermissions.includes(permission));
  }

  return {
    // ...
    canAccessAction
  };
}
```

为操作按钮添加权限控制时，可以使用封装的通用的权限组件，将该组件包裹操作按钮，只需要传入是否拥有权限即可。

**注意**：除了前端对操作按钮添加权限控制，一般后端也需要在对应接口中根据用户的角色和权限进行校验，返回允许或拒绝的结果。

```tsx filename="PageA.tsx" {5,7}
function PageA() {
  const { canAccessAction } = useAccess();

  return (
   <Access accessible={canAccessAction([PERMISSIONS.PAGEA.ADD])}>
      <Button>新增</Button>
    </Access>
  )
}
```

封装一个通用的权限组件在 `React` 中是十分简单的，例如 [Umi 中的 Access 组件](https://github.com/umijs/umi/blob/3f6e6c5b8d04158eefd482afeccfdbe3e0575011/packages/plugins/src/access.ts#L75)

```tsx
export interface AccessProps {
  accessible: boolean;
  fallback?: React.ReactNode;
}
export const Access: React.FC<PropsWithChildren<AccessProps>> = (props) => {
  if (process.env.NODE_ENV === 'development' && typeof props.accessible !== 'boolean') {
    throw new Error('[access] the \`accessible\` property on <Access /> should be a boolean');
  }

  return <>{ props.accessible ? props.children : props.fallback }</>;
};
```

## 参考链接

- [Ant Design Pro - 权限管理](https://pro.ant.design/zh-CN/docs/authority-management)
- [UmiJs - 权限](https://umijs.org/docs/max/access)
