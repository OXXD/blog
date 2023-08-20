---
title: 如何解决 Github 3月24日 更新 RSA SSH host key 之后无法克隆项目
description: 
date: 2023-03-26
tag: 
---

# 如何解决 Github 3月24日 更新 RSA SSH host key 之后无法克隆项目

最近在使用 `SSH` 克隆 `Github` 项目时，发现无法克隆并且会报 `RSA key` 相关的错误。

起初以为是本机的密钥遭到泄漏或者破坏，翻了 [Github Blog](https://github.blog/2023-03-23-we-updated-our-rsa-ssh-host-key/)  之后发现是 `Github` 替换了他们的 `RSA SSH host key` 导致的，只需要本机更新下 `~/.ssh/known_hosts` 中的信息即可。

本文记录了问题的原因和解决方法。

## 问题描述

通过 `SSH` 克隆 Github 项目时 会报以下错误 `WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED! `

```bash
gcl git@github.com:reactjs/react.dev.git
```

```bash
Cloning into 'react.dev'...
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the RSA key sent by the remote host is
SHA256:uNiVztksCsDhcc0u9e8BujQXVUpKZIDTMczCvj3tD2s.
Please contact your system administrator.
Add correct host key in /Users/oxxd/.ssh/known_hosts to get rid of this message.
Offending RSA key in /Users/oxxd/.ssh/known_hosts:4
Host key for github.com has changed and you have requested strict checking.
Host key verification failed.
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```

## 问题原因

[Github Blog - We updated our RSA SSH host key](https://github.blog/2023-03-23-we-updated-our-rsa-ssh-host-key/) 在 2023年3月24日 发布的文章中提到，他们发现 `GitHub.com` 的 `RSA SSH private key` 被提交到了一个公开的仓库，他们很快的做出反应保证用户数据安全，替换了新的密钥。

受影响的通过 `SSH` 与 `Github` 交互的用户需要替换 `~/.ssh/known_hosts` 文件中的旧信息。

## 解决方法

1. 首先先移除 `~/.ssh/known_hosts` 文件中的旧信息。可以执行以下命令，也可以手动替换

```bash
ssh-keygen -R github.com
```

2. 手动替换 `~/.ssh/known_hosts` 文件中的信息为新的。

```bash
github.com ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCj7ndNxQowgcQnjshcLrqPEiiphnt+VTTvDP6mHBL9j1aNUkY4Ue1gvwnGLVlOhGeYrnZaMgRK6+PKCUXaDbC7qtbW8gIkhL7aGCsOr/C56SJMy/BCZfxd1nWzAOxSDPgVsmerOBYfNqltV9/hWCqBywINIR+5dIg6JTJ72pcEpEjcYgXkE2YEFXV1JHnsKgbLWNlhScqb2UmyRkQyytRLtL+38TGxkxCflmO+5Z8CSSNY7GidjMIZ7Q4zMjA2n1nGrlTDkzwDCsw+wqFPGQA179cnfGWOWRVruj16z6XyvxvjJwbz0wQZ75XK5tKSb7FNyeIEs4TT4jk+S4dhPeAUC5y+bDYirYgM4GC7uEnztnZyaVWQ7B381AK4Qdrwt51ZqExKbQpTUNn+EjqoTwvqNj4kqx5QUCI0ThS/YkOxJCXmPUWZbhjpCg56i+2aB6CmK2JGhn57K5mj0MNdBXA4/WnwH6XoPWJzK5Nyu2zB3nAZp+S5hpQs+p1vN1/wsjk=
```

3. 或者通过以下命令替换

```bash
ssh-keygen -R github.com
curl -L https://api.github.com/meta | jq -r '.ssh_keys | .[]' | sed -e 's/^/github.com /' >> ~/.ssh/known_hosts
```

4. 接下来即可重新正常的克隆 `Github` 项目。

## 参考链接

- [Github Blog - We updated our RSA SSH host key](https://github.blog/2023-03-23-we-updated-our-rsa-ssh-host-key/)
