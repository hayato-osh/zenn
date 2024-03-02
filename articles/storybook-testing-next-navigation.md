---
title: "@storybook/test を使って next/navigation をテストする"
emoji: "💯"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['storybook', 'nextjs']
publication_name: "yumemi_inc"
published: true
---

Storybook の play function や test-runner が登場し、Storybook をコンポーネントカタログの用途だけでなく、テストに活用するプロジェクトは増えていると思います。
今回は play function を使って `next/navigation` および `next/router` のテストをする方法を紹介します。

## 結論
`.storybook/preview.tsx` を以下のようにしてください。

```tsx:preview.tsx
import { action } from '@storybook/addon-actions'
import { fn } from '@storybook/test';

const defaultNavigationContext = {
  push: fn((...args: any[]) => {
    action('nextNavigation.push')(...args);
  }),
  replace: fn((...args: any[]) => {
    action('nextNavigation.replace')(...args);
  }),
  forward: fn((...args: any[]) => {
    action('nextNavigation.forward')(...args);
  }),
  back: fn((...args: any[]) => {
    action('nextNavigation.back')(...args);
  }),
  prefetch: fn((...args: any[]) => {
    action('nextNavigation.prefetch')(...args);
  }),
  refresh: fn(() => {
    action('nextNavigation.refresh')();
  }),
};

const preview: Preview = {
  parameters: {
    nextjs: {
      appDirectory: true,
      navigation: defaultNavigationContext,
    },
  },
};

export default preview;
```

これだけで Actions パネルに出力する内容を変えることなく `next/navigation` の内容をスパイし、テストを書くことができます。

pages router を使っている場合は以下のように書けばよいです。

```tsx:preview.tsx
import { action } from '@storybook/addon-actions'
import { fn } from '@storybook/test';

const defaultRouter = {
  push: fn(async (...args: any[]) => {
    action('nextRouter.push')(...args);
    return await Promise.resolve(true);
  }),
  replace: fn(async (...args: any[]) => {
    action('nextRouter.replace')(...args);
    return await Promise.resolve(true);
  }),
  reload: fn((...args: any[]) => {
    action('nextRouter.reload')(...args);
  }),
  back: fn((...args: any[]) => {
    action('nextRouter.back')(...args);
  }),
  forward: fn(() => {
    action('nextRouter.forward')();
  }),
  prefetch: fn(async (...args: any[]) => {
    action('nextRouter.prefetch')(...args);
    await Promise.resolve();
  }),
  beforePopState: fn((...args: any[]) => {
    action('nextRouter.beforePopState')(...args);
  }),
  events: {
    on: fn((...args: any[]) => {
      action('nextRouter.events.on')(...args);
    }),
    off: fn((...args: any[]) => {
      action('nextRouter.events.off')(...args);
    }),
    emit: fn((...args: any[]) => {
      action('nextRouter.events.emit')(...args);
    }),
  },
};

const preview: Preview = {
  nextjs: {
    router: defaultRouter,
  },
}
```

これだけで各コンポーネントで `next/navigation` でクエリパラメータが書き換わることをテストしたり、ページ遷移などの関数が実行されたことをテストできるようになります。

ちなみにデフォルトの `@storybook/next` でスタブされたルーターの内容は以下で紹介されており、それを参考に `fn` でラップしただけなのでスタブの内容が変わるわけではないので安心です。
https://www.npmjs.com/package/@storybook/nextjs#default-navigation-context

雑な例ですが、ユーザーの入力によってクエリパラメータが書き換わるような検索コンポーネントを例にすると以下のように書くことができます。

```tsx:Search.stories.tsx
import { Meta, StoryObj } from '@storybook/react'
import { expect, userEvent, within } from '@storybook/test'

import Search from "./search";

const meta = {
  component: Search,
  args: {
    placeholder: 'placeholder'
  },
} satisfies Meta<typeof Search>

export default meta;

type Story = StoryObj<typeof meta>

export const Default: Story = {
  play: async ({ canvasElement, parameters }) => {
    const canvas = within(canvasElement);
    await userEvent.type(canvas.getByRole('textbox'), 'keyword')

    await expect(
      parameters.nextjs.navigation.replace
    ).toHaveBeenCalledWith('/?query=keyword')
  }
}
```

それではよき Storybook ライフを。
