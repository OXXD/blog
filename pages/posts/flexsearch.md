---
title: 使用 FlexSearch 实现本地全文搜索
description: 
date: 2024-03-03
tag: 
---

# 使用 FlexSearch 实现本地全文搜索

搜索功能是网站中提供用户快速查找内容、在不同内容中跳转的一个重要功能，尤其在文档类型网站中几乎是必不可少的常见功能。

实现全文搜索一般都需要将内容存储在数据库中并且由后端提供搜索接口实现。在目前主流的预编译（SSG）静态网站中，通过提前编译时将内容存储为文件格式提供给浏览器实现本地搜索也是另一种常见的方案。

[FlexSearch](https://github.com/nextapps-de/flexsearch) 是一个用于客户端（浏览器、`Node.js`）和服务器端（`Node.js`）的高性能全文搜索引擎，主要特点：快速且轻量级、支持多种搜索模式，可以在浏览器和 `Node.js` 环境中使用。本文介绍如何利用 [FlexSearch](https://github.com/nextapps-de/flexsearch) 实现本地的全文搜索。

## 使用方式

实现搜索的流程为：

1. 创建索引
2. 将数据加入索引
3. 调用搜索函数根据输入文本执行搜索。

### 创建索引

`FlexSearch` 提供了三种不同的索引，按照原文为：

1. `Index` is a flat high performance index which stores id-content-pairs.
2. `Worker` / `WorkerIndex` is also a flat index which stores id-content-pairs but runs in background as a dedicated worker thread.
3. `Document` is multi-field index which can store complex JSON documents (could also exist of worker indexes).

简单翻译如下：

1. `Index` 是一个高性能的平坦索引，用于存储 id-content 对。
2. `Worker / WorkerIndex` 也是一个平坦索引，存储 id-content 对，但在后台作为专用的工作线程运行。它适用于需要在独立的线程中执行搜索操作的场景，以提高性能。
3. `Document` 是一个多字段索引，可以存储复杂的 JSON 文档，可以包含多个字段和子字段。它也可以由 Worker 索引组成。适用于需要存储和检索多个字段的场景

可以看到这三种索引类型有各自的使用场景，`Index` 存储的 id-content 对适用于需要快速搜索的场景，如果有性能需求不希望影响页面 UI 渲染可以使用 `Worker` 索引，如果需要多字段搜索那么 `Document` 索引会更适合（一般全文搜索也会选用这个方式，因为需要存储多个字段）

以 `Index` 索引为例，创建一个索引，只需要新建一个对象即可。

```javascript
var index = new FlexSearch.Index();
```

创建索引时也可以配置不同选项，如：

```javascript
var index = new Index({
    charset: "latin:extra",
    tokenize: "reverse",
    resolution: 9
});
```

不同参数会有不同的效果，具体需要根据实际需要参考[文档](https://github.com/nextapps-de/flexsearch?tab=readme-ov-file#options)配置

### 将数据加入索引

创建索引后，把需要搜索的数据通过 `add` 函数加入索引中。注意我们需要为每份数据指定一个 `id`，最好是数值类型，需要保持唯一。这样后续如果需要更新索引内容时，可以根据 `id` 直接更新。

```javascript
index.add(1, "Hello World");
index.add(2, "FlexSearch is awesome");
index.add(3, "Full-text search made easy");
```

### 搜索

创建好索引和添加好内容后，执行搜索就十分简单了，只需要执行 `search` 函数传入搜索文本即可。`Index` 索引返回的结果是 `id` 数组。

```javascript
index.search(query)
```

如果需要限制搜索数量或者其他搜索选项，可以传入不同的参数

```javascript
index.search(text, limit);
index.search(text, options);
index.search(text, limit, options);
```

可以看出，`FlexSearch` 的 `API` 实现简单易用，主要使用以下几个常见的 `API` 即可完成搜索功能。

```javascript
index.add(id, text);
index.search(text);
index.search(text, limit);
index.search(text, options);
index.search(text, limit, options);
index.search(options);
```

```javascript
document.add(doc);
document.add(id, doc);
document.search(text);
document.search(text, limit);
document.search(text, options);
document.search(text, limit, options);
document.search(options);
```

```javascript
worker.add(id, text);
worker.search(text);
worker.search(text, limit);
worker.search(text, options);
worker.search(text, limit, options);
worker.search(text, limit, options, callback);
worker.search(options);
```

其他更多 `API` 可以参考[文档](https://github.com/nextapps-de/flexsearch?tab=readme-ov-file#api-overview)

## 使用 `Document` 索引实现全文搜索

一般全文搜索会涉及多个字段或者需要存储和获取不同原文档数据的字段和内容，所以使用 `Document` 索引会更为合适。

`Document` 索引的实现方式和 `Index` 索引基本一致，不同的地方在于我们需要指定需要加入到索引的内容字段，如果有多个字段都需要被索引和搜索，那么需要指定多个字段。

### 创建索引

```javascript
const index = new Document({
    document: {
        id: "id",
        index: ["content"]
    }
});
```

```javascript
var docs = [{
    id: 0,
    title: "Title A",
    content: "Body A"
},{
    id: 1,
    title: "Title B",
    content: "Body B"
}];

const index = new Document({
    document: {
        id: "id",
        index: ["title", "content"]
    }
});
```

### 将数据加入索引

```javascript
index.add({ 
    id: 0, 
    content: "some text"
});
```

### 搜索

```javascript
index.search(query)
```

默认的返回结果为字段和符合搜索结果的 `id` 数组

```javascript
[{
    field: "title",
    result: [0, 1, 2]
},{
    field: "content",
    result: [3, 4, 5]
}]
```

如果需要返回完整的数据，可以在执行搜索时加上选项 `enrich: true`

```javascript
index.search(query, { enrich: true });
```

返回结果

```javascript
[{
    field: "title",
    result: [
        { id: 0, doc: { /* document */ }},
        { id: 1, doc: { /* document */ }},
        { id: 2, doc: { /* document */ }}
    ]
},{
    field: "content",
    result: [
        { id: 3, doc: { /* document */ }},
        { id: 4, doc: { /* document */ }},
        { id: 5, doc: { /* document */ }}
    ]
}]
```

## 参考链接

- [FlexSearch](https://github.com/nextapps-de/flexsearch)
- [How MDN’s autocomplete search works](https://hacks.mozilla.org/2021/08/mdns-autocomplete-search/)
- [How MDN’s site-search works](https://hacks.mozilla.org/2021/03/how-mdns-site-search-works/)
- [MDN 实现](https://github.com/mdn/yari/blob/main/client/src/search.tsx)
- [Nextra FlexSearch 实现](https://github.com/shuding/nextra/blob/main/packages/nextra-theme-docs/src/components/flexsearch.tsx)
