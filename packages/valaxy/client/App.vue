<script setup lang="ts">
import { computed, onBeforeMount } from 'vue'
import { useHead, useSeoMeta } from '@unhead/vue'

// @ts-expect-error virtual module
import ValaxyUserApp from '/@valaxyjs/UserAppVue'

// @ts-expect-error virtual module
import ValaxyThemeApp from '/@valaxyjs/ThemeAppVue'
import pkg from 'valaxy/package.json'
import { useI18n } from 'vue-i18n'
import { definePerson, defineWebPage, defineWebSite, useSchemaOrg } from '@unhead/schema-org'
import dayjs from 'dayjs'
import ValaxyAddons from './components/ValaxyAddons.vue'
import { isDark, useFrontmatter } from './composables'

// https://github.com/vueuse/head
// you can use this to manipulate the document head in any components,
// they will be rendered correctly in the html results with vite-ssg
import { useSiteConfig } from './config'

// <link rel="apple-touch-icon" href="/pwa-192x192.png">
// <link rel="mask-icon" href="/safari-pinned-tab.svg" color="#00aba9">

const siteConfig = useSiteConfig()
// todo, allow user config
const themeColor = computed(() => isDark.value ? '#000' : '#ffffff')
const fm = useFrontmatter()

const { locale } = useI18n()

const title = computed(() => fm.value[`title_${locale.value}`] || fm.value.title)
useHead({
  title,
  titleTemplate: computed(() => fm.value.titleTemplate || ((title: string) => title ? `${title} - ${siteConfig.value.title}` : siteConfig.value.title)),
  link: [
    {
      rel: 'icon',
      href: siteConfig.value.favicon,
      type: siteConfig.value.favicon?.endsWith('svg') ? 'image/svg+xml' : 'image/png',
    },
  ],
  meta: [
    { name: 'description', content: computed(() => siteConfig.value.description) },
    {
      name: 'theme-color',
      content: themeColor,
    },
    {
      name: 'msapplication-TileColor',
      content: themeColor,
    },
    {
      name: 'generator',
      content: `Valaxy ${pkg.version}`,
    },
  ],

  templateParams: {
    schemaOrg: {
      host: siteConfig.value.url,
    },
  },
})

// seo
// todo: get first image url from markdown
const siteUrl = computed(() => fm.value.url || siteConfig.value.url)
const description = computed(() => fm.value.excerpt || fm.value.description || siteConfig.value.description)

useSeoMeta({
  description,
  ogDescription: description,
  ogLocale: computed(() => locale.value || fm.value.lang || siteConfig.value.lang || 'en'),
  ogLocaleAlternate: computed(() => siteConfig.value.languages.filter(l => l !== locale.value)),
  ogSiteName: computed(() => siteConfig.value.title),
  ogTitle: computed(() => fm.value.title || siteConfig.value.title),
  ogImage: computed(() => fm.value.ogImage || fm.value.cover || siteConfig.value.favicon),
  ogType: 'website',
  ogUrl: siteUrl,
})

// for SEO
useSchemaOrg([
  // https://unhead.unjs.io/guide/guides/identity.html
  // Personal Website or Blog
  definePerson({
    name: siteConfig.value.author.name,
    url: siteUrl.value,
    image: siteConfig.value.author.avatar,
    sameAs: siteConfig.value.social.map(s => s.link),
  }),
  defineWebSite({
    name: title.value,
    datePublished: computed(() => fm.value.date),
    dateModified: computed(() => fm.value.updated),
  }),
  defineWebPage(),
])

onBeforeMount(() => {
  if (siteConfig.value.timezone)
    dayjs.tz.setDefault(siteConfig.value.timezone)
})
</script>

<template>
  <ValaxyThemeApp />
  <ValaxyAddons />
  <ValaxyUserApp />
  <RouterView />
</template>
