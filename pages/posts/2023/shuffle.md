---
title: 洗牌算法：从数组中随机获取指定数量的元素
description: 
date: 2023-05-28
tag: JavaScript
---

# 洗牌算法：从数组中随机获取指定数量的元素

项目中从数组中随机选择一些元素是一个常见的需求。本文中，我们将介绍一种流行的算法，即 Fisher-Yates 洗牌算法，它可以帮助我们轻松地从数组中随机选择指定数量的元素。

Fisher-Yates 洗牌算法是一种用于将数组随机排序的算法，它的时间复杂度为 O(n)。该算法的基本思想是：从数组的最后一个元素开始，随机选择一个元素并将其与当前元素交换，然后继续向前遍历数组，直到第一个元素为止。在此过程中，每个元素都有相等的概率被选择。通过这种方式，我们可以确保数组中的每个元素都有相等的机会被选择，从而产生一个随机排序的数组。

## 实现

```javascript
function getRandomFromArray(arr, num) {
  // 先将数组的副本创建出来
  const shuffledArray = arr.slice();
  let i = arr.length;
  const result = [];

  // 洗牌算法
  while (i--) {
    const randomIndex = Math.floor((i + 1) * Math.random());
    const temp = shuffledArray[randomIndex];
    shuffledArray[randomIndex] = shuffledArray[i];
    shuffledArray[i] = temp;
  }

  // 返回选取的结果
  for (i = 0; i < num; i++) {
    result.push(shuffledArray[i]);
  }

  return result;
}
```

## 测试

```javascript
const arr = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
const num = 4; // 选取的数量
const result = getRandomFromArray(arr, num);
console.log(result); // 输出选取的结果
```

## Lodash 中的实现

`Lodash` 中也有提供一个 [shuffle](https://lodash.com/docs/4.17.15#shuffle) 方法，其实现也是基于 Fisher-Yates 洗牌算法，其中[部分源码](https://github.com/lodash/lodash/blob/4.17.15/lodash.js#L6711)如下。

```javascript
/**
 * A specialized version of `_.shuffle` which mutates and sets the size of `array`.
 *
 * @private
 * @param {Array} array The array to shuffle.
 * @param {number} [size=array.length] The size of `array`.
 * @returns {Array} Returns `array`.
 */
function shuffleSelf(array, size) {
  var index = -1,
      length = array.length,
      lastIndex = length - 1;

  size = size === undefined ? length : size;
  while (++index < size) {
    var rand = baseRandom(index, lastIndex),
        value = array[rand];

    array[rand] = array[index];
    array[index] = value;
  }
  array.length = size;
  return array;
}
```

## 参考链接

- [Fisher–Yates shuffle](https://en.wikipedia.org/wiki/Fisher%E2%80%93Yates_shuffle)
- [Lodash](https://lodash.com/docs/4.17.15#shuffle)