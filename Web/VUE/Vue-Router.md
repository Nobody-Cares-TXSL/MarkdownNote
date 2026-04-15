# Vue Router

## 快速上手

### 安装

```bash
npm install vue-router
```

### 四步配置

#### 1. 定义路由组件

```js
// views/Home.vue、views/About.vue 等
```

#### 2. 创建路由配置

```js
// router/index.js
import { createRouter, createWebHistory } from 'vue-router'
import Home from '../views/Home.vue'
import About from '../views/About.vue'

const routes = [
  { path: '/', component: Home },
  { path: '/about', component: About },
]

const router = createRouter({
  history: createWebHistory(),
  routes,
})

export default router
```

路由模式：

| 模式 | 说明 |
|------|------|
| `createWebHistory()` | HTML5 模式，URL 无 `#`，需服务器配置 |
| `createWebHashHistory()` | Hash 模式，URL 带 `#`，无需服务器配置 |

#### 3. 挂载路由

```js
// main.js
import router from './router'

createApp(App).use(router).mount('#app')
```

#### 4. 添加路由出口

```vue
<!-- App.vue -->
<router-view />
```

## useRoute() vs useRouter()

```js
import { useRoute, useRouter } from 'vue-router'

const route = useRouter()  // ❌ 容易写混

const route = useRoute()   // ✅ 只读，获取当前路由信息（path、params、query）
const router = useRouter() // ✅ 执行操作（push、replace、go）
```

| 区别 | `useRoute()` | `useRouter()` |
|------|-------------|---------------|
| 作用 | 读取当前路由信息 | 执行导航操作 |
| 常用 | `route.params` `route.query` | `router.push()` `router.replace()` |
| 类比 | 相当于 `$route` | 相当于 `$router` |

## 导航

| 方式 | 声明式 | 编程式 |
|------|--------|--------|
| 导航 | `<router-link to="/about">` | `router.push('/about')` |
| 替换 | `<router-link replace>` | `router.replace('/about')` |
| 前进/后退 | — | `router.go(-1)` / `router.back()` |

### router-link 声明式导航

> `:to` 绑定对象时写法与 `router.push()` 完全一致

```vue
<!-- 字符串路径 -->
<router-link to="/user/42">用户详情</router-link>

<!-- 对象形式 -->
<router-link :to="{ path: '/user/42' }">用户详情</router-link>

<!-- 命名路由 + params -->
<router-link :to="{ name: 'user', params: { id: 42 }">用户详情</router-link>

<!-- 带查询参数 -->
<router-link :to="{ path: '/user', query: { page: 1 } }">用户列表</router-link>

<!-- replace 模式（不留历史记录） -->
<router-link :to="/login" replace>登录</router-link>
```

### 编程式导航

#### push — 跳转（加入历史记录）

```js
router.push('/user/42')
router.push({ path: '/user/42' })
router.push({ name: 'user', params: { id: 42 } })  // path 和 params 不能同时用
router.push({ path: '/user', query: { page: 1, sort: 'asc' } })
// → /user?page=1&sort=asc
```

#### replace — 替换（不留历史记录）

```js
router.replace('/login')
// 用法同 push，但用户无法后退到上一页
```

#### go / back / forward — 前进后退

```js
router.back()    // router.go(-1)
router.forward() // router.go(1)
router.go(-3)    // 后退 3 步
```

#### 导航失败处理

```js
router.push('/nonexistent').catch(err => {
  if (err.name === 'NavigationDuplicated') return // 忽略重复导航
  console.error('导航失败', err)
})
```

## 获取当前路由信息

| 属性 | 示例 | 说明 |
|------|------|------|
| `route.path` | `'/user/42'` | 当前路径（不含 query） |
| `route.params` | `{ id: '42' }` | 路径参数（`/user/:id`） |
| `route.query` | `{ page: '1' }` | 查询参数（`?page=1`） |
| `route.name` | `'user'` | 当前路由名称 |
| `route.hash` | `'#section'` | 当前哈希值 |
| `route.fullPath` | `'/user/42?page=1'` | 完整路径（含 query 和 hash） |

```vue
<script setup>
import { useRoute } from 'vue-router'
const route = useRoute()
const userId = route.params.id
const page = route.query.page
</script>

<template>
  <p>用户ID：{{ route.params.id }}</p>
  <p>当前页：{{ route.query.page }}</p>
</template>
```

> template 中也可直接用 `{{ $route.params.id }}`，效果一样。

## 命名路由

给路由配置 `name`，跳转时通过名称而非路径：

```js
const routes = [
  { path: '/user/:id', name: 'user', component: User },
]
```

```js
router.push({ name: 'user', params: { id: 42 } })
```

优势：路径变了只需改路由配置，跳转代码不用动。

## 动态路由 & 传参

### 动态路由匹配

路径中的 `:xxx` 是动态占位符，访问时该位置可以是任意值：

```js
{ path: '/user/:id', component: User }
// /user/42  → route.params.id === '42'
// /user/abc → route.params.id === 'abc'
```

### 传参三种方式

#### 1. params 动态路由

路由配置中声明 `:param`，参数在 URL 路径上，**刷新不丢失**。

```js
// 传参
router.push({ name: 'user', params: { id: 42 } })
// 或 router.push('/user/42')

// 取值
route.params.id  // '42'    URL: /user/42
```

#### 2. query 查询参数

参数在 URL `?` 后面，**刷新不丢失**。

```js
// 传参
router.push({ path: '/user', query: { id: 42, type: 'admin' } })
// 或 router.push('/user?id=42&type=admin')

// 取值
route.query.id    // '42'    URL: /user?id=42&type=admin
```

#### 3. state 路由状态

参数不在 URL 上，**刷新丢失**（基于 `history.state`）。

```js
// 传参
router.push({ path: '/user', state: { from: 'home', id: 42 } })

// 取值
history.state.from  // 'home'  URL: /user（干净无参数）
```

#### 对比

| 方式 | 参数位置 | 刷新是否丢失 | 适用场景 |
|------|---------|-------------|---------|
| `params` | URL 路径 `/user/:id` | 不丢失 | 核心标识（用户ID、文章ID） |
| `query` | URL 查询 `?id=42` | 不丢失 | 筛选条件（分页、搜索、排序） |
| `state` | 隐藏（`history.state`） | 丢失 | 临时数据（来源页、临时标记） |

### 参数语法变体

> 以下后缀仅影响**匹配规则**，取值方式不变，都是 `route.params.名字`

| 写法 | 含义 | 路径配置示例 | 匹配结果 |
|------|------|------------|---------|
| `:名字` | 必须有，占一段 | `'/user/:id'` | `/user/42` ✓ `/user` ✗ |
| `:名字+` | 必须有，可占多段 | `'/file/:path+'` | `/file/a/b/c` ✓ `/file` ✗ |
| `:名字*` | 可选，可占多段 | `'/file/:path*'` | `/file` ✓ `/file/a` ✓ |
| `:名字(正则)` | 限定匹配格式 | `'/user/:id(\\d+)'` | `/user/42` ✓ `/user/abc` ✗ |

`+` 和 `*` 匹配多段时，值是数组：

```js
// 路由: { path: '/file/:path+', component: File }
// 访问: /file/docs/readme.md
route.params.path  // ['docs', 'readme.md']  ← 数组
```

### props 将路由参数注入组件

开启 `props: true` 后，`params` 直接作为组件 props，无需 `useRoute()`：

```js
// 路由配置
{ path: '/user/:id', component: User, props: true }
```

```vue
<script setup>
defineProps(['id'])
// 直接用 id，不需要 import useRoute
</script>
```

也支持函数形式传自定义 props：

```js
{
  path: '/user/:id',
  component: User,
  props: route => ({ id: route.params.id, isAdmin: route.query.admin === '1' })
}
```

## 路由配置进阶

### 嵌套路由

```js
{
  path: '/user/:id',
  component: User,
  children: [
    { path: 'profile', component: Profile },
    { path: 'posts', component: Posts },
  ],
}
```

父组件需添加 `<router-view />` 作为子路由出口。

### 404 兜底路由

```js
const routes = [
  // ...其他路由
  { path: '/:pathMatch(.*)*', name: 'NotFound', component: NotFound },
]
```

> 必须放在 `routes` 数组**最后**，匹配所有未定义的路径。

### 重定向与别名

```js
{ path: '/', redirect: '/home' }    // 重定向
{ path: '/home', alias: '/' }       // 别名（URL 不变）
```

| 对比 | redirect | alias |
|------|---------|-------|
| URL 变化 | 会变成目标路径 | 不变 |
| 执行 | 发起新导航 | 直接渲染目标组件 |

### 路由懒加载

```js
{ path: '/about', component: () => import('../views/About.vue') }
```

按需加载，减小首屏体积。

## 导航守卫

```js
// 全局前置守卫
router.beforeEach((to, from, next) => {
  // to: 目标路由
  // from: 来源路由
  // next(): 放行 / next(false): 取消 / next('/login'): 重定向
})
```

| 守卫 | 位置 |
|------|------|
| `beforeEach` | 全局前置 |
| `afterEach` | 全局后置 |
| `beforeEnter` | 路由独享 |
| `beforeRouteEnter` | 组件内（无法访问 `this`） |
| `beforeRouteUpdate` | 组件内（路由复用时触发） |
| `beforeRouteLeave` | 组件内（离开守卫） |

### 登录拦截示例

```js
router.beforeEach((to, from) => {
  if (to.meta.requiresAuth && !isLoggedIn) {
    return '/login'  // 返回路径等同于 next('/login')
  }
})
```
