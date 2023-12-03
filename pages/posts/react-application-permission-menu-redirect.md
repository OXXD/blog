---
title: 如何实现权限控制时一级菜单跳转有权限的任意二级菜单
description:
date: 2023-12-03
tag: Web, React
---

# 如何实现权限控制时一级菜单跳转有权限的任意二级菜单

后台系统中使用模块较多时，一般都会通过一级菜单二级菜单来组织模块，并且使用面包屑导航。一般一级菜单通常都是作为模块导航和入口，跳转到第一个二级菜单。
如果按照[上篇文章](/posts/react-application-permission)实现了权限控制之后，就需要根据当前用户拥有的权限判断一级菜单跳转到那个二级菜单下。

本文介绍如何实现权限控制时一级菜单跳转有权限的任意二级菜单。

![react-application-permission-menu-redirect](/images/minigame/react-application-permission-menu-redirect.png)

## 实现方式

### 1. 当前路由配置

```typescript filename="routes.ts"
export const routes = [
  {
    path: '/list',
    icon: 'smile',
    routes: [
      {
        path: '/list/table-list',
        component: './TableList',
        access: 'canAccessRoute',
        meta: {
          permission: PERMISSIONS.LIST.TABLE_LIST.READ,
        },
      },
      {
        path: '/list/basic-list',
        component: './BasicList',
        access: 'canAccessRoute',
        meta: {
          permission: PERMISSIONS.LIST.BASIC_LIST.READ,
        },
      },
    ]
  },
];
```

### 2. 修改后的路由配置

首先先给一级路由加上权限控制，并且记录下该一级菜单拥有的所有二级菜单权限标记，后面可以通过路由信息中获取到进行匹配。

```typescript filename="routes.ts" {4-11}
export const routes = [
  {
    path: '/list',
    icon: 'smile',
    access: 'canAccessRoute', // 加上权限控制一级菜单
    meta: {
      permissions: [ // 加上该一级菜单下的二级菜单权限标记
        PERMISSIONS.LIST.TABLE_LIST.READ, 
        PERMISSIONS.LIST.BASIC_LIST.READ
      ],
    },
    routes: [
     // ...
    ]
  },
];
```

在权限判断逻辑中加上一级菜单判断有任意子路由即拥有菜单权限

```typescript filename="access.ts" {5-10}
export default function access(initialState) {
  const { userPermissions } = initialState ?? {};

  function canAccessRoute(route: IRoute) {
    // 一级菜单判断有任意子路由即拥有菜单权限
    if (route.meta?.permissions) {
      return route.meta?.permissions.some((permission: string) =>
        menu_permissions.includes(permission),
      );
    }
  
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

### 3. 实现一级菜单跳转有权限的任意二级菜单组件

这里使用 [UmiJs - 路由 wrappers](https://umijs.org/docs/guides/routes#wrappers) 配置路由组件的包装组件，可以直接实现路由跳转的功能。
其他框架也可以直接实现一个类似的 `HOC` 包裹后的页面也可以实现一样的功能。

```tsx filename="wrapper.tsx"
type RedirectRouteType = {
  permission: string;
  path: string;
};

const redirectRoutesMap: Record<string, RedirectRouteType[]> = {
  '/list': [
    {
      permission: PERMISSIONS.LIST.TABLE_LIST.READ,
      path: '/list/table-list',
    },
    {
      permission: PERMISSIONS.LIST.BASIC_LIST.READ,
      path: '/list/basic-list',
    },
  ],
  '/module': [
    // ...
  ]
};

/**
 * 跳转到拥有权限的第一个子路由
 */
export default () => {
  const { initialState } = useModel('@@initialState');
  const { userPermissions } = initialState || {};

  const { route } = useRouteData(); // 获取出路由信息
  const redirectRoutes = redirectRoutesMap[route.path as string]; // 使用路由地址匹配出是哪个一级菜单

  // 找到当前一级菜单下有权限的第一个二级菜单地址
  const redirectTo = useMemo(() => {
    const matched = redirectRoutes.find((item) =>
      userPermissions.some((permission) => item.permission === permission),
    );
    const redirectPath = matched?.path || '/';
    return redirectPath;
  }, [redirectRoutes, userPermissions]);
  
  // 使用 Navigate 跳转到匹配到的那个二级菜单地址 
  return <Navigate to={redirectTo} />;
};
```

### 其他方案

以上实现的是跳转点击是的路由判断后进行跳转。这里也提供另一个实现的方案，可供参考。可以借助[动态路由相关的 API](https://umijs.org/docs/api/runtime-config#patchclientroutes-routes-)，当获取用户权限之后，根据获取的用户权限生成用户拥有的所有路由，并且将这些动态路由加入路由配置中，这样用户就不拥有不存在权限路由，也可以实现该功能。

## 参考链接

- [如何实现前端页面权限和按钮权限](/posts/react-application-permission)
- [UmiJs - 路由 wrappers](https://umijs.org/docs/guides/routes#wrappers)
- [Ant Design Pro - 权限管理](https://pro.ant.design/zh-CN/docs/authority-management)
- [UmiJs - 权限](https://umijs.org/docs/max/access)
