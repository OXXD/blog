---
title: 浏览器中如何获取时区名称
description: 
date: 2023-07-09
tag: Web
---

# 浏览器中如何获取时区名称

国际化项目中处理时间时，可能会遇到需要处理时区的场景，比如切换不同时区，显示时区列表选择，获取时区对应的时间等场景。那么在浏览器中应该如何处理这些场景，获得时区名称的数据呢。

查找一些资料后，发现以下两个较方便的方法。

## moment-timezone

使用 `moment-timezone` 可以轻松的获取到时区名称的列表数据。

### 安装

```bash
npm install moment-timezone
```

### 使用

```javascript
var moment = require('moment-timezone');
moment().tz.names();
// 输出内容：
// ["Africa/Abidjan", "Africa/Accra", "Africa/Addis_Ababa", ...]
```

## Intl.supportedValuesOf()

[TC39 新的提案](https://github.com/tc39/proposal-intl-enumeration) 中提出的 `Intl.supportedValuesOf()` 这个 `API` 可以让浏览器原生支持获取时区名称数据等一些其他数据，目前已被大多数浏览器支持，不过使用是还是需要注意兼容性问题。`FormatJS` 提供了一个 [polyfill](https://github.com/formatjs/formatjs/tree/main/packages/intl-enumerator)

```javascript
console.log(Intl.supportedValuesOf('timeZone'));
// 输出内容：
// ["Africa/Abidjan", "Africa/Accra", "Africa/Addis_Ababa", ...]
```

还支持其他格式的数据：

- calendar
- collation
- currency
- numberingSystem
- timeZone
- unit

## 参考链接

- [Moment Timezone Documentation](https://momentjs.com/timezone/docs/#/data-loading/getting-zone-names/)
- [Intl.supportedValuesOf()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/supportedValuesOf)
- [TC39 - Intl Enumeration API Specification](https://github.com/tc39/proposal-intl-enumeration)
- [A polyfill of Intl.supportedValuesOf in FormatJS](https://github.com/formatjs/formatjs/tree/main/packages/intl-enumerator)
- [Timezone list and guess time zone feature #695](https://github.com/iamkun/dayjs/issues/695#issuecomment-1225504895)
- [dayjs Time Zone](https://day.js.org/docs/en/timezone/timezone)
- [IANA Time Zone Database](https://www.iana.org/time-zones)
- [How to get list of all timezones in javascript on Stack Overflow](https://stackoverflow.com/questions/38399465/how-to-get-list-of-all-timezones-in-javascript)