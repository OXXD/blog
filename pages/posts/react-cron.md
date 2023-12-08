---
title: 使用 React 实现 cron 时间选择组件
description:
date: 2023-12-08
tag: Web, React
---

# 使用 React 实现 cron 时间选择组件

[cron](https://en.wikipedia.org/wiki/Cron) 是最常见的类 Unix 系统下的基于时间的任务管理系统，在实现定时任务场景中较常使用。

本文介绍如何实现一个前端时间选择组件，其中绑定的值使用 `cron`  的时间格式，方便传递给后端存储使用。

## 实现效果

![cron](/images/minigame/cron.png)

[在线演示](https://codesandbox.io/p/sandbox/croninput-antd-pxr8p5)

## 实现方式

### 先定义时间选项

```typescript
export enum TimeType {
  EVERY_DAY = "everyDay",
  EVERY_WEEK = "everyWeek",
  EVERY_MONTH = "everyMonth",
}

export const timeTypes = [
  { value: TimeType.EVERY_DAY, label: "每天" },
  { value: TimeType.EVERY_WEEK, label: "每周" },
  { value: TimeType.EVERY_MONTH, label: "每月" },
];

export const dayOfTheWeekOption = [
  { value: "1", label: "星期一" },
  { value: "2", label: "星期二" },
  { value: "3", label: "星期三" },
  { value: "4", label: "星期四" },
  { value: "5", label: "星期五" },
  { value: "6", label: "星期六" },
  { value: "7", label: "星期天" },
];

export const monthOption = [
  { value: "1", label: "一月" },
  { value: "2", label: "二月" },
  { value: "3", label: "三月" },
  { value: "4", label: "四月" },
  { value: "5", label: "五月" },
  { value: "6", label: "六月" },
  { value: "7", label: "七月" },
  { value: "8", label: "八月" },
  { value: "9", label: "九月" },
  { value: "10", label: "十月" },
  { value: "11", label: "十一月" },
  { value: "12", label: "十二月" },
];

//获取dayOfTheMonthOption的每月对象
function getDayOfTheMonthOption() {
  const days = [];
  for (let i = 1; i < 32; i += 1) {
    days.push({ value: i.toString(), label: i.toString().concat("号") });
  }
  return days;
}

export const dayOfTheMonthOption = getDayOfTheMonthOption();

export function parseCron(expression: string) {
  const cron = expression ? expression.split(" ") : [];
  const [minutes, hours, dayOfMonth, month, dayOfWeek] = cron;
  // "20 0 * * *" 每天
  // "26 1 * * 1" 每周
  // "26 0 1 * *" 每月
  const isEveryDay = dayOfMonth === "*" && month === "*" && dayOfWeek === "*";
  const isEveryWeek = dayOfMonth === "*" && month === "*" && dayOfWeek !== "*";
  const isEveryMonth = dayOfMonth !== "*" && month === "*" && dayOfWeek === "*";
  return {
    isEveryDay,
    isEveryWeek,
    isEveryMonth,
    cron,
    cronObjects: { minutes, hours, dayOfMonth, month, dayOfWeek },
  };
}
```

### 引入相关依赖

这里使用 `Ant Design` 组件库，使用 `dayjs` 处理时间

```tsx
import { Select, Space, TimePicker } from "antd";
import type { Dayjs } from "dayjs";
import dayjs from "dayjs";
import { useEffect, useState } from "react";
import {
  TimeType,
  dayOfTheMonthOption,
  dayOfTheWeekOption,
  timeTypes,
  parseCron,
} from "./utils";
```

### 实现组件布局

这里我们需要三种时间类型选择，分为每天、每周、每月，每个时间类型选择都需要选择时间，其中每周需要选择周几，每月需要选择几号。

使用下拉组件实现时间类型选择和周几、几号选择，使用时间选择器组件实现时间选择。

```tsx
const format = "HH:mm";
const defaultCron = "0 * * * *";
const space = " "; //空格

type CronInputProps = {
  value?: string;
  onChange?: (cron: string) => void;
};

const CronInput: React.FC<CronInputProps> = (props) => {
  const [timeType, setTimeType] = useState(TimeType.EVERY_DAY); // 类型
  const [selectedDay, setSelectedDay] = useState<string>(); // 日期（星期几或者几号）
  const [selectedTime, setSelectedTime] = useState<Dayjs | null>(null); // 时间
  const [expression, setExpression] = useState<string>(defaultCron);

  const RenderSelect = ({
    placeholder,
    data = [],
  }: {
    placeholder: string;
    data: { value: string; label: string }[];
  }) => {
    return (
      <Space>
        <Select
          placeholder={placeholder}
          onChange={handleChangeSelectedDay}
          value={selectedDay}
          options={data}
        ></Select>
        <TimePicker
          value={selectedTime}
          format={format}
          placeholder="请选择时间"
          onChange={handleChangeTime}
        />
      </Space>
    );
  };

  return (
    <Space>
      <Select
        placeholder="请选择类型"
        onChange={handleChangeTimeType}
        value={timeType}
        options={timeTypes}
      ></Select>
      {timeType === TimeType.EVERY_DAY && (
        <TimePicker
          value={selectedTime}
          format={format}
          placeholder="请选择时间"
          onChange={handleChangeTime}
        />
      )}
      {timeType === TimeType.EVERY_WEEK && (
        <RenderSelect data={dayOfTheWeekOption} placeholder="请选择星期" />
      )}
      {timeType === TimeType.EVERY_MONTH && (
        <RenderSelect data={dayOfTheMonthOption} placeholder="请选择日期" />
      )}
    </Space>
  );
};
```

### 加上事件处理选择时间后的逻辑

时间类型改变，重置周几和日期选择、重置时间选择

周几和日期选择，时间选择事件中，将选中的数据处理转换成 `cron` 格式中的位数

```tsx
  // 类型选择
  const handleChangeTimeType = (val: TimeType) => {
    setTimeType(val);
    setSelectedTime(null);
    setSelectedDay(undefined);
    handleChange(defaultCron);
  };

  // 日期选择
  const handleChangeSelectedDay = (day: string) => {
    setSelectedDay(day);
    const currentCron = expression ? expression.split(" ") : [];
    const [minutes, hours, dayOfMonth, month, dayOfWeek] = currentCron;
    let result = "";
    if (timeType === TimeType.EVERY_WEEK) {
      result = minutes
        .concat(space)
        .concat(hours)
        .concat(space)
        .concat(dayOfMonth)
        .concat(space)
        .concat(month)
        .concat(space)
        .concat(day);
    }
    if (timeType === TimeType.EVERY_MONTH) {
      result = minutes
        .concat(space)
        .concat(hours)
        .concat(space)
        .concat(day.length ? day : "*")
        .concat(space)
        .concat(month)
        .concat(space)
        .concat(dayOfWeek);
    }
    if (result) {
      handleChange(result);
    }
  };

  //时间选择
  const handleChangeTime = (time: Dayjs | null) => {
    setSelectedTime(time);
    if (!time) return;
    const currentCron = expression ? expression.split(" ") : [];
    const [, , dayOfMonth, month, dayOfWeek] = currentCron;
    const minutes = time.minute().toString(); //获取分钟
    const hours = time.hour().toString(); //获取小时
    let result = undefined;
    if (!Number.isNaN(Number(hours)) && !Number.isNaN(Number(minutes))) {
      const minutesAndHour = minutes.concat(space).concat(hours).concat(space);
      if (timeType === TimeType.EVERY_DAY)
        result = minutesAndHour.concat("* * *");
      if (timeType !== TimeType.EVERY_DAY)
        result = minutesAndHour
          .concat(dayOfMonth)
          .concat(space)
          .concat(month)
          .concat(space)
          .concat(dayOfWeek);
    }
    if (result) {
      handleChange(result);
    }
  };
```

### 支持受控模式和非受控模式

`React` 组件通常都支持受控模式和非受控模式，按照约定，通过 `value` 和 `onChange` 事件来使组件成为受控模式，这样把状态从父组件传入可以在父组件中轻松拿到具体数值

```tsx
  const { value = defaultCron, onChange } = props;

  // 这里需要判断如果存在 value，转换对应的状态支持组件回显
  useEffect(() => {
    if (value && value !== expression) {
      setExpression(value);
      const { isEveryDay, isEveryWeek, isEveryMonth, cron } = parseCron(value);
      const [minutes, hours, dayOfMonth, , dayOfWeek] = cron;
      const nextTimeType = isEveryDay
        ? TimeType.EVERY_DAY
        : isEveryWeek
        ? TimeType.EVERY_WEEK
        : isEveryMonth
        ? TimeType.EVERY_MONTH
        : timeTypes[0].value;
      setTimeType(nextTimeType);

      if (isEveryWeek) {
        setSelectedDay(dayOfWeek);
      }
      if (isEveryMonth) {
        setSelectedDay(dayOfMonth);
      }

      if (!Number.isNaN(Number(hours)) && !Number.isNaN(Number(minutes))) {
        setSelectedTime(dayjs(`${hours}:${minutes}`, format));
      }
    }
  }, [value]);
```

部分代码参考自[掘金文章](https://juejin.cn/post/7290766145896300585)，去除了秒数和支持受控组件

## 参考链接

- [在线演示](https://codesandbox.io/p/sandbox/croninput-antd-pxr8p5)
- [cron - Wikipedia](https://en.wikipedia.org/wiki/Cron)
- [react+ts手写cron表达式转换组件 - 掘金](https://juejin.cn/post/7290766145896300585)
- [crontab guru - 格式校验工具](https://crontab.guru/#5_4_*_*_sun)
