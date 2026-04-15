# Axios

基于 Promise 的 HTTP 客户端，浏览器和 Node.js 通用。

## 安装

```bash
npm install axios
```

## 基本用法

```js
import axios from 'axios'

// GET
axios.get('/api/users').then(res => console.log(res.data))

// POST
axios.post('/api/users', { name: 'Tom', age: 20 })
```

## 请求方法

| 方法 | 说明 |
|------|------|
| `axios.get(url, config)` | 查询 |
| `axios.post(url, data, config)` | 创建 |
| `axios.put(url, data, config)` | 全量更新 |
| `axios.patch(url, data, config)` | 部分更新 |
| `axios.delete(url, config)` | 删除 |

### GET 传参

```js
// 方式一：params（自动拼到 URL 后面）
axios.get('/api/users', { params: { page: 1, size: 10 } })
// → /api/users?page=1&size=10

// 方式二：手动拼
axios.get('/api/users?page=1&size=10')
```

### POST 传参

```js
// JSON 格式（默认）
axios.post('/api/users', { name: 'Tom' })

// FormData（文件上传）
const fd = new FormData()
fd.append('file', fileInput.files[0])
axios.post('/api/upload', fd)
```

## 响应结构

```js
axios.get('/api/users').then(res => {
  res.data   // 响应体（最常用）
  res.status // 状态码 200
  res.headers // 响应头
})
```

## 错误处理

```js
axios.get('/api/users')
  .then(res => console.log(res.data))
  .catch(err => {
    if (err.response) {
      // 服务器返回了错误状态码（4xx、5xx）
      console.log(err.response.status)  // 404
      console.log(err.response.data)    // 错误信息
    } else if (err.request) {
      // 请求已发出，无响应（网络断开、服务挂了）
      console.log('网络错误')
    } else {
      // 请求配置出错
      console.log(err.message)
    }
  })
```

## 创建实例

项目推荐创建独立实例，避免污染全局默认配置：

```js
// utils/request.js
import axios from 'axios'

const request = axios.create({
  baseURL: '/api',     // 通用前缀
  timeout: 10000,      // 超时时间 ms
  headers: { 'X-Token': 'xxx' },
})

export default request
```

### 常用配置项

| 配置 | 说明 | 示例 |
|------|------|------|
| `baseURL` | 请求前缀 | `'/api'` |
| `timeout` | 超时时间（ms） | `10000` |
| `headers` | 自定义请求头 | `{ 'Content-Type': 'application/json' }` |
| `params` | URL 查询参数 | `{ page: 1 }` |
| `paramsSerializer` | 参数序列化 | `indexes: null` 处理数组参数 |

### 实例方法 vs 全局方法

```js
// 全局：axios.get()  → 影响默认配置，适合简单项目
// 实例：request.get() → 独立配置，项目推荐
```

## 拦截器

### 请求拦截器

在请求发出前执行，常用于添加 token：

```js
request.interceptors.request.use(
  config => {
    const token = localStorage.getItem('token')
    if (token) config.headers.Authorization = `Bearer ${token}`
    return config
  },
  err => Promise.reject(err)
)
```

### 响应拦截器

在收到响应后执行，常用于统一错误处理：

```js
request.interceptors.response.use(
  res => {
    // 统一解包：只返回业务数据
    const { code, data } = res.data
    if (code === 200) return data
    return Promise.reject(new Error(res.data.message))
  },
  err => {
    // HTTP 错误处理
    if (err.response?.status === 401) {
      // token 过期，跳转登录（需 import router）
      router.push('/login')
    }
    return Promise.reject(err)
  }
)
```

## 取消请求

```js
const controller = new AbortController()

axios.get('/api/users', { signal: controller.signal })

// 取消
controller.abort()
```

> 常见场景：离开页面时取消未完成的请求，避免更新已卸载的组件。

## 开发代理配置

开发环境跨域通过 Vite 代理解决，生产环境由 Nginx 处理：

```js
// vite.config.js
export default {
  server: {
    proxy: {
      '/api': {
        target: 'http://localhost:3000',  // 后端地址
        changeOrigin: true,                // 改变请求头中的 Origin
        rewrite: path => path.replace(/^\/api/, ''), // 去掉 /api 前缀
      },
    },
  },
}
```

请求 `/api/users` → 实际转发到 `http://localhost:3000/users`

> `changeOrigin` 避免后端 CORS 拦截，`rewrite` 按需配置，后端本身有 `/api` 前缀就不用 rewrite。

## 并发请求

```js
const [users, posts] = await Promise.all([
  axios.get('/api/users'),
  axios.get('/api/posts'),
])
```

> 旧版 `axios.all()` 已废弃，直接用 `Promise.all()`。
