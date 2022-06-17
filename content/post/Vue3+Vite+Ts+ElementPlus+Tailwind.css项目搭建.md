---
title: "Vue3+Vite+Ts+ElementPlus+Tailwind.css项目搭建"
date: 2022-06-17T14:30:52+08:00
draft: false
---

## 创建项目

```code
npm init vite my-project
cd my-project
```

使用`yarn`安装依赖

```code
yarn
```

## 初始化Tailwind CSS

**安装 Tailwind 以及其它依赖项:**

```code
yarn add -D tailwindcss@latest postcss@latest autoprefixer@latest
```

**创建配置文件**
接下来，生成您的 tailwind.config.js 和 postcss.config.js 文件：

```code
npx tailwindcss init -p
```

**在CSS中引入Tailwind**

```code
/* ./src/index.css */
@tailwind base;
@tailwind components;
@tailwind utilities;
```

具体可以参考Tailwind官方文档：[在 Vue 3 和 Vite 安装 Tailwind CSS](https://www.tailwindcss.cn/docs/guides/vue-3-vite)

## 集成项目配置

1. 引入NodeJs的type类型

```code
yarn add -D @types/node
```

2. 修改tsconfig.json

```json
{
  "compilerOptions": {
    "typeRoots": [
      "node_modules/@types", // 默认值
      "src/types"
   ],
    "target": "esnext",
    "useDefineForClassFields": true,
    "module": "esnext",
    "moduleResolution": "node",
    "strict": true,
    "jsx": "preserve",
    "sourceMap": true,
    "resolveJsonModule": true,
    "esModuleInterop": true,
    "lib": ["esnext", "dom"],
    "baseUrl": "./",
    "paths":{
      "@": ["src"],
      "@/*": ["src/*"],
    }
  },
  "include": ["src/**/*.ts", "src/**/*.d.ts", "src/**/*.tsx", "src/**/*.vue"]
}
```

3. 修改`vite.config.ts`

```ts
import { defineConfig } from 'vite';
import vue from '@vitejs/plugin-vue';
import * as path from 'path';

// https://vitejs.dev/config/
export default defineConfig({
    resolve: {
        //设置别名
        alias: {
            '@': path.resolve(__dirname, 'src')
        }
    },
    plugins: [vue()],
    server: {
        port: 8080, //启动端口
        hmr: {
            host: '127.0.0.1',
            port: 8080
        },
        // 设置 https 代理
        proxy: {
            '/api': {
                target: 'your https address',
                changeOrigin: true,
                rewrite: (path: string) => path.replace(/^\/api/, '')
            }
        }
    }
});
```

## 代码质量风格统一

### eslint

1. 安装依赖

```code
yarn add eslint eslint-plugin-vue --D
```

由于 ESLint 默认使用 `Espree` 进行语法解析，无法识别 `TypeScript` 的一些语法，故我们需要安装 `@typescript-eslint/parser` 替代掉默认的解析器

```shell
yarn add @typescript-eslint/parser -D
```

安装对应的插件 @typescript-eslint/eslint-plugin 它作为 eslint 默认规则的补充，提供了一些额外的适用于 ts 语法的规则。

```shell
pnpm install @typescript-eslint/eslint-plugin --save-dev

```

2. 创建配置文件

```json
{
  "env": {
    "browser": true,
    "es2020": true,
    "jest": true,
    "node": true
  },
  "settings": {
  },
  "extends": [
    "plugin:vue/vue3-recommended",
    "plugin:@typescript-eslint/recommended",
    "plugin:tailwindcss/recommended",
    "prettier",
    "plugin:prettier/recommended"
  ],
  "parser": "vue-eslint-parser",

  "parserOptions": {
      "parser": "@typescript-eslint/parser",
      "ecmaVersion": 2020,
      "sourceType": "module",
      "ecmaFeatures": {
          "jsx": true
      }
  },
  "plugins": ["@typescript-eslint", "tailwindcss"],
  "rules": {
    "@typescript-eslint/explicit-module-boundary-types": "off",
    "@typescript-eslint/no-non-null-assertion": "off",
    "tailwindcss/classnames-order": "warn",
    "tailwindcss/no-custom-classname": "warn",
    "tailwindcss/no-contradicting-classname": "error",
  }
}

```

3. 创建忽略文件

```code
node_modules/
dist/
index.html
```

4. 命令行式运行，修改`package.json`

```
{
    ...
    "scripts": {
        ...
        "eslint:comment": "使用 ESLint 检查并自动修复 src 目录下所有扩展名为 .js 和 .vue 的文件",
        "eslint": "eslint --ext .js,.jsx,.ts,.tsx,.vue --ignore-path .gitignore --fix src",
    }
    ...
}

```

**注意：** `ext`文件后缀一定要包含对应的文件类型

### prettier

1. 安装依赖

```shell
yarn add prettier eslint-config-prettier eslint-plugin-prettier -D
```

2. 创建配置文件 `prettierrc`

```json
{
    "trailingComma": "none",
    "semi": false,
    "singleQuote": true
}  
```

3. 命令行式运行

```json
{
    ...
    "scripts": {
        ...
        "prettier:comment": "自动格式化当前目录下的所有文件",
        "prettier": "prettier --write"
    }
    ...
}

```

## 集成`pinia、vue-router@4`

参考：[手把手教你用 vite+vue3+ts+pinia+vueuse 打造大厂企业级前端项目](https://juejin.cn/post/7079785777692934174#heading-10)

## 引入element-plus

为了**更好的类型提示**以及`Tree Shaking`，我采用手动导入包的方式。
需要安装[unplugin-element-plus](https://github.com/element-plus/unplugin-element-plus) 导入样式

```vue
<!-- App.vue -->
<template>
  <el-button>I am ElButton</el-button>
</template>
<script>
  import { ElButton } from 'element-plus'
  export default {
    components: { ElButton },
  }
</script>
```

```ts
// vite.config.ts
import { defineConfig } from 'vite'
import ElementPlus from 'unplugin-element-plus/vite'

export default defineConfig({
  // ...
  plugins: [ElementPlus()],
})
```

`element-plus`具体可以参考[element-plus官网](https://element-plus.org/zh-CN/)

## commit-lint

### 安装

```shell
yarn add @commitlint/config-conventional @commitlint/cli -D
```

### 生成配置文件

```shell
echo "module.exports = { extends: ['@commitlint/config-conventional'] };" > commitlint.config.js
```

将会自动创建一个`commitlint.config.js`文件

## 安装Husky 和 lint-staged

```shell
npx mrm@2 lint-staged
```

此命令会安装`husky` 和 `lint-staged`,并添加配置到`package.json`
可以参考[Pre-commit Hook](https://www.prettier.cn/docs/precommit.html)

**注意：**执行该命令需要在git项目下，否则会报错

### 添加commit-lint Hook

```shell
cat <<EEE > .husky/commit-msg
#!/bin/sh
. "\$(dirname "\$0")/_/husky.sh"

npx --no -- commitlint --edit "\${1}"
EEE
```

### 给 hook 文件执行权限

```shell
chmod a+x .husky/commit-msg
```

至此，整个项目的搭建就结束了

>参考文章

- [在 Vue 3 和 Vite 安装 Tailwind CSS](https://www.tailwindcss.cn/docs/guides/vue-3-vite)
- [手把手教你用 vite+vue3+ts+pinia+vueuse 打造大厂企业级前端项目](https://juejin.cn/post/7079785777692934174)
- [prettier中文网](prettier.cn/docs/precommit.html)
- [commitlint官方文档](https://commitlint.js.org/#/guides-local-setup?id=install-commitlint)
