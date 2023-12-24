---
title: 使用 React 实现多行输入组件
description:
date: 2023-12-17
tag: Web, React
---

# 使用 React 实现多行输入组件

输入组件一般都是字符串类型，在一些场景中需要输入多行数据，比如网址、名称等，封装一个支持多行输入的组件可以方便快速创建数据，避免多次输入耗费时间。

本文介绍如何基于 `input` 输入框组件封装一个多行输入组件。

## 实现效果

![react-area-input](/images/minigame/react-area-input.png)

[在线演示](https://stackblitz.com/edit/react-vw178j?)

## 实现方式

### 1. 引入相关依赖

这里基于 [Ant Design 输入框 Input](https://ant.design/components/input-cn)封装，也可以使用原生的 `textarea` 或者其他组件库

`AreaInput.tsx`

```tsx filename="AreaInput.tsx"
import { Input } from 'antd';
import { TextAreaProps } from 'antd/es/input';
import React from 'react';
import { useEffect, useState } from 'react';

const { TextArea } = Input;
```

### 2. 定义组件接收的 props

组件继承 [Ant Design 输入框 Input](https://ant.design/components/input-cn) 的所有 `props`，并且将 `value` 的类型改为字符串数组（ `string[]`）。

这里使用 `TypeScript` 的 [Omit](https://www.typescriptlang.org/docs/handbook/utility-types.html#omittype-keys) 实现覆盖原有的 `value` 类型

`AreaInput.tsx`

```tsx filename="AreaInput.tsx"
type AreaInputProps = Omit<TextAreaProps, 'value' | 'onChange'> & {
  value?: string[];
  onChange?: (val: string[]) => void;
};

/**
 * 支持多行输入文本框，value 为 string[]
 * 根据换行符 \n 切割文本
 * @param props
 * @returns
 */
const AreaInput: React.FC<AreaInputProps> = (props) => {
  const { value, onChange, ...rest } = props;
};

export default AreaInput;
```

### 3. 实现组件逻辑

实现组件实际逻辑部分很简单，只需要组件内使用一个额外状态维护实际输入的文本，并对其根据换行符 `\n` 加工后通过 `onChange` 提交给父组件更新 `value`即可以实现。

`AreaInput.tsx`

```tsx filename="AreaInput.tsx"
const AreaInput: React.FC<AreaInputProps> = (props) => {
  const [text, setText] = useState<string>();

  useEffect(() => {
    if (Array.isArray(value)) {
      setText(value.join('\n'));
    }
  }, [value]);

  const handleChange: React.ChangeEventHandler<HTMLTextAreaElement> = (e) => {
    const string = e.target.value;
    setText(string);
    const array = string.split('\n');
    onChange?.([...array]);
  };

  return (
    <TextArea
      autoSize={{ minRows: 3, maxRows: 6 }}
      placeholder="请输入，按回车键换行"
      value={text}
      onChange={handleChange}
      {...rest}
    />
  );
};
```

### 4. 使用方式

使用方式就和使用一个 `Input` 组件一样，可以通过 `value` 和 `onChange` 来实现受控模式获取到输入值（一个字符串数组）

```tsx filename="App.tsx"
import AreaInput from './components/AreaInput';

const App: React.FC = () => {
  const [value, setValue] = useState<string[]>(['google.com', 'facebook.com']);

  return (
      <AreaInput value={value} onChange={setValue} />
  );
};
```

## 参考链接

- [在线演示](https://codesandbox.io/p/sandbox/croninput-antd-pxr8p5)
- [输入框 Input - Ant Design](https://ant.design/components/input-cn)
