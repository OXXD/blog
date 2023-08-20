---
title: Web 开发本地环境如何配置 HTTPS
description: 
date: 2022-11-26
tag: Tooling
---

# Web 开发本地环境如何配置 HTTPS

Web 本地开发时我们都是使用 `http://localhost` 来访问本地服务方便调试，但有些场景需要在 `HTTPS` 下才能使用，比如 `Service Worker`、`Web Notifications API`, 虽然这些本地调试时 `HTTP` 也能正常调试，但也存在一些场景一定需要 `HTTPS`，比如 `HTTP` 请求 `HTTPS` 资源、使用了自定义域名等。本文给出本地开发时配置 `HTTPS` 的方案

## 方案一：使用内网穿透工具

比较方便省事的一个方案就是使用一些内网穿透工具，可以快速将本地的一些服务暴露到公网访问。比如 [localtunnel](https://github.com/localtunnel/localtunnel), [ngrok](https://www.npmjs.com/package/ngrok), [钉钉内网穿透工具](https://open.dingtalk.com/document/resourcedownload/http-intranet-penetration)。

```bash
npx localtunnel --port 8000

# 或者全局安装
npm install -g localtunnel
lt --port 8000
```

这些工具使用方便，也会提供临时域名方便访问，适合在自己没有服务器和域名的场景使用。不过由于是公共工具、公共服务器，一般都会有一些限制，比如访问速度和同事请求数量，会带来一些不便。

另一个类似的方案是如果自己有域名和服务器，可以考虑使用 `nginx` 做反向代理，这一方案有一些要求，有条件的朋友可以研究下。

## 方案二：本地生成证书

另一个方案比较适用的方案，就是自己本地生成证书，并且让浏览器或者系统信任本地证书，项目内进行简单的配置就可以实现访问 `https://localhost`. 生成证书工具可以借助 [mkcert](https://github.com/FiloSottile/mkcert) 或者 `openssl`

### Vite 项目

Vite 项目可以使用官方插件 [@vitejs/plugin-basic-ssl](https://github.com/vitejs/vite-plugin-basic-ssl), 已经帮你生成好了证书，直接引入该插件即可。

```javascript
// vite.config.js
import basicSsl from '@vitejs/plugin-basic-ssl'

export default {
  plugins: [
    basicSsl()
  ]
}
```

启动服务后访问 `https://localhost:5173` 即可。

### 自己生成证书和 Nodejs 项目实例

如果不想使用第三方插件生成好的证书，可以自己生成证书。这里使用 [mkcert](https://github.com/FiloSottile/mkcert)，略过安装方式（不同系统安装方式不同）。

```bash
# 首次使用
mkcert -install

# 为站点生成证书
mkcert localhost
```

证书生成成功后，在项目配置里配置好证书地址即可。以下是一个 `Nodejs` 项目实例代码，其他的项目也配置也基本类似，只需要配置好证书地址既可。

```javascript
const https = require('node:https');
const fs = require('node:fs');

const options = {
  key: fs.readFileSync('test/fixtures/keys/agent2-key.pem'),
  cert: fs.readFileSync('test/fixtures/keys/agent2-cert.pem')
};

https.createServer(options, (req, res) => {
  res.writeHead(200);
  res.end('hello world\n');
}).listen(8000);
```

启动服务好可以访问 `https://localhost:8000`

```bash
node server.js
```

### 小技巧

可以使用 [http-server](https://github.com/http-party/http-server) 在任意目录启动一个静态文件服务器，也支持自定义证书，启动命令也只需要一行命令。

```bash
# 可以全局安装
npm i -g http-server

# 启动服务
http-server

# 启动服务并使用自定义证书
http-server -c-1 --ssl --cert {ssl cert文件} --key {ssl key文件}
```

## 参考链接

- [How to use HTTPS for local development](https://web.dev/how-to-use-local-https/)
- [When to use HTTPS for local development](https://web.dev/when-to-use-local-https/)
- [Using HTTPS in Your Development Environment](https://auth0.com/blog/using-https-in-your-development-environment/)
- [@vitejs/plugin-basic-ssl](https://github.com/vitejs/vite-plugin-basic-ssl)
- [Vite 文档](https://vitejs.dev/config/server-options.html#server-https)
- [Nodejs 文档](https://nodejs.org/dist/latest-v18.x/docs/api/https.html#httpscreateserveroptions-requestlistener)