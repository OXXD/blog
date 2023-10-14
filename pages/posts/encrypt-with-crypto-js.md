---
title: 使用 crypto-js 进行加密和解密
description:
date: 2023-10-14
tag: Web
---

# 使用 crypto-js 进行加密和解密

网页开发中有一些隐私数据不能明文展示或者存储，比如用户个人信息等。这个时候就需要对这些敏感信息进行加密解密处理，
前端中比较常见使用到的是 [crypto-js](https://github.com/brix/crypto-js) 这一加密库和 `AES` 加密算法（目前常见的较流行的加密算法）。

本文介绍如何使用 `crypto-js` 进行加密和解密。

## 示例

以一个简单的加密和解密示例，只需要调用  `CryptoJS.AES.encrypt` 和 `CryptoJS.AES.decrypt` 即可实现 `AES` 加密解密，需要传入相对应的密钥。

```javascript
const CryptoJS = require("crypto-js");

// 加密
const message = "Hello, World!";
const secretKey = "MySecretKey";
const encryptedMessage = CryptoJS.AES.encrypt(message, secretKey).toString();

// 解密
const decryptedBytes = CryptoJS.AES.decrypt(encryptedMessage, secretKey);
const decryptedMessage = decryptedBytes.toString(CryptoJS.enc.Utf8);

console.log("Encrypted Message:", encryptedMessage);
console.log("Decrypted Message:", decryptedMessage);
```

输出：

```bash
Encrypted Message: U2FsdGVkX18G/2dkfUWj+rYa4e62g6UiLDZgvmsDJj8=
Decrypted Message: Hello, World!
```

## 封装成可复用的方法

我们将加密和解密方法进行一层封装，传入一些自定义的预制选项（如密钥长度，`mode` 和 `padding`），可以方便后续在不同地方代码复用和快速实现加密解密。

```typescript
/**
 * 加密
 * @param {string} str 需要加密的数据
 * @param {string} secret 密钥
 * @returns
 */
export function encrypt(str: string, secret: string) {
  const cryptoKey = CryptoJS.enc.Utf8.parse(secret);
  const cryptoOption = {
    iv: CryptoJS.enc.Utf8.parse(secret.substring(0, 16)),
    mode: CryptoJS.mode.CBC,
    padding: CryptoJS.pad.Pkcs7,
  };
  const encryptedStr = CryptoJS.AES.encrypt(str, cryptoKey, cryptoOption).toString();
  return encryptedStr;
}

/**
 * 解密
 * @param {string} str 需要解密的数据
 * @param {string} secret 密钥
 * @returns
 */
export function decrypt(str: string, secret: string) {
  const cryptoKey = CryptoJS.enc.Utf8.parse(secret);
  const cryptoOption = {
    iv: CryptoJS.enc.Utf8.parse(secret.substring(0, 16)),
    mode: CryptoJS.mode.CBC,
    padding: CryptoJS.pad.Pkcs7,
  };
  const decryptedStr = CryptoJS.AES.decrypt(str, cryptoKey, cryptoOption).toString(
    CryptoJS.enc.Utf8,
  );
  return decryptedStr;
}
```

## 其他更多用法

除了 AES 加密解密外，`crypto-js` 还提供了很多其他的加密算法和功能，可根据不同的需求进行使用。具体可以参考 [文档](https://cryptojs.gitbook.io/docs/)

```javascript
var hash = CryptoJS.MD5("Message");
var hash = CryptoJS.SHA1("Message");
var hash = CryptoJS.SHA256("Message");
```

**注意：** `crypto-js` 是一个 `JavaScript` 实现的加密库，前端存储密钥和加密解密数据并非完全安全，在一些安全性要求较高的场景中，可能需要额外的考虑。

## 参考链接

- [crypto-js](https://github.com/brix/crypto-js)
- [CryptoJS 文档](https://cryptojs.gitbook.io/docs/)
- [AES](https://zh.wikipedia.org/wiki/%E9%AB%98%E7%BA%A7%E5%8A%A0%E5%AF%86%E6%A0%87%E5%87%86)
