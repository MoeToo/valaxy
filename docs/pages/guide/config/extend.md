---
title: Extend Config
title_zh-CN: 扩展配置
categories:
  - config
---

> [packages/valaxy/node/type.ts](https://github.com/YunYouJun/valaxy/blob/main/packages/valaxy/node/types.ts)

### Unocss Presets

```ts
// types
export interface ValaxyExtendConfig {
  /**
   * Markdown Feature
   */
  features: {
    /**
     * enable katex for global
     */
    katex: boolean
  }

  vite?: ViteUserConfig
  vue?: Parameters<typeof Vue>[0]
  components?: Parameters<typeof Components>[0]
  unocss?: UnoCSSConfig
  /**
   * unocss presets
   */
  unocssPresets?: {
    uno?: Parameters<typeof presetUno>[0]
    attributify?: Parameters<typeof presetAttributify>[0]
    icons?: Parameters<typeof presetIcons>[0]
    typography?: Parameters<typeof presetTypography>[0]
  }
  pages?: Parameters<typeof Pages>[0]
  /**
   * for markdown
   */
  markdown?: MarkdownOptions
  extendMd?: (ctx: {
    route: {
      meta: { frontmatter: Record<string, any>, layout?: string } & object
      path: string
      component: string
    }
    data: Readonly<Record<string, any>>
    content: string
    excerpt?: string
    path: string
  }) => void
  addons?: ValaxyAddons
}
```

::: zh-CN
所以，你可以像这样使用：
:::

::: en
So you can use it like this:
:::

```ts
// valaxy.config.ts
import { defineValaxyConfig } from 'valaxy'
import type { ThemeConfig } from 'valaxy-theme-yun'
import { addonComponents } from 'valaxy-addon-components'
import Inspect from 'vite-plugin-inspect'

const safelist = [
  'i-ri-home-line',
]

export default defineValaxyConfig<ThemeConfig>({
  // site config see site.config.ts or write in siteConfig
  siteConfig: {},

  theme: 'yun',
  themeConfig: {
    banner: {
      enable: true,
      title: '云游君的小站',
    },
  },

  vite: {
    // https://github.com/antfu/vite-plugin-inspect
    // Visit http://localhost:3333/__inspect/ to see the inspector
    plugins: [Inspect()],
  },

  unocss: {
    safelist,
  },

  addons: [
    addonComponents()
  ],
})
```
