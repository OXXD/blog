---
title: 如何调试/抓包 Node.js 项目网络请求
description: 
date: 2023-02-12
tag: Node.js
---

# 如何调试/抓包 Node.js 项目网络请求

当我们需要调试一个网站的网络请求时，可以使用非常方便的 `Chrome Devtools` 中的网络请求面板看到每一个网络请求。那么我们的后端项目（比如 `Node.js`）中的网络请求是否也能使用类似的工具查看和调试每个网络请求呢？

最初的思路是使用 [Charles](https://www.charlesproxy.com/)、[Fiddler](https://www.telerik.com/fiddler) 这一类的网络代理和调试工具，但是折腾一番之后发现 `Node.js` 项目的请求并不会出现在代理工具中。经过搜索后发现，`Node.js` 项目只需要设置两个网络代理相关的环境变量即可将网络请求转发到网络代理工具。

## 通过 dotenv 设置环境变量

`.env` 环境变量文件

```bash
http_proxy="http://localhost:8080" # 网络代理工具的地址和端口
https_proxy="http://localhost:8080"
```

## 也可以通过 cross-env 启动时注入环境变量

```json
{
    "scripts": {
        "dev": "cross-env http_proxy=http://127.0.0.1:8899 https_proxy=http://127.0.0.1:8899 NODE_ENV=development node server.js"
    }
}
```

## 效果

这里我们使用 [mitmproxy](https://mitmproxy.org/)，相比于其他网络代理调试工具其优势是开源免费垮平台，可以只给需要的项目设置代理端口。后续调试 `Node.js` 网络请求就可以有直观的可视化面板，可以看到详细请求信息，请求报文，响应数据等。也可以使用 [Charles](https://www.charlesproxy.com/)、[Fiddler](https://www.telerik.com/fiddler) 等其他的网络代理调试工具。

![mitmproxy](/images/minigame/mitmproxy.png)

## 参考链接

- [mitmproxy](https://mitmproxy.org/)
- [StackOverflow - how to monitor the network on node.js similar to chrome/firefox developer tools?](https://stackoverflow.com/questions/28873332/how-to-monitor-the-network-on-node-js-similar-to-chrome-firefox-developer-tools)
- [如何抓包/代理node服务的request](https://juejin.cn/post/7186824867437051961)