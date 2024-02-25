---
title: 制作一个搜索弹窗组件（Tailwind CSS + shadcn/ui）
description: 
date: 2024-02-25
tag: React, Tailwind CSS
---

# 制作一个搜索弹窗组件（Tailwind CSS + shadcn/ui）

## 实现效果

![react-search-dialog](/images/minigame/react-search-dialog.gif)

[在线演示](https://stackblitz.com/edit/stackblitz-starters-abvyuz)

## 如何实现

### 创建一个项目，并引入 shadcn/ui 组件库

这里我们直接使用 `shadcn/ui` 中的 `Dialog` 组件，也可以按照项目所使用的组件库自行选择或者封装。

1. 创建一个 Next.js 项目

```bash
npx create-next-app@latest my-app --typescript --tailwind --eslint
```

2. 初始化 `shadcn/ui` 组件库，具体的步骤可以参考[文档](https://ui.shadcn.com/docs/installation/next)

```bash
npx shadcn-ui@latest init
```

3. 增加 `Dialog`, `Input` 等组件

```bash
npx shadcn-ui@latest add dialog
npx shadcn-ui@latest add input
```

### 组件结构

先按照界面实现组件，主要分为触发弹窗的搜索按钮，搜索弹窗组件中的输入框和搜索结果。

按照弹窗组件的使用方式，我们可以将触发弹窗的搜索按钮放在 `DialogTrigger` 中，将搜索弹窗组件的输入框和搜索结果放在弹窗主要内容内。

```tsx
import { Button } from '@/components/ui/button';
import {
  Dialog,
  DialogClose,
  DialogContent,
  DialogHeader,
  DialogTrigger,
} from '@/components/ui/dialog';
import type { SearchResult } from '@/types';
import { Loader2, SearchIcon } from 'lucide-react';

export default function Search(props: SearchProps) {
  return (
    <Dialog open={open} onOpenChange={handleOpenChange}>
      <DialogTrigger asChild>
        <Button
          variant="secondary"
          className="h-9 justify-start rounded-2xl bg-transparent pl-2 focus-visible:ring-0 focus-visible:ring-offset-0 lg:w-64 lg:bg-[#EBEDF0] lg:hover:bg-[#EBEDF0]/70"
        >
          <SearchIcon className="text-black-1" size={24} />
          <span className="text-gray-1 ml-1 hidden lg:inline">搜索</span>
        </Button>
      </DialogTrigger>
      <DialogContent className="sm:max-w[100vw] top-0 translate-y-0 gap-0 p-0 lg:top-28 lg:max-w-[640px] 2xl:max-w-[720px]">
        <DialogHeader>
          <div className="flex items-center border-b px-4 py-2">
            <SearchIcon size={24} className="text-gray-1" />
            <Input
              ref={inputRef}
              className="text-black-1 placeholder:text-gray-1 !border-none text-xl !shadow-none !outline-none !ring-0"
              placeholder="搜索文档"
              value={value}
              onChange={(e) => {
                onChange(e.target.value);
              }}
            ></Input>
            <DialogClose className="text-primary flex-shrink-0 border-l pl-4 text-xl">
              取消
            </DialogClose>
          </div>
        </DialogHeader>
        <div className="max-h-screen min-h-screen overflow-auto lg:max-h-96 lg:min-h-[198px]">
          <SearchResults
            results={results}
            onClickResult={handleClickResult}
          />
        </div>
      </DialogContent>
    </Dialog>
  );
}

type SearchResultsProps = {
  results?: SearchResult[];
  onClickResult?: () => void;
};

function SearchResults(props: SearchResultsProps) {
  const { results, onClickResult } = props;

  if (!results?.length) {
    return <EmptyResult>查无结果</EmptyResult>;
  }

  return <div className="px-4 pb-16 lg:pb-4">{/* 列表，展示搜索结果 */}</div>;
}

function EmptyResult({ children }: { children: React.ReactNode }) {
  return (
    <div className="text-gray-1 h-full min-h-48 pt-16 text-center text-xl font-medium">
      {children}
    </div>
  );
}
```

### 实现交互

搜索需要考虑到页面的交互和实际搜索逻辑的实现，实际实现中有多种方式，比如使用`hooks`封装逻辑，使用不同组件封装等。

这里我们参考通用的组件设计实践，将 `UI` 组件和逻辑分开。`Search` 这个组件只负责 `UI` 相关的交互和展示，通过受控组件的方式和其他实际实现搜索逻辑的逻辑组件配合使用，这样可以达到组件分工合理、代码复用、支持不同搜索引擎实现等优点。

所以 `Search` 这个组件的交互逻辑其实很简单，只需要控制弹窗开启关闭，和控制搜索输入框中输入的值的改变触发搜索即可。

```tsx
type SearchProps = {
  /** 搜索输入框中的值 */
  value: string;
  /** 搜索输入框中的值改变事件，父组件可以通过该事件触发搜索逻辑并且更新搜索结果 */
  onChange: (value: string) => void;
  /** 搜索结果，根据这个 prop 来更新搜索结果展示 */
  results: SearchResult[];
  onActive?: () => void;
  onInActive?: () => void;
};
```

```tsx
  const { value, onChange, results, onActive, onInActive } =
    props;

  const [open, setOpen] = useState(false);
  const inputRef = useRef<HTMLInputElement>(null);

  const isSearching = value && Boolean(value);
  
  // 监听键盘，可以通过 / 或者 crtl+k 打开搜索弹窗
  useEffect(() => {
    const INPUTS = ['INPUT', 'SELECT', 'TEXTAREA'];
    const handleKeydown = (e: KeyboardEvent): void => {
      const isEditingContent = (event: KeyboardEvent) => {
        const element = event.target as HTMLElement;
        const tagName = element.tagName;
        return element.isContentEditable || INPUTS.includes(tagName);
      };

      if (
        !isEditingContent(e) &&
        (e.key === '/' ||
          (e.key === 'k' &&
            (e.metaKey /* for Mac */ || /* for non-Mac */ e.ctrlKey)))
      ) {
        setOpen(true);
        e.preventDefault();
      }
    };

    window.addEventListener('keydown', handleKeydown);
    return () => {
      window.removeEventListener('keydown', handleKeydown);
    };
  }, []);

  // 监听弹窗改变事件，通过事件通知给父组件
  const handleOpenChange = (val: boolean) => {
    setOpen(val);
    if (val) {
      onActive?.();
    } else {
      onInActive?.();
    }
  };
```

这篇文章简单介绍了如何制作一个搜索组件的 `UI`，实际要实现搜索功能，需要结合其他搜索引擎（如[flexsearch](https://github.com/nextapps-de/flexsearch)）或者搜索接口，会放在其他文章单独讨论。

## 参考链接

- [shadcn/ui](https://ui.shadcn.com/)
- [Dialog – Radix Primitives](https://www.radix-ui.com/primitives/docs/components/dialog)
- [Next.js](https://nextjs.org/)
- [flexsearch](https://github.com/nextapps-de/flexsearch)