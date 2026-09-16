# 1. `app/` 本身就是路由系统

传统 React：

```text
src/
├── pages/
├── components/
├── hooks/
└── App.tsx
```

你可以随便组织，然后使用 React Router：

```tsx
<Route path="/users/:id" element={<UserPage />} />
```

Next.js：

```text
app/
├── page.tsx
├── users/
│   ├── page.tsx
│   └── [id]/
│       └── page.tsx
```

直接由目录结构产生：

```text
app/page.tsx
        ↓
       /

app/users/page.tsx
        ↓
      /users

app/users/[id]/page.tsx
        ↓
      /users/:id
```

因此：

```text
app/
```

不是普通的业务目录，而是 **routing convention**。

------

# 2. `page.tsx`：这个文件代表一个可访问页面

这是最重要的约定之一。

```text
app/
└── dashboard/
    └── page.tsx
```

意味着：

```text
/dashboard
```

存在。

而：

```text
app/dashboard/components/Button.tsx
```

不会自动产生：

```text
/dashboard/components/Button
```

所以：

> **只有 `page.tsx` 才会把一个 segment 暴露成页面。**

这和传统 React Router 的思维明显不同。

------

# 3. `layout.tsx`：布局不是普通组件，而是路由层级的一部分

例如：

```text
app/
├── layout.tsx
├── page.tsx
└── dashboard/
    ├── layout.tsx
    └── page.tsx
```

渲染 `/dashboard` 时大致形成：

```text
RootLayout
└── DashboardLayout
    └── DashboardPage
```

也就是：

```tsx
<RootLayout>
  <DashboardLayout>
    <DashboardPage />
  </DashboardLayout>
</RootLayout>
```

你不需要自己：

```tsx
<DashboardLayout>
  <Outlet />
</DashboardLayout>
```

来管理。

而且 Layout 在导航过程中具有 **持久化（preserve）** 特性。

这也是 Next.js 与普通 React Router 应用很重要的区别。

------

# 4. `loading.tsx`：自动成为 Suspense Loading UI

例如：

```text
app/
└── dashboard/
    ├── page.tsx
    └── loading.tsx
```

Next.js 会自动把它用于这个 route segment 的 loading UI。

概念上类似：

```tsx
<Suspense fallback={<Loading />}>
  <DashboardPage />
</Suspense>
```

你不需要手动：

```tsx
<Suspense fallback={...}>
```

这就是一个典型的：

> **文件名 → 框架行为**

------

# 5. `error.tsx`：自动成为该路由 segment 的错误边界

```text
app/
└── dashboard/
    ├── page.tsx
    └── error.tsx
```

相当于 Next.js 帮你建立了一个 error boundary。

因此它不是普通：

```tsx
<ErrorMessage />
```

组件。

它参与的是 Next.js 的 **route-level error handling**。

通常还需要：

```tsx
"use client";
```

因为 Error Boundary 本身依赖 Client Component 能力。

------

# 6. `not-found.tsx`：404 UI 的特殊约定

```text
app/
├── not-found.tsx
└── users/
    └── not-found.tsx
```

可以分别控制不同层级的 not-found UI。

并且：

```tsx
notFound();
```

可以直接触发对应的 `not-found.tsx`。

例如：

```tsx
const user = await getUser(id);

if (!user) {
  notFound();
}
```

这里的 `notFound()` 并不是你自己实现的路由跳转。

它是在告诉 Next.js：

> 当前 RSC 渲染应该进入 not-found 状态。

------

# 7. `route.ts`：目录可以直接定义 HTTP API

这是从传统 React 转过来特别容易忽略的一点。

```text
app/
└── api/
    └── users/
        └── route.ts
```

直接对应：

```text
/api/users
```

然后：

```ts
export async function GET() {
  ...
}

export async function POST() {
  ...
}
```

Next.js 就把它作为 HTTP endpoint。

所以：

```text
app/api/users/route.ts
```

不是普通 React 文件，而是：

> **服务器端 HTTP Route Handler**

这意味着 Next.js 的 `app/` 同时承担：

```text
页面路由
+
服务器组件
+
HTTP API
```

------

# 8. `[id]`：动态路由参数

```text
app/
└── users/
    └── [id]/
        └── page.tsx
```

对应：

```text
/users/123
/users/456
/users/abc
```

然后：

```tsx
export default async function Page({
  params,
}) {
  const { id } = await params;
}
```

这是文件系统路由。

------

# 9. `[...slug]` 和 `[[...slug]]`：Catch-all Route

```text
app/docs/[...slug]/page.tsx
```

匹配：

```text
/docs/a
/docs/a/b
/docs/a/b/c
```

而：

```text
app/docs/[[...slug]]/page.tsx
```

还可以匹配：

```text
/docs
/docs/a
/docs/a/b
```

普通 React 不会因为你建立了这个目录就产生任何路由行为。

------

# 10. `(group)`：Route Group，不进入 URL

例如：

```text
app/
├── (marketing)/
│   ├── about/
│   │   └── page.tsx
│   └── pricing/
│       └── page.tsx
│
└── (app)/
    └── dashboard/
        └── page.tsx
```

实际 URL：

```text
/about
/pricing
/dashboard
```

而不是：

```text
/marketing/about
/app/dashboard
```

它主要用于：

- 组织路由
- 区分布局
- 划分应用区域

例如：

```text
app/
├── (auth)/
│   ├── login/
│   └── register/
│
└── (main)/
    ├── dashboard/
    └── settings/
```

这在大型 Next.js 项目中非常常见。

------

# 11. `@slot`：Parallel Routes

例如：

```text
app/
└── dashboard/
    ├── page.tsx
    ├── @analytics/
    │   └── page.tsx
    └── @activity/
        └── page.tsx
```

然后 Layout：

```tsx
export default function Layout({
  children,
  analytics,
  activity,
}) {
  return (
    <>
      {children}
      {analytics}
      {activity}
    </>
  );
}
```

这里：

```text
@analytics
@activity
```

不是普通目录。

它们代表 Next.js 的 **Parallel Route slot**。

------

# 12. `(.)`、`(..)`：Intercepting Routes

例如：

```text
app/
├── feed/
│   └── page.tsx
│
└── photo/
    └── [id]/
        └── page.tsx
```

配合：

```text
feed/
└── (..)photo/
    └── [id]/
        └── page.tsx
```

可以实现一种非常典型的 Next.js UX：

```text
/feed
   ↓ 点击照片
/feed
┌───────────────────┐
│                   │
│     Photo Modal   │
│                   │
└───────────────────┘
```

但直接访问：

```text
/photo/123
```

又是完整 Photo 页面。

这是 Next.js 特有的路由能力之一。

------

# 13. `default.tsx`：Parallel Route 的默认内容

在 Parallel Routes 中：

```text
@modal/
└── default.tsx
```

可以定义这个 slot 没有匹配内容时的默认 UI。

所以 `default.tsx` 也是有框架语义的特殊文件。

------

# 14. `middleware.ts` / `proxy.ts`：请求进入应用时执行的特殊代码

Next.js 的请求处理链中有专门的入口文件约定。

较新的 Next.js 文档已经将原来的 Middleware 命名调整为 **Proxy**，因此你会看到：

```text
proxy.ts
```

这类文件不是普通工具模块。

它可以参与：

```text
Request
   ↓
Proxy
   ↓
Next.js routing
   ↓
RSC / Route Handler / Page
```

典型用途包括：

- 请求重写
- 重定向
- 请求级访问控制
- 根据 Cookie 判断请求
- 多租户路由处理

------

# 15. `generateMetadata()`：页面 Metadata 也可以通过导出约定产生

例如：

```tsx
export async function generateMetadata() {
  return {
    title: "Dashboard",
    description: "My dashboard",
  };
}
```

Next.js 会自动把它转换成页面 metadata。

你不需要自己：

```tsx
useEffect(() => {
  document.title = ...
}, []);
```

甚至可以：

```tsx
export async function generateMetadata({ params }) {
  const user = await getUser(params.id);

  return {
    title: user.name,
  };
}
```

这是 Next.js **服务器渲染 + SEO metadata** 体系的一部分。

------

# 16. `generateStaticParams()`：动态路由的静态生成参数

例如：

```text
app/
└── products/
    └── [id]/
        └── page.tsx
```

可以：

```tsx
export async function generateStaticParams() {
  return [
    { id: "1" },
    { id: "2" },
    { id: "3" },
  ];
}
```

Next.js 可以据此进行静态生成。

这不是 React 本身的概念。

------

# 17. `"use client"`：文件顶部直接改变组件执行模型

这是从 React 转 Next.js **最重要的心智变化之一**。

普通 React：

```tsx
function UserList() {
  ...
}
```

基本默认：

```text
Browser
```

而 Next.js App Router：

```tsx
function UserList() {
  ...
}
```

默认是：

```text
Server Component
```

如果写：

```tsx
"use client";

function UserList() {
  ...
}
```

则变成：

```text
Client Component
```

也就是说：

> **一个字符串 directive 改变了整个组件的执行环境。**

这不是传统 React SPA 的思维方式。

------

# 18. Server Component 默认可以直接访问服务器资源

例如：

```tsx
export default async function Page() {
  const users = await db.user.findMany();

  return <UserList users={users} />;
}
```

在 Next.js 中这是合法且推荐的架构。

你甚至不一定需要：

```text
React
 ↓
fetch("/api/users")
 ↓
API Route
 ↓
Database
```

可以：

```text
Server Component
       ↓
      ORM
       ↓
   PostgreSQL
```

这就是 Next.js 全栈模型与传统 React SPA 最大的结构性区别之一。

------

# 19. `"use server"`：Server Action / Server Function

例如：

```ts
"use server";

export async function createUser(formData: FormData) {
  ...
}
```

然后：

```tsx
<form action={createUser}>
```

Next.js/React 会把这个函数作为服务器端调用入口处理。

因此：

```text
"use server"
```

同样不是普通代码注释，而是框架/编译器语义。

------

# 20. `actions.ts` 并不是特殊文件，但 `"use server"` 是特殊语义

这个区别非常值得记住。

例如：

```text
app/
└── actions.ts
```

`actions.ts` **本身没有特殊含义**。

你完全可以叫：

```text
user-actions.ts
mutation.ts
server-functions.ts
```

真正特殊的是：

```ts
"use server";
```

所以 Next.js 的约定并不全部是“特殊文件名”。

还有：

```text
特殊目录
+
特殊文件名
+
特殊 export
+
特殊 directive
```

共同构成框架约定。

------

# 21. `app/layout.tsx` 的根布局具有特殊意义

例如：

```text
app/layout.tsx
```

它是整个 App Router 的 Root Layout。

而且必须满足一些特殊要求，例如：

```tsx
<html>
  <body>
    {children}
  </body>
</html>
```

它并不是：

```text
components/Layout.tsx
```

这种普通组件。

------

# 22. `public/`：静态资源目录

```text
public/
├── logo.png
├── favicon.ico
└── images/
```

然后：

```text
/public/logo.png
```

通过：

```text
/logo.png
```

访问。

传统 React 项目也经常有 `public`，但 Next.js 对它有明确的框架约定。

------

# 23. `favicon.ico`、`icon.tsx`、`opengraph-image` 等文件也有特殊语义

例如：

```text
app/
├── icon.png
├── apple-icon.png
├── opengraph-image.png
└── twitter-image.png
```

Next.js 可以自动处理对应 metadata。

还可以：

```text
app/
└── opengraph-image.tsx
```

动态生成 OG Image。

这类文件甚至没有：

```tsx
export default function Page()
```

但 Next.js 会因为**文件名和位置**知道它是什么。

------

# 24. `robots.txt`、`sitemap.xml` 也可以通过约定生成

例如：

```text
app/
├── robots.ts
└── sitemap.ts
```

可以直接生成：

```text
/robots.txt
/sitemap.xml
```

所以 App Router 的文件系统实际上已经开始承担：

```text
页面
API
Metadata
SEO
资源
错误处理
Loading
```

等多个职责。

------

# 25. `instrumentation.ts`：应用初始化 / instrumentation

例如：

```text
instrumentation.ts
```

可以用于应用启动阶段的 instrumentation，例如：

- OpenTelemetry
- tracing
- observability 初始化

它同样属于 Next.js 的特殊入口。

------

# 26. `app` 中的目录不一定都是 URL segment

这是非常重要的理解。

例如：

```text
app/
├── dashboard/
├── (auth)/
├── @modal/
└── _components/
```

它们分别可能意味着：

```text
dashboard/
    → URL segment

(auth)/
    → route group

@modal/
    → parallel route

_components/
    → 普通目录，不产生 route
```

因此不能简单理解成：

> `app` 下一个文件夹 = 一个 URL。

正确的模型是：

> **Next.js 会解析目录名的语法，然后决定这个目录在路由树中的语义。**

------

# 27. 还有一个很关键的区别：文件位置决定 Server/Client 边界

例如：

```text
app/
└── dashboard/
    └── page.tsx
```

默认：

```text
Server Component
```

然后：

```tsx
import InteractiveChart from "./InteractiveChart";
```

如果：

```tsx
"use client";

export default function InteractiveChart() {
  ...
}
```

就形成：

```text
Server Component
       │
       ↓
Client Component
```

而 Client Component 的依赖树会受到影响。

所以 Next.js 项目里：

```text
文件放在哪里
+
"use client"
+
"use server"
```

共同决定：

```text
代码在哪里执行
```

这与纯 React SPA 的架构完全不同。

------

# 28. 可以把 Next.js 的特殊约定归纳成一张表

| 约定                     | 作用                            |
| ------------------------ | ------------------------------- |
| `app/`                   | App Router                      |
| `page.tsx`               | 页面                            |
| `layout.tsx`             | 持久化布局                      |
| `loading.tsx`            | Loading UI                      |
| `error.tsx`              | Error Boundary                  |
| `not-found.tsx`          | Not Found UI                    |
| `default.tsx`            | Parallel Route 默认 UI          |
| `route.ts`               | HTTP Route Handler              |
| `[id]`                   | Dynamic Segment                 |
| `[...slug]`              | Catch-all Segment               |
| `[[...slug]]`            | Optional Catch-all              |
| `(group)`                | Route Group                     |
| `@slot`                  | Parallel Route                  |
| `(.)` / `(..)`           | Intercepting Route              |
| `public/`                | 静态资源                        |
| `proxy.ts`               | 请求级 Proxy                    |
| `generateMetadata()`     | 动态 Metadata                   |
| `generateStaticParams()` | 静态生成动态参数                |
| `icon.*`                 | App Icon                        |
| `opengraph-image.*`      | OG Image                        |
| `robots.ts`              | robots.txt                      |
| `sitemap.ts`             | sitemap.xml                     |
| `instrumentation.ts`     | instrumentation                 |
| `"use client"`           | Client Component 边界           |
| `"use server"`           | Server Function / Server Action |

