---
title: 如何给 Next.js 项目配置代码格式化和校验（ESLint + Prettier + husky）
description: 
date: 2023-08-19
tag: React, Next.js
---

# 如何给 Next.js 项目配置代码格式化和校验（ESLint + Prettier + husky）

目前在前端项目中的工程化已经十分成熟，相应工具也趋于稳定。使用 `ESLint` 给项目加上代码校验，在编写代码时即遵守规范提前发现错误，使用 `Prettier` 格式化代码让团队内不同成员风格一致，使用 `Git` 钩子在提交时校验提交内容和自动修复格式化等等。

本文介绍在 `Next.js` 项目中如何正确配置和使用这些工具，帮助我们提升开发效率和团队内的统一代码风格、规范。

可以在 `Github` 上看到完整的[项目模版源码](https://github.com/OXXD/next-template)

## ESLint

使用 `create-next-app` 创建的 `Next.js` 项目已经配置好了 `ESLint`，只需要按照项目需要修改对应配置即可。这里我们加上 `prettier` 的配置，让 `ESLint` 和 `Prettier` 能够更和谐的一起工作。

`.eslintrc.json`

```json
{
  "extends": ["next/core-web-vitals", "prettier"]
}
```

## Prettier

参照[文档](https://prettier.io/docs/en/install#eslint-and-other-linters)，安装 `Prettier` 和配合 `ESLint` 使用只需要安装 `eslint-config-prettier` 即可。

```bash
npm i -D prettier eslint-config-prettier 
# 额外的插件
npm i -D prettier-plugin-organize-imports prettier-plugin-tailwindcss
```

如果使用了 `Tailwind CSS` 推荐额外安装 [prettier-plugin-tailwindcss](https://github.com/tailwindlabs/prettier-plugin-tailwindcss)，可以帮忙自动排序 `className`。并且我们额外安装可以帮助排序 `import` 的插件。

接着在 `.prettierrc.json` 文件中配置一下。

```json
{
  "plugins": [
    "prettier-plugin-tailwindcss",
    "prettier-plugin-organize-imports"
  ],
  "tailwindFunctions": ["classNames"],
  "singleQuote": true,
  "trailingComma": "es5"
}
```

## husky + lint-staged

使用 `husky` 和 `lin-staged` 可以在 `Git` 提交代码时对提交的部分进行 `ESLint` 的代码校验和 `prettier` 的格式化，避免有些新同学编辑器中没有装对应插件和开启自动修复。安装配置也十分简单。

```bash
npm install --save-dev husky lint-staged
npx husky install
npm pkg set scripts.prepare="husky install"
npx husky add .husky/pre-commit "npx lint-staged"
```

`.lintstagedrc.js`

```javascript
const path = require('path');

const buildEslintCommand = (filenames) =>
  `next lint --fix --file ${filenames
    .map((f) => path.relative(process.cwd(), f))
    .join(' --file ')}`;

module.exports = {
  '*.{js,jsx,ts,tsx}': [buildEslintCommand], // 这些格式的文件在提交时交给 ESLint 校验
  '**/*.{js,jsx,tsx,ts,less,md,json}': ['prettier --write'], // 这些格式的文件在提交时让 prettier 格式化
};
```

提交代码时如果看到以下提示，即说明配置成功。

![nextjs-husky](/images/minigame/nextjs-husky.png)

## 同步编辑器设置和扩展

最后，我们在项目中加上 `.vscode` 文件夹，配置编辑器的扩展和自动校验和修复的设置，让其他同学接入项目也能快速上手和使用相同的配置、扩展。

`.vscode/extensions.json`

```json
{
  "recommendations": [
    // Linting / Formatting
    "esbenp.prettier-vscode",
    "dbaeumer.vscode-eslint",
    "bradlc.vscode-tailwindcss"
  ]
}
```

`.vscode/settings.json`

```json
{
  // Formatting using Prettier by default for all languages
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  // Formatting using Prettier for JavaScript, overrides VSCode default.
  "[javascript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  // Linting using ESLint.
  "eslint.validate": [
    "javascript",
    "javascriptreact",
    "typescript",
    "typescriptreact"
  ],

  // Enable file nesting.
  "explorer.fileNesting.enabled": true,
  "explorer.fileNesting.patterns": {
    "*.ts": "$(capture).test.ts, $(capture).test.tsx",
    "*.tsx": "$(capture).test.ts, $(capture).test.tsx"
  }
}
```

## commitlint

如果团队对提交时填写的文案需要统一按照格式，可以使用 [commitlint](https://commitlint.js.org/#/?id=getting-started) 加上 `Git` 提交时的文案校验，统一使用某一种规范。

## 参考链接

- [项目模版源码](https://github.com/OXXD/next-template)
- [Configuring: ESLint | Next.js](https://nextjs.org/docs/pages/building-your-application/configuring/eslint)
- [Next.js ESlint Usage With Other Tools](https://nextjs.org/docs/pages/building-your-application/configuring/eslint#usage-with-other-tools)
- [Prettier](https://prettier.io/docs/en/install)
- [eslint-config-prettier](https://github.com/prettier/eslint-config-prettier)
- [prettier-plugin-tailwindcss](https://github.com/tailwindlabs/prettier-plugin-tailwindcss)
- [husky](https://github.com/typicode/husky)
- [lint-staged](https://github.com/okonet/lint-staged)
- [commitlint](https://commitlint.js.org/#/?id=getting-started)
