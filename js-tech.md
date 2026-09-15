# traditional UI component library

- `Prime UI`
- `VarletUI`
- `Vuetify`
- `Chakra UI`
- `Material UI`
- `Mantine`

# Tailwind-base component library

- `Daisy UI`
- `tailwind UI`
- `Sailboat UI`
- `Shadcn UI`
- `Tailblocks`

# Shadcn-base component library

- `tweakcn`：比 shadcn create 更灵活的调整工具

- `Inspira UI`
- `Vue Bits`
- `React Bits`
- `Aceternity UI`
- `Magic UI`

# 轻通知组件

- `Vue Toastification`：Vue 专属，更新少，但文档比较清楚
- `React Hot Toast`：React 的轻通知组件
- `sonner`：轻通知组件，默认给 React 用，Vue 可以使用 `Vue Sonner`

# Vue

- `Vue Router`：官方路由库

- `Pinia`：官方状态管理库

- `VueUse`

# React

- `React Router`：路由库
- `Tanstack Router React`：类型安全更强的路由库

- `Redux`：状态管理库，老项目多，使用复杂
- `zustand`：状态管理库，需要手动编写 selector
- `jotai`：状态管理库，使用简单，灵活度高
- `React Use`

# 模拟数据

- `Mock.js`
- `Faker.js`

# BaaS

- `Supabase`：开源项目，提供一站式后端即服务
- `Neon`：纯数据库，拥有完全的灵活性
- `Firebase`：与 Google 生态高度融合，文档丰富，使用 NoSQL
- `Appwrite`：社区和生态相对小

# 环境变量

- `dotenv`
- `dotenvx`：相比 `dotenv` 更好用

# 表单校验

- `React Hook Form`：React 框架常用的表单校验库
- `VeeValidate`：Vue 框架常用的表单校验库

# 类型校验

- `yup`：一个常用的类型校验库
- `Zod` ：TypeScript 优先的验证库
- `TypeBox`
- `validator.js`

# 远端状态管理库

- `TanStack Query`：一套服务器状态管理库，用于高效处理前端应用中的异步数据获取、缓存与同步问题

# React Native

- `react-native safe-area-context`：获取手机屏幕的安全区域 `<SafaAreaView>`
- `react-native-svg`：在开发中使用 svg 图片
- `Nativewind`：在 React Native 中使用 TailWind CSS

# ORM

- `Prisma`
- `Drizzle`：还没发布正式版
- `Sequelize`：有点坑，慎用，适合初学 ORM 概念，v6 版本 TS 支持不太好

# Backend Framework

- `Express`
- `koa`
- `Hono`
- `Nest.js`

# Icon

- `iconify`
  - `@iconify/tailwind`
  - `@egoist/tailwindcss-icons`
- `Phosphor`

# Logging

- `pino`：日志处理
- `logdy`：日志分析工具

# 模块依赖分析

- `madge`：可视化模块依赖

# 加密

- `bcrypt`：有实现好的函数，可以直接调用
- `crypto`：node 的内置模块，提供各种加密方法，需要自行实现加密过程

# JWT

- `jsonwebtoken`：缺少持续维护，但新版本也没有已知漏洞，仅支持同步写法
- `jose`：有持续维护，支持异步写法

# 接口性能测试

- `autocannon`：纯 JS 实现

# Node.js cluster

- `pm2`：可以方便的将 nodejs 运行在多核心环境

# Node.js 包管理器

- `nvm`
  - `nvm-desktop`
- `volta`
- `fnm`

# Node.js 动态加载 Module

- `jiti`

# 事件发布订阅

- `mitt`

# MIME

- `mime`
- `mime-types`
- `mrmime`

# Build Tools

- `Turborepo`
- `Nx`
- `Rush`
- `Lerna`

# 日期处理

- `Day.js`

- `rrule.js`

- `date-fns`

- `Luxon`

- `Moment.js`：已停止新功能开发，进入维护状态

# 项目结构

- `Vertical Slice Architecture`
- `Feature Slice Architecture`
- `Feature-First Vertical Slice/Modular Vertical Slice`

# unplugin

**自动导入 / 自动注册**

- `unplugin-auto-import`: 自动导入常用 API，比如 Vue、Vue Router、Pinia、React Hooks 等，省掉手写 import。
- `unplugin-vue-components`: 自动发现并注册 Vue 组件，模板里直接用组件名即可。
- `unplugin-icons`: 把图标当组件用，按需加载图标集里的图标。
- `unplugin-vue2-script-setup`: 给 Vue 2 补上类似 Vue 3 的 script setup 体验。
- `unplugin-vue-markdown`: 把 Markdown 文件编译成 Vue 组件，方便直接在页面里用。

**编译转换 / 语法增强**

- `unplugin-vue`: 处理 Vue 3 单文件组件的编译转换，兼容更多构建工具。
- `unplugin-macros`: 提供宏能力，让一些需要编译期展开的写法更方便。
- `unplugin-vue-cssvars`: 让 Vue 的 CSS 变量能力更好地在样式里使用。
- `unplugin-preprocessor-directives`: 处理类似条件编译指令的预处理逻辑。
- `unplugin-replace`: 在构建时批量替换代码中的目标字符串。

**性能 / 体积 / 产物处理**

- `unplugin-imagemin`: 构建时压缩图片，减小资源体积。
- `unplugin-isolated-decl`: 更快地产生独立的类型声明文件。
- `unplugin-unused`: 检查未使用的依赖或导入，帮助清理项目。
- `unplugin-ast`: 直接基于 AST 做代码转换，适合更复杂的源码改写。
- `unplugin-turbo-console`: 改善 console 调试体验，偏开发辅助。

# 跨端框架


 | 工具             | 主要定位       | 技术栈             | 覆盖平台                      |
  | ---------------- | -------------- | ------------------ | ----------------------------- |
  | **Electron**     | 桌面应用       | Chromium + Node.js | Windows / macOS / Linux       |
  | **Tauri**        | 轻量桌面应用   | Web 前端 + Rust    | Windows / macOS / Linux       |
  | **Wails**        | 桌面应用（Go） | Web 前端 + Go      | Windows / macOS / Linux       |
  | **React Native** | 原生移动应用   | React / JS / TS    | iOS / Android                 |
  | **Flutter**      | 原生移动/桌面  | Dart + Skia        | iOS / Android / Desktop / Web |
  | **Kuikly**       | 高性能移动端   | JS / 自研渲染      | iOS / Android                 |
  | **UniApp**       | 多端应用       | Vue / JS           | 小程序 / H5 / App             |

# 并发控制

| 场景                        | 推荐库             |
| --------------------------- | ------------------ |
| 限制同时运行的 Promise 数量 | p-limit            |
| 批量任务队列                | p-queue            |
| Map + 并发控制              | p-map              |
| 请求池                      | promise-pool       |
| 流式任务处理                | Bottleneck         |
| 分布式限流                  | Bottleneck + Redis |

# Lodash Like

- `remeda`
- `es-toolkit`

# Animation

- `GSAP`
- `AOS`
- `Motion`

# Smooth Scroll

- `Lenis`
- `GSAP ScrollSmoother`

# 富文本编辑器

- `BlockNote`