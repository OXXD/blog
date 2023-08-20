---
title: 如何 Mock 接口请求
description: 
date: 2023-07-21
tag: Tooling
---

# 如何 Mock 接口请求

前后端分离的项目，一般都是前后端单独开发页面和接口，等到开发完成后再一起联调。如果后端接口还未完成或者遇到后端服务不可用、网络不可用等场景时，那么前端开发页面时就需要手动 `Mock` 数据或者临时修改代码绕过接口调用才能继续开发。

本文介绍如何 `Mock` 接口请求，使得前端在无法调用后端服务的场景时也能简易方便快速的 `Mock` 接口调用和接口数据进行开发，并且无侵入修改代码。也可以使用在 `Mock` 特定接口数据排查问题的场景。

## Mock Service Worker

这里将使用 [Mock Service Worker](https://mswjs.io/) 这个方案，即 [msw](https://www.npmjs.com/package/msw)。其借助于 [Service Worker](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API) 能够拦截请求的能力，只需要简单的写 `Mock` 接口地址和数据匹配即可 `Mock` 对应接口。

其他类似的方案也有，比如使用 [JSON Server](https://github.com/typicode/json-server) 快速创建后端服务，但是缺点是需要修改接口地址。`Umi` 中的 [Mock](https://umijs.org/docs/guides/mock) 是使用效果类似的方案，不过实现方式是基于 `webpack-dev-middleware` 的中间件拦截请求实现，类似这个实现的也有不少。其他更多方案可[参考](https://mswjs.io/docs/comparison).

接下以 [Next.js](https://github.com/vercel/next.js/tree/canary/examples/with-msw) 中的示例来介绍如何使用 `msw`。

### 安装

```bash
npm install msw --save-dev
```

### 编写 Mock

```bash
mkdir src/mocks
touch src/mocks/handlers.ts
```

`handler.ts` 中引入 `rest`，因为我们需要 `Mock` 的 `HTTP` 请求，也支持 `Mock Graphql` 请求。

编写 `Mock` 的方式非常简单，只需要指定对应的请求方法和接口地址，然后返回 `JSON` 数据，其中可以使用任意代码逻辑（包括不同的状态吗，不同的参数返回不同的数据等），语法类似  `express` 的请求方法。

```typescript
import { rest } from 'msw'
import { Book, Review } from './types'

export const handlers = [
  rest.get('https://my.backend/book', (_req, res, ctx) => {
    return res(
      ctx.json<Book>({
        title: 'Lord of the Rings',
        imageUrl: '/book-cover.jpg',
        description:
          'The Lord of the Rings is an epic high-fantasy novel written by English author and scholar J. R. R. Tolkien.',
      })
    )
  }),
  rest.get('/reviews', (_req, res, ctx) => {
    return res(
      ctx.json<Review[]>([
        {
          id: '60333292-7ca1-4361-bf38-b6b43b90cb16',
          author: 'John Maverick',
          text: 'Lord of The Rings, is with no absolute hesitation, my most favored and adored book by‑far. The trilogy is wonderful‑ and I really consider this a legendary fantasy series. It will always keep you at the edge of your seat‑ and the characters you will grow and fall in love with!',
        },
      ])
    )
  }),
]
```

### 引入 Mock

这里引入 `Mock` 我们需要注册 `Service Worker` 才能生效，`msw` 已经替我们写好了 `Service Worker` 的拦截请求代码，只需要使用其提供的命令生成这份 `js` 文件到 `public` 文件夹即可

```bash
npx msw init <PUBLIC_DIR> --save
```

然后在 `browser.ts` 文件夹中创建浏览器需要初始化的方法，提供给后续判断是否需要开启 `Mock` 时使用。

```bash
touch src/mocks/browser.ts
```

```typescript
// src/mocks/browser.js
import { setupWorker } from 'msw'
import { handlers } from './handlers'

// This configures a Service Worker with the given request handlers.
export const worker = setupWorker(...handlers)
```

### 开启 Mock

这里我们在 `.env.development` 中加一个环境变量来控制是否开启 `Mock`。注意有的框架需要特定的环境变量名前缀才会在浏览器端中生效，比如 `NEXT_PUBLIC_` 和 `VITE_`

```env
NEXT_PUBLIC_API_MOCKING=enabled
```

```typescript
if (process.env.NEXT_PUBLIC_API_MOCKING === 'enabled') {
  const { worker } = require('./mocks/browser')
  worker.start()
}
```

观察浏览器中如果出现 `[MSW] Mocking enabled` 即代表开启 `Mock` 成功，后续对应的接口请求都会被 `Mock`，可以愉快的开发了，在没有后端或者无网络或者需要排查特定接口数据的场景都非常好用。

![msw](/images/minigame/msw.png)
![msw-request](/images/minigame/msw-request.png)
![msw-request-response](/images/minigame/msw-request-response.png)

### Vite 项目注意事项

`Vite` 项目中使用 `ESM`, `import` 和 `import.meta.env` 来判断环境变量，没有使用 `process.env` 和 `require` 可以考虑使用以下方式动态引入和判断是否开启 `Mock`。

```typescript
async function setup() {
  if (import.meta.env.VITE_API_MOCKING === "enabled") {
    const { worker } = await import("./mocks/browser");
    return worker.start()
  }
}

setup().then(() => {
  ReactDOM.createRoot(document.getElementById("root") as HTMLElement).render(
    <App />
  );
});
```

## 参考链接

- [Mock Service Worker](https://mswjs.io/)
- [Next.js Mock Service Worker Example](https://github.com/vercel/next.js/tree/canary/examples/with-msw)
