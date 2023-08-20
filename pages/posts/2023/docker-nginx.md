---
title: 使用 Docker 部署 Nginx 时如何设置环境变量
description: 
date: 2023-06-30
tag: Docker
---

# 使用 Docker 部署 Nginx 时如何设置环境变量

当我们使用 `Docker` 部署 `Nginx` 时，可能会遇到需要根据不同环境，配置不同端口或者反向代理地址的场景。那么 `Docker` 的环境变量是否能够传递给 `Nginx` 配置呢？实际是可以的，官方提供的 `nginx` 镜像就支持这一操作。

本文介绍如何将 `Docker` 环境变量透穿给 `Nginx` 配置。

### 原 `Dockerfile` 配置

以下是一个前端单页应用的 `Dockerfile`，基本流程是打包前端项目，复制 `nginx.conf` 配置，将产物放在 `nginx` 的网站目录，启动 `nginx` 服务。

```dockerfile
FROM node:16-alpine as builder

WORKDIR /usr/src/app/
USER root
COPY package-lock.json ./
COPY package.json ./
RUN npm ci

COPY ./ ./

RUN npm run build

FROM nginx

WORKDIR /usr/share/nginx/html/

COPY ./docker/nginx.conf /etc/nginx/conf.d/default.conf
COPY --from=builder /usr/src/app/dist  /usr/share/nginx/html/

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

### 原 `nginx.conf` 配置

```nginx
server {
    listen 80;
    # gzip config
    gzip on;
    gzip_min_length 1k;
    gzip_comp_level 9;
    gzip_types text/plain text/css text/javascript application/json application/javascript application/x-javascript application/xml;
    gzip_vary on;
    gzip_disable "MSIE [1-6]\.";

    root /usr/share/nginx/html;
    include /etc/nginx/mime.types;

    location / {
        try_files $uri $uri/ /index.html;
    }
    
    # 代理后端接口
    location /api {
        proxy_pass http://api.someserver.com;
        proxy_set_header   X-Forwarded-Proto $scheme;
        proxy_set_header   X-Real-IP         $remote_addr;
    }
}

```

`nginx` 镜像中自 `1.19` 开始已支持环境变量。只需要将 `Dockerfile` 中的 `Nginx` 配置地址放在 `/etc/nginx/templates/*.template` 中，并且 `nginx.conf` 的配置中使用 `$ENV_NAME` 指定对应的环境变量即可。

![docker-nginx](/images/minigame/docker-nginx.png)

### 修改 `Dockerfile`

```diff
- COPY ./docker/nginx.conf /etc/nginx/conf.d/default.conf
+ COPY ./docker/nginx.conf /etc/nginx/templates/default.conf.template
```

### 修改 `nginx.conf`

```diff
- proxy_pass http://api.someserver.com;
+ proxy_pass $API_URL;
```

### 打包镜像

```bash
 docker build -t some-nginx .
```

### 启动容器，传入环境变量

```bash
docker run -d -p 80:80 -e API_URL=http://api.someserver.com some-nginx
```

## 参考链接

- [Docker Nginx image](https://hub.docker.com/_/nginx)
- [Stack Overflow](https://stackoverflow.com/questions/72748706/how-to-pass-environment-variable-to-nginx-conf-file-in-docker)