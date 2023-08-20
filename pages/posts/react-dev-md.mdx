---
title: 项目源码分析 react.dev（二）：Markdown 转换成 HTML
description: 
date: 2023-04-09
tag: React
---

# 项目源码分析 react.dev（二）：Markdown 转换成 HTML

继[上文](/posts/2023/react-dev-intro)分析了 react.dev 的目录结构及本地运行后，本文分析如何将 `content` 文件夹下的 `Markdown` 编写的内容转换成 `HTML` 渲染到页面上。

`content` 文件夹下的 `Markdown` 编写的内容实际都是 [MDX](https://mdxjs.com/) 格式，可以使用 `React` 组件。

核心的逻辑依旧在 `[[...markdownPath]].js` 文件中。其他需要关注的文件有：

1. `src/content` 文件夹包括了所有的 `Markdown` 内容
2. `src/components/MDX` 包括了自定义的组件，可以直接使用在 `Markdown` 内容中
3. `plugins/markdownToHtml.js` 定义了使用到的 `remark` 插件
4. `src/utils/prepareMDX.js`

## 页面组件

先从 `[[...markdownPath]].js` 文件导出的默认组件 `Layout` 看起，这个页面组件会将 `props` 中获得的 `content` 属性传递给 `Page` 组件渲染到页面中。核心代码如下：

```tsx
export default function Layout({content, toc, meta}) {
  const parsedContent = useMemo(
    () => JSON.parse(content, reviveNodeOnClient),
    [content]
  );
  const parsedToc = useMemo(() => JSON.parse(toc, reviveNodeOnClient), [toc]);
  const section = useActiveSection();
  return (
    <Page toc={parsedToc} routeTree={routeTree} meta={meta} section={section}>
      {parsedContent}
    </Page>
  );
}
```

传给 `Layout` 组件的 `content` 这个 `prop` 是 `getStaticProps` 中编译后的 `Markdown` 内容，编译后实际为 `React` 组件代码。

```tsx
export async function getStaticProps(context) {
  // ...省略部分代码
  const output = {
    props: {
      content: JSON.stringify(children, stringifyNodeOnServer),
      toc: JSON.stringify(toc, stringifyNodeOnServer),
      meta,
    },
  };
  return output;
}
```

## getStaticProps 中的编译步骤

### 1. 使用 `fs.readFileSync` 读取文件

```javascript
  const rootDir = process.cwd() + '/src/content/';

  // Read MDX from the file.
  let path = (context.params.markdownPath || []).join('/') || 'index';
  let mdx;
  try {
    mdx = fs.readFileSync(rootDir + path + '.md', 'utf8');
  } catch {
    mdx = fs.readFileSync(rootDir + path + '/index.md', 'utf8');
  }
```

### 2. 先从缓存中读取编译后的内容

如果 `/node_modules/.cache/react-docs-mdx/` 目录下有缓存的编译结果，直接读取缓存不需要重新编译一遍，这样可以加快整个项目的编译速度。在许多比较耗时的操作中，缓存是很常见的降低耗时的手段。

```javascript
  // See if we have a cached output first.
  const {FileStore, stableHash} = require('metro-cache');
  const store = new FileStore({
    root: process.cwd() + '/node_modules/.cache/react-docs-mdx/',
  });
  const hash = Buffer.from(
    stableHash({
      // ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
      // ~~~~ IMPORTANT: Everything that the code below may rely on.
      // ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
      mdx,
      mdxComponentNames,
      DISK_CACHE_BREAKER,
      PREPARE_MDX_CACHE_BREAKER,
      lockfile: fs.readFileSync(process.cwd() + '/yarn.lock', 'utf8'),
    })
  );
  const cached = await store.get(hash);
  if (cached) {
    console.log(
      'Reading compiled MDX for /' + path + ' from ./node_modules/.cache/'
    );
    return cached;
  }
```

### 3. 使用 @mdx-js/mdx 将 `mdx` 编译成 `JavaScript`

这里将使用 `fs.readFileSync` 读取到的 `mdx` 文件内容，提供给 `@mdx-js/mdx` 编译成 `JavaScript` 代码。实际编译之后会是一些 `React` 组件。 其中使用到了 `src/components/MDX` 包括的自定义的组件和 `plugins/markdownToHtml.js` 中定义的 `remark` 插件。

```javascript
  // If we don't add these fake imports, the MDX compiler
  // will insert a bunch of opaque components we can't introspect.
  // This will break the prepareMDX() call below.
  let mdxWithFakeImports =
    mdx +
    '\n\n' +
    mdxComponentNames
      .map((key) => 'import ' + key + ' from "' + key + '";\n')
      .join('\n');

  // Turn the MDX we just read into some JS we can execute.
  const {remarkPlugins} = require('../../plugins/markdownToHtml');
  const {compile: compileMdx} = await import('@mdx-js/mdx');
  const visit = (await import('unist-util-visit')).default;
  const jsxCode = await compileMdx(mdxWithFakeImports, {
    remarkPlugins: [
      ...remarkPlugins,
      (await import('remark-gfm')).default,
      (await import('remark-frontmatter')).default,
    ],
    rehypePlugins: [
      // Support stuff like ```js App.js {1-5} active by passing it through.
      function rehypeMetaAsAttributes() {
        return (tree) => {
          visit(tree, 'element', (node) => {
            if (node.tagName === 'code' && node.data && node.data.meta) {
              node.properties.meta = node.data.meta;
            }
          });
        };
      },
    ],
  });
```

#### @mdx-js/mdx

`@mdx-js/mdx` 这个 `MDX` 编译器的使用方法可以参考文章 [MDX compiler](https://mdxjs.com/packages/mdx/)。大致用法如下：

一些 `mdx` 内容：

```mdx
export const Thing = () => <>World!</>

# Hello, <Thing />
```

使用以下的编译代码将 `mdx` 编译成 `JavaScript`

```javascript
import fs from 'node:fs/promises'
import {compile} from '@mdx-js/mdx'

const compiled = await compile(await fs.readFile('example.mdx'))

console.log(String(compiled))
```

编译后的 `JavaScript` 如下：

```javascript
/* @jsxRuntime automatic @jsxImportSource react */
import {Fragment as _Fragment, jsx as _jsx, jsxs as _jsxs} from 'react/jsx-runtime'

export const Thing = () => _jsx(_Fragment, {children: 'World'})

function _createMdxContent(props) {
  const _components = Object.assign({h1: 'h1'}, props.components)
  return _jsxs(_components.h1, {
    children: ['Hello ', _jsx(Thing, {})]
  })
}

export default function MDXContent(props = {}) {
  const {wrapper: MDXLayout} = props.components || {}
  return MDXLayout
    ? _jsx(MDXLayout, Object.assign({}, props, {children: _jsx(_createMdxContent, props)}))
    : _createMdxContent(props)
}
```

### 4. 将编译后的 `JSX` 代码转换为 `JavaScript` 并且序列化处理成 `React` 组件树

由于上一步经过 `@mdx-js/mdx` 编译后的代码还存在 `JSX`，所以需要使用 `babel` 转换成原生 `JavaScript` 并且序列化处理成 `React` 组件树后返回给页面组件。

```javascript
  const {transform} = require('@babel/core');
  const jsCode = await transform(jsxCode, {
    plugins: ['@babel/plugin-transform-modules-commonjs'],
    presets: ['@babel/preset-react'],
  }).code;

  // Prepare environment for MDX.
  let fakeExports = {};
  const fakeRequire = (name) => {
    if (name === 'react/jsx-runtime') {
      return require('react/jsx-runtime');
    } else {
      // For each fake MDX import, give back the string component name.
      // It will get serialized later.
      return name;
    }
  };
  const evalJSCode = new Function('require', 'exports', jsCode);
  // ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  // THIS IS A BUILD-TIME EVAL. NEVER DO THIS WITH UNTRUSTED MDX (LIKE FROM CMS)!!!
  // In this case it's okay because anyone who can edit our MDX can also edit this file.
  evalJSCode(fakeRequire, fakeExports);
  // ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  const reactTree = fakeExports.default({});
  // console.log('reactTree', reactTree);

  // Pre-process MDX output and serialize it.
  let {toc, children} = prepareMDX(reactTree.props.children);
```

### 5. 将最后的结果返回到 `props` 中传递给页面组件，并且存在缓存中

```javascript
  const output = {
    props: {
      content: JSON.stringify(children, stringifyNodeOnServer),
      toc: JSON.stringify(toc, stringifyNodeOnServer),
      meta,
    },
  };

  // Cache it on the disk.
  await store.set(hash, output);
  return output;
```

### 6. stringifyNodeOnServer 和 reviveNodeOnClient 序列化和反序列化将 React 组件树保存在 JSON 中

这两个核心方法，将 `React` 组件树序列化和反序列化保存在 `JSON` 中。

```javascript
// Deserialize a client React tree from JSON.
function reviveNodeOnClient(key, val) {
  if (Array.isArray(val) && val[0] == '$r') {
    // Assume it's a React element.
    let type = val[1];
    let key = val[2];
    let props = val[3];
    if (type === 'wrapper') {
      type = Fragment;
      props = {children: props.children};
    }
    if (MDXComponents[type]) {
      type = MDXComponents[type];
    }
    if (!type) {
      console.error('Unknown type: ' + type);
      type = Fragment;
    }
    return {
      $$typeof: Symbol.for('react.element'),
      type: type,
      key: key,
      ref: null,
      props: props,
      _owner: null,
    };
  } else {
    return val;
  }
}

// Serialize a server React tree node to JSON.
function stringifyNodeOnServer(key, val) {
  if (val != null && val.$$typeof === Symbol.for('react.element')) {
    // Remove fake MDX props.
    // eslint-disable-next-line @typescript-eslint/no-unused-vars
    const {mdxType, originalType, parentName, ...cleanProps} = val.props;
    return [
      '$r',
      typeof val.type === 'string' ? val.type : mdxType,
      val.key,
      cleanProps,
    ];
  } else {
    return val;
  }
}
```

## 参考链接

- [MDX](https://mdxjs.com/)
- [Using MDX with Next.js](https://nextjs.org/docs/advanced-features/using-mdx)
- [remark](https://github.com/remarkjs/remark)
- [@mdx-js/mdx - MDX compiler](https://mdxjs.com/packages/mdx/)