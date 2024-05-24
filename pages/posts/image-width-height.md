---
title: 前端如何判断上传图片尺寸（宽度和高度）
description: 
date: 2024-05-24
tag: Web
---

# 前端如何判断上传图片尺寸（宽度和高度）

## 实现

### 核心实现

```javascript
const img = new Image();
img.onload = function() {
  const width = img.width;
  const height = img.height;
}
img.src = 'https://www.gstatic.com/webp/gallery/1.jpg'
```

1. 创建一个 `Image` 对象
2. 设置 `Image` 对象的 `src` 属性
3. 在加载完成后检测尺寸。通过 `width` 和 `height` 属性

### 上传中获取

一般需要判断上传图片尺寸的场景都是在上传图片时，需要判断。上传图片完成后，只需要通过 [FileReader](https://developer.mozilla.org/en-US/docs/Web/API/FileReader) 或者 [URL.createObjectURL(file)](https://developer.mozilla.org/en-US/docs/Web/API/URL/createObjectURL_static) 将图片文件转换为临时 `URL` 并将其赋值给 `Image` 对象的 `src` 属性

```html
<input type="file" id="imageInput" accept="image/*">
<p id="output"></p>

<script>
  document.getElementById('imageInput').addEventListener('change', function (event) {
    const file = event.target.files[0];
    if (file) {
       const reader = new FileReader();
        reader.onload = function(e) {
            const img = new Image();
            img.onload = function() {
                const width = img.width;
                const height = img.height;
                document.getElementById('output').textContent = `Image Dimensions: ${width} x ${height}`;
            }
            img.src = e.target.result;
        }
  
        reader.readAsDataURL(file);
    } else {
      document.getElementById('output').textContent = 'No file selected';
    }
  });
</script>
```

### 封装一下

将从上传的图片文件中获取 `src` 和获取图片尺寸的函数使用  `Promise` 封装一下，可以更方便复用

```javascript
function getImageSize(src) {
  return new Promise((resolve, reject) => {
    const img = new Image();
    img.onload = function () {
      const width = img.width;
      const height = img.height;
      resolve({ width, height })
    }
    img.src = src
  })
}

function getImageSrcFromFile(file) {
  return new Promise((resolve, reject) => {
    const reader = new FileReader();
    reader.onload = function (e) {
      resolve(e.target.result)
    }
    reader.readAsDataURL(file);
  })
}

function getImageSrc(file) {
  return URL.createObjectURL(file)
  // 注意后续需要调用 URL.revokeObjectURL(src) 释放内存
}
```

使用：

```javascript
document.getElementById('imageInput').addEventListener('change', async function (event) {
  const file = event.target.files[0];
  if (file) {
    const src = await getImageSrcFromFile(file);
    const { width, height } = await getImageSize(src);
    document.getElementById('output').textContent = `Image Dimensions: ${width} x ${height}`;
  } else {
    document.getElementById('output').textContent = 'No file selected';
  }
});
```

## 参考链接

- [FileReader - Web APIs | MDN](https://developer.mozilla.org/en-US/docs/Web/API/FileReader)
- [HTMLImageElement: Image() constructor - Web APIs | MDN](https://developer.mozilla.org/en-US/docs/Web/API/HTMLImageElement/Image)
- [URL: createObjectURL() static method - Web APIs | MDN](https://developer.mozilla.org/en-US/docs/Web/API/URL/createObjectURL_static)
