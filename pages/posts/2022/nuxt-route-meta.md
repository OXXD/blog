---
title: 如何在 Nuxt 中获取 Vue Router 的 meta
description: 
date: 2022-11-06
tag: Vue, Nuxt
---

# 如何在 Nuxt 中获取 Vue Router 的 meta

最近在一个 `Nuxt` 项目中遇到需要给路由添加自定义 `meta` 用来判断菜单路由匹配，在非 `Nuxt` 项目中我们可以直接在 `Vue Router` 的配置文件里定义就行。但是我们都知道 `Nuxt` 是基于 `pages` 目录下自动生成页面路由的，并不能自定义 `Vue Router` 的配置文件。下面给出几个解决该问题的方案。

## 解决方案

- 官方方案：给每个页面添加 `meta` 属性，后期获取在 `middleware` 中获取，参考[官方示例](https://github.com/nuxt/nuxt.js/tree/2.x/examples/routes-meta)
  - 优点：官方方案，简单实现，不需要额外引入第三方库
  - 缺点：获取只能在 `middleware` 中获取并通过 `Vuex Store` 中转一层做处理，不能方便的在每个组件中的 `this.$route.meta` 直接获取
  - 缺点：每个页面都需要添加 `meta` 属性
- 使用 [router-extras-module](https://github.com/nuxt-community/router-extras-module) 可以给每个页面配置全面的任意路由信息
  - 优点：功能强大全面
  - 缺点：引入了额外的第三方库，并且配置方式过重，增加了学习成本
- 使用第三方库 [nuxt-route-meta](https://www.npmjs.com/package/nuxt-route-meta)，使用方式也是给每个页面添加 `meta` 属性，后期获取可以在任意组件中获取得到
  - 优点：任意组件中实例都能获取得到，只需要通过 `this.$route.meta`
  - 缺点：实际上是通过 `Nuxt` 中 `extendRoute` 属性在每次编译的时候加上了 `meta` 信息，所以修改了之后需要每次编译之后才能生效
  - 缺点：也和官方方案一样，每个页面都需要添加 `meta` 属性
  - 缺点：引入了额外的第三方库

综合考虑之后，决定采用官方方案实现。

## 实现方案

在需要的相关页面文件中添加自定义的 `meta` 信息

`page/index.vue`

```vue
export default {
  meta: {
    activeMenu: "/ad",
  },
}
```

自定义一个 `middleware` 将获取到的 `meta` 信息存在 `Vuex Store` 中方便后期获取，并且修改 `Nuxt` 配置的 `router.middleware` 字段使该自定义 `middleware` 生效

`src/middleware/meta.js`

```javascript
export default ({ route, store }) => {
  store.commit("nav/setActiveMenu", route.meta[0]?.activeMenu || "");
};
```

`nuxt.config.js`

```javascript
{
  router: {
    middleware: ['meta']
  },
}
```

`src/store/nav.js`

```javascript
export const state = {
  activeMenu: "",
};

export const mutations = {
  setActiveMenu(state, activeMenu) {
    state.activeMenu = activeMenu;
  },
};
```

后期需要使用到该 `meta` 信息，就可以从 `Vuex Store` 中取出

```vue
export default {
  activeMenu: {
    activeMenu() {
      return this.$store.state.nav.activeMenu
    }
  }
}
```

## 参考链接

- [Routes meta with Nuxt](https://github.com/nuxt/nuxt.js/tree/2.x/examples/routes-meta)
- [router-extras-module](https://github.com/nuxt-community/router-extras-module)
- [nuxt-route-meta](https://www.npmjs.com/package/nuxt-route-meta)
- [How to Access Nuxt.js Page Data in Route Meta Fields](https://sebastianlandwehr.com/blog/how-to-access-nuxt-js-page-data-in-route-meta-fields/)