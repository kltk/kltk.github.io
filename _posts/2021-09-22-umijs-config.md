---
layout: post
title: umijs config
tags:
    - nodejs
    - umijs
---

最近用 umijs 创建一个玩具项目，在这里记一下配置过程。

### 约定式路由

umijs 默认生成的是配置式路由，每次添加新的页面都要在配置里添加。改为约定式路由则，只要按约定创建目录结构就会自动生成路由，不用再手动添加。

```diff
// https://umijs.org/zh-CN/docs/convention-routing
// .umirc.ts
export default defineConfig({
-  routes: []
})
```

### 按需加载

按计划每个页面的内容都是不相关的，打开页面时只需要加载当前页面的内容就可以，所以添加按需加载的配置。

开启按需加载的时候会自动处理路由，所以不用在每个页面上单独再处理。

```diff
// https://umijs.org/zh-CN/docs/load-on-demand
// .umirc.ts
export default defineConfig({
+  dynamicImport: {},
})
```

开启 mfsu 加快编译速度，会自动打开按需加载，上面的配置就不用加了。

预编译文件丢到 cache 目录。

```diff
// .umirc.ts
export default defineConfig({
-  dynamicImport: {},
+  mfsu: {
+    development: {
+      output: "./node_module/.cache/.mfsu-dev"
+    },
+  },
})
```

### 菜单

添加一个全局菜单方便浏览不同页面，可以使用 [@umijs/plugin-layout](https://umijs.org/zh-CN/plugins/plugin-layout) 插件从路由生成菜单。

启用 @umijs/plugin-layout：

```diff
// .umirc.ts
export default defineconfig({
+  layout: {
+    name: 'Playground',
+    local: true,
+    layout: 'side',
+  },
})
```

启用 @umijs/plugin-layout 后页面上已经渲染了 layout，但是菜单还是空的，因为生成的路由没有 [name](https://umijs.org/zh-CN/plugins/plugin-layout#name-1) [属性](https://umijs.org/zh-CN/docs/convention-routing#%E6%89%A9%E5%B1%95%E8%B7%AF%E7%94%B1%E5%B1%9E%E6%80%A7)。

> React 组件在 js 中本质是一个函数，直接写入 name 属性会报错，先用 defineProperty 处理一下。

> 约定式路由是在编译时生成的，理论上运行时不用真的写入 name 属性也可以

```typescript
// page.tsx
function Page() {
    ...
}

Object.defineProperty(Page, 'name', { writable: true });
Page.name = 'Page Menu Item Name';

export default Page;
```

### 子菜单

菜单层级是和[路由](https://umijs.org/zh-CN/docs/convention-routing#%E5%B5%8C%E5%A5%97%E8%B7%AF%E7%94%B1)相关的，添加 _layout.tsx 来生成子菜单。

```typescript
// _layout.tsx
import React from 'react';

interface LayoutProps {
  children: React.ReactChildren;
}

function Layout(props: LayoutProps) {
  const { children } = props;
  return children;
}

Object.defineProperty(Layout, 'name', { writable: true });
Layout.name = 'SubMenu';

export default Layout;
```
