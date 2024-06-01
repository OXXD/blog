---
title: 浏览器中如何获取用户网络状态
description: 
date: 2024-06-02
tag: Web
---

# 浏览器中如何获取用户网络状态

网页开发中存在需要获取用户是否在线的场景及用户网络状态，浏览器提供了 `navigator.onLine` 和 `navigator.connection` 可以实现这一需求。

## 获取在线状态

```javascript
if (navigator.onLine) {
  console.log("online");
} else {
  console.log("offline");
}
```

## 监听网络状态变更事件

```javascript
window.addEventListener("offline", (e) => {
  console.log("offline");
});

window.addEventListener("online", (e) => {
  console.log("online");
});
```

## 通过 NetworkInformation API 获取更多网络相关信息

[NetworkInformation API](https://developer.mozilla.org/en-US/docs/Web/API/NetworkInformation) 中提供了更多网络状态相关的信息，如最大下行速度、网络连接类型等。

```typescript
function getConnection() {
  const nav = navigator as any;
  if (!isObject(nav)) return null;
  return nav.connection || nav.mozConnection || nav.webkitConnection;
}

function getConnectionProperty(): NetworkState {
  const c = getConnection();
  if (!c) return {};
  return {
    rtt: c.rtt,
    type: c.type,
    saveData: c.saveData,
    downlink: c.downlink,
    downlinkMax: c.downlinkMax,
    effectiveType: c.effectiveType,
  };
}
```

| 属性          | 描述                                   | 类型                                                                                           |
| ------------- | -------------------------------------- | ---------------------------------------------------------------------------------------------- |
| rtt           | 当前连接下评估的往返时延               | `number`                                                                                       |
| type          | 设备使用与所述网络进行通信的连接的类型 | `bluetooth` \| `cellular` \| `ethernet` \| `none` \| `wifi` \| `wimax` \| `other` \| `unknown` |
| downlink      | 有效带宽估算（单位：兆比特/秒）        | `number`                                                                                       |
| downlinkMax   | 最大下行速度（单位：兆比特/秒）        | `number`                                                                                       |
| saveData      | 用户代理是否设置了减少数据使用的选项   | `boolean`                                                                                      |
| effectiveType | 网络连接的类型                         | `slow-2g` \| `2g` \| `3g` \| `4g`                                                              |

更多信息参考：[MDN NetworkInformation](https://developer.mozilla.org/en-US/docs/Web/API/NetworkInformation)

## 封装成一个 useNetwork 自定义 Hook

以下代码是 `ahooks` 中的 `useNetwork` 自定义 Hook 实现方式，其核心原理是通过以上的 `navigator.onLine` 和 `navigator.connection` 中提供的 `API` 进行分装的。

其他的自定义 `Hooks` 也有类似实现的封装。

```typescript
import { useEffect, useState } from 'react';
import { isObject } from '../utils';

export interface NetworkState {
  since?: Date;
  online?: boolean;
  rtt?: number;
  type?: string;
  downlink?: number;
  saveData?: boolean;
  downlinkMax?: number;
  effectiveType?: string;
}

enum NetworkEventType {
  ONLINE = 'online',
  OFFLINE = 'offline',
  CHANGE = 'change',
}

function getConnection() {
  const nav = navigator as any;
  if (!isObject(nav)) return null;
  return nav.connection || nav.mozConnection || nav.webkitConnection;
}

function getConnectionProperty(): NetworkState {
  const c = getConnection();
  if (!c) return {};
  return {
    rtt: c.rtt,
    type: c.type,
    saveData: c.saveData,
    downlink: c.downlink,
    downlinkMax: c.downlinkMax,
    effectiveType: c.effectiveType,
  };
}

function useNetwork(): NetworkState {
  const [state, setState] = useState(() => {
    return {
      since: undefined,
      online: navigator?.onLine,
      ...getConnectionProperty(),
    };
  });

  useEffect(() => {
    const onOnline = () => {
      setState((prevState) => ({
        ...prevState,
        online: true,
        since: new Date(),
      }));
    };

    const onOffline = () => {
      setState((prevState) => ({
        ...prevState,
        online: false,
        since: new Date(),
      }));
    };

    const onConnectionChange = () => {
      setState((prevState) => ({
        ...prevState,
        ...getConnectionProperty(),
      }));
    };

    window.addEventListener(NetworkEventType.ONLINE, onOnline);
    window.addEventListener(NetworkEventType.OFFLINE, onOffline);

    const connection = getConnection();
    connection?.addEventListener(NetworkEventType.CHANGE, onConnectionChange);

    return () => {
      window.removeEventListener(NetworkEventType.ONLINE, onOnline);
      window.removeEventListener(NetworkEventType.OFFLINE, onOffline);
      connection?.removeEventListener(NetworkEventType.CHANGE, onConnectionChange);
    };
  }, []);

  return state;
}

export default useNetwork;
```

### useNetwork 自定义 Hook 使用方式

```tsx
import React from 'react';
import { useNetwork } from 'ahooks';

export default () => {
  const networkState = useNetwork();

  return (
    <div>
      <div>Network information: </div>
      <pre>{JSON.stringify(networkState, null, 2)}</pre>
    </div>
  );
};
```

## 参考链接

- [MDN Navigator: onLine property](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/onLine)
- [MDN NetworkInformation](https://developer.mozilla.org/en-US/docs/Web/API/NetworkInformation)
- [useNetwork - ahooks 3.0](https://ahooks.js.org/zh-CN/hooks/use-network/)
- [useNetwork - ahooks 3.0 源码](https://github.com/alibaba/hooks/blob/master/packages/hooks/src/useNetwork/index.ts)