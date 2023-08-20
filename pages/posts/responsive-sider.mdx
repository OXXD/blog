---
title: 制作一个兼容移动端的响应式侧边栏
description: 
date: 2023-06-16
tag: React
---

# 制作一个兼容移动端的响应式侧边栏

侧边栏是网站中的重要导航组件，尤其在后台系统中，提供了方便的菜单导航。一般为 PC 端实际的侧边栏都是占据侧边一定宽度，当切换到移动端显示时如果未做对应调整，反而会占据小屏幕下的主要空间，影响主要内容的展示。

本文介绍通过 `Media Query` 和 `Drawer` 组件配合，实现一个兼容移动端的响应式侧边栏。使用的组件为 [Ant Design](https://ant.design/components/layout-cn)，交互效果来自 [Google AdSense](https://adsense.google.com/) 后台

## 效果

![responsive-sider-adsense](/images/minigame/responsive-sider-adsense.gif)

![responsive-sider](/images/minigame/responsive-sider.gif)

## 实现

实现原理其实也很简单，只需要通过 `Media Query` 监听视窗改变判断是否移动端，如果是移动端那么就切换为全屏 `Drawer` 组件。这里以 [Ant Design 自定义触发器 demo](https://ant.design/components/layout-cn#components-layout-demo-custom-trigger) 为例，实现完成的代码可以见 [CodeSandbox](https://codesandbox.io/s/zi-ding-yi-chu-fa-qi-antd-5-6-1-forked-qnysj6?file=/demo.tsx&resolutionWidth=320&resolutionHeight=675)

### 1. 原始的代码如下：

```tsx
import React, { useState } from 'react';
import {
  MenuFoldOutlined,
  MenuUnfoldOutlined,
  UploadOutlined,
  UserOutlined,
  VideoCameraOutlined,
} from '@ant-design/icons';
import { Layout, Menu, Button, theme } from 'antd';

const { Header, Sider, Content } = Layout;

const App: React.FC = () => {
  const [collapsed, setCollapsed] = useState(false);
  const {
    token: { colorBgContainer },
  } = theme.useToken();

  return (
    <Layout>
      <Sider trigger={null} collapsible collapsed={collapsed}>
        <div className="demo-logo-vertical" />
        <Menu
          theme="dark"
          mode="inline"
          defaultSelectedKeys={['1']}
          items={[
            {
              key: '1',
              icon: <UserOutlined />,
              label: 'nav 1',
            },
            {
              key: '2',
              icon: <VideoCameraOutlined />,
              label: 'nav 2',
            },
            {
              key: '3',
              icon: <UploadOutlined />,
              label: 'nav 3',
            },
          ]}
        />
      </Sider>
      <Layout>
        <Header style={{ padding: 0, background: colorBgContainer }}>
          <Button
            type="text"
            icon={collapsed ? <MenuUnfoldOutlined /> : <MenuFoldOutlined />}
            onClick={() => setCollapsed(!collapsed)}
            style={{
              fontSize: '16px',
              width: 64,
              height: 64,
            }}
          />
        </Header>
        <Content
          style={{
            margin: '24px 16px',
            padding: 24,
            minHeight: 280,
            background: colorBgContainer,
          }}
        >
          Content
        </Content>
      </Layout>
    </Layout>
  );
};

export default App;
```

### 2. 引入 `use-media-antd-query` 来判断是否移动端

判断是否移动端的原理本质是通过 `window.matchMedia` 和 `Media Query` 实现的，这里也可以使用其他方式判断（比如其他 `hook` 库提供的 `useMediaQuery` 或者自行通过 `window.matchMedia` 实现）。

```tsx
import useAntdMediaQuery from "use-media-antd-query";

const colSize = useAntdMediaQuery();
const isMobile = colSize === "sm" || colSize === "xs";

const [collapsed, setCollapsed] = useState(isMobile);
```

### 3. 判断移动端时，使用全屏的 `Drawer` 组件包裹菜单

```tsx
// 菜单复用
const menus = (
  <Menu
    mode="inline"
    defaultSelectedKeys={["1"]}
    items={[
      {
        key: "1",
        icon: <UserOutlined />,
        label: "nav 1"
      },
      {
        key: "2",
        icon: <VideoCameraOutlined />,
        label: "nav 2"
      },
      {
        key: "3",
        icon: <UploadOutlined />,
        label: "nav 3"
      }
    ]}
  />
);

{isMobile ? (
  <Drawer
    placement="left"
    open={!collapsed}
    onClose={() => setCollapsed(true)}
    width="100vw" // 全屏
    bodyStyle={{ padding: 0 }}
    headerStyle={{ padding: "16px 2px" }}
  >
    <div className="demo-logo-vertical" />
    {menus}
  </Drawer>
) : (
  <Sider
    trigger={null}
    collapsible
    collapsed={collapsed}
    style={{ background: colorBgContainer }}
  >
    <div className="demo-logo-vertical" />
    {menus}
  </Sider>
)}
```

完整的 [CodeSandbox demo 源码](https://codesandbox.io/s/zi-ding-yi-chu-fa-qi-antd-5-6-1-forked-qnysj6?file=/demo.tsx&resolutionWidth=320&resolutionHeight=675)

## 参考链接

- [window.matchMedia 文档](https://developer.mozilla.org/en-US/docs/Web/API/Window/matchMedia)
- [Ant Design 自定义触发器 demo](https://ant.design/components/layout-cn#components-layout-demo-custom-trigger)
- [CodeSandbox demo 源码](https://codesandbox.io/s/zi-ding-yi-chu-fa-qi-antd-5-6-1-forked-qnysj6?file=/demo.tsx&resolutionWidth=320&resolutionHeight=675)
- [use-media-antd-query](https://github.com/chenshuai2144/useMediaQuery)
