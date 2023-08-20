---
title: 如何解决数组排序 Array.sort 遇到 undefined 字段排序无效
description: 
date: 2022-11-12
tag: JavaScript
---

# 如何解决数组排序 Array.sort 遇到 undefined 字段排序无效

## 问题

最近在项目中遇到排序没有生效的问题，排查之后发现了是 `sort` 方法遇到字段 `undefined` 时需要特殊处理。

数据

```javascript
var array = [
  { number: 2 },
  {},
  { number: 1 }
];
```

使用 `sort` 方法根据 `number` 字段从小到大排序

```javascript
array.sort((a, b) => a.number - b.number);
```

输出的结果不是我们想要的

```javascript
[ { number: 2 }, {}, { number: 1 } ]
```

## 解决方法

`sort` 方法提供的排序方法中，如果返回的数字是 0 那么会保持原顺序，如果返回的数字 >0 那么会将 a 至于 b 之后，如果返回的数字 < 0 那么会将 a 至于 b 之前。

如果遇到字段可能是 `undefined`，那么我们需要特殊判断处理下。

首先判断如果 a,b 的排序字段都是 `undefined` 返回 0 保持原顺序。

再判断如果 a 的字段是 `undefined` 但 b 的排序字段存在则返回 >0 将 a 至于 b 之后。

再判断 b 的排序字段如果是 `undefined` 但 a 的排序字段存在则返回 <0 将 a 至于 b 之前。

最后 a,b 的排序字段都存在时，使用 a,b 的排序字段计算后的返回结果判断顺序

```javascript
array.sort((a, b) => {
  // two undefined values should be treated as equal ( 0 )
  if( typeof a.number === 'undefined' && typeof b.number === 'undefined' )
    return 0;
  // if a is undefined and b isn't a should have a lower index in the array
  else if( typeof a.number === 'undefined' )
    return 1;
    // if b is undefined and a isn't a should have a higher index in the array
  else if( typeof b.number === 'undefined' )
    return -1;
    // if both numbers are defined compare as normal
  else
    return a.number - b.number;
});
```

输出结果

```javascript
[ { number: 1 }, { number: 2 }, {} ]
```

## 参考链接

- [Stack Overflow](https://stackoverflow.com/questions/56312968/javascript-sort-object-array-by-number-properties-which-include-undefined)
- [MDN 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)