# VUE
## npm
### 配置npm
```bash
# 配置npm镜像源为阿里云镜像源
npm config set registry https://registry.npmmirror.com

# 查看镜像源
npm config get registry
```

### 命令
```bash
# 初始化项目，生成 package.json 文件
npm init

# 安装指定的包
npm install <package-name>

# 卸载指定的包
npm uninstall <package-name>

# 运行 package.json 中定义的脚本命令
npm run <script-name>
```

## Vite

### 创建项目
```bash
# 创建新的 Vite 项目（交互式选择框架）
npm create vite

# 一步创建 Vue 项目
npm create vite@latest my-vue-app -- --template vue

# 创建 Vue 项目（交互式选择框架）
npm create vue@latest
```

### 开发命令
| 命令 | 说明 |
|------|------|
| `npm run dev` | 启动开发服务器（支持热更新） |
| `npm run build` | 构建生产版本（输出到 `dist/` 目录） |
| `npm run preview` | 预览生产构建 |

## 指令

格式：`v-name:argument.modifiers="value"`

| 部分 | 含义 | 示例 |
|------|------|------|
| `v-name` | 指令名 | `v-bind` `v-on` `v-if` |
| `:argument` | 指令参数 | `:src` `:click` `:class` |
| `.modifiers` | 修饰符 | `.prevent` `.stop` `.lazy` |
| `"value"` | 绑定值 | `"url"` `"count"` `"msg"` |

示例：`v-on:click.prevent="handleSubmit"` → 监听 click 事件 + 阻止默认 + 执行 handleSubmit

| 写法 | 完整形式 | 说明 |
|------|---------|------|
| `:attr` | `v-bind:attr` | 动态绑定属性 |
| `@event` | `v-on:event` | 监听事件 |
| `#name` | `v-slot:name` | 具名插槽 |
| `v-text` | — | 设置文本内容（等同 `{{ }}`，会覆盖子元素） |
| `v-html` | — | 渲染 HTML（注意 XSS 风险，不要用于用户输入） |

### 事件修饰符

| 修饰符 | 说明 |
|--------|------|
| `.prevent` | 阻止默认行为（等同 `e.preventDefault()`） |
| `.stop` | 阻止冒泡（等同 `e.stopPropagation()`） |
| `.once` | 只触发一次 |
| `.capture` | 使用捕获模式 |
| `.self` | 仅 `event.target` 是自身时触发 |

### 条件渲染

| 指令 | 说明 |
|------|------|
| `v-if` | 条件为真时渲染，false 时销毁 DOM（有切换开销） |
| `v-show` | 条件为真时显示，false 时 `display:none`（有初始渲染开销） |
| `v-else-if` | 链式条件，紧跟 `v-if` |
| `v-else` | 默认分支，紧跟 `v-if` / `v-else-if` |

> 频繁切换用 `v-show`，条件很少变用 `v-if`。

### 列表渲染

```vue
<!-- 遍历数组 -->
<li v-for="item in list" :key="item.id">{{ item }}</li>

<!-- 带索引 -->
<li v-for="(item, index) in list" :key="item.id">{{ index }} - {{ item }}</li>

<!-- 遍历对象 -->
<li v-for="(value, key) in obj" :key="key">{{ key }}: {{ value }}</li>
```

> `:key` 必须唯一且稳定，避免用 `index` 作为 key。

### 样式绑定

```vue
<!-- :class -->
<div :class="{ active: isActive }">          <!-- 对象：条件控制 -->
<div :class="[classA, classB]">              <!-- 数组：组合多个类 -->

<!-- :style（CSS 属性用驼峰：font-size → fontSize）-->
<div :style="{ color: textColor }">
```

## 表单绑定 v-model

`v-model` = 双向绑定（自动监听输入 + 更新值）

```vue
<input v-model="text">                       <!-- string -->
<input type="checkbox" v-model="checked">    <!-- boolean -->
<input type="checkbox" value="A" v-model="arr">  <!-- string[] -->
<textarea v-model="desc"></textarea>         <!-- string -->
<select v-model="selected">                  <!-- string -->
<select v-model="arr" multiple>              <!-- string[] -->

<!-- 修饰符 -->
<input v-model.lazy="text">                  <!-- change 时更新 -->
<input v-model.number="age">                 <!-- 转为数字 -->
<input v-model.trim="name">                  <!-- 去首尾空格 -->
```

## 组件通信

### Props —— 父 → 子

```vue
<!-- 父组件 -->
<Child :title="msg" :count="5" />
```

```vue
<!-- 子组件 -->
<script setup>
defineProps({
  title: String,
  count: { type: Number, default: 0 }
})
</script>
```

> Props 单向数据流，子组件不能修改。

### Emit —— 子 → 父

子组件通过 `emit` 向上发送事件，父组件用 `@事件名` 监听并处理：

```vue
<!-- 子组件 Child.vue -->
<script setup>
const emit = defineEmits(['change'])

function handleClick() {
  emit('change', '新值')   // 事件名: 'change'，参数: '新值'
}
</script>

<template>
  <button @click="handleClick">通知父组件</button>
</template>
```

```vue
<!-- 父组件 -->
<script setup>
import Child from './Child.vue'

function handleChange(val) {
  console.log('子组件传来的值：', val)   // '新值'
}
</script>

<template>
  <Child @change="handleChange" />
  <!--       ↑ 事件名    ↑ 回调函数，参数就是 emit 的第二项 -->
</template>
```

> 事件名推荐用 camelCase（`myEvent`），模板中自动转换为 kebab-case（`@my-event`）。

### v-model —— 父子双向

`v-model` 是 `:modelValue` + `@update:modelValue` 的语法糖：

```vue
<!-- 父组件 -->
<Child v-model="count" />
<!-- 等价于 -->
<Child :modelValue="count" @update:modelValue="count = $event" />
```

```vue
<!-- 子组件 -->
<script setup>
const props = defineProps(['modelValue'])
const emit = defineEmits(['update:modelValue'])
function update(val) {
  emit('update:modelValue', val)
}
</script>
```

多个 v-model：

```vue
<!-- 父组件 -->
<Child v-model:title="t" v-model:visible="v" />
```

```vue
<!-- 子组件 -->
<script setup>
defineProps(['title', 'visible'])
defineEmits(['update:title', 'update:visible'])
</script>
```

### provide / inject —— 祖孙跨层级

```vue
<!-- 祖先组件 -->
<script setup>
import { provide } from 'vue'
provide('theme', 'dark')
</script>
```

```vue
<!-- 后代组件 -->
<script setup>
import { inject } from 'vue'
const theme = inject('theme')    // 'dark'
</script>
```

> 适合深层级传递，但层级简单时优先用 Props。

### 通信方式选择

| 场景 | 推荐方式 |
|------|---------|
| 父 → 子 | Props |
| 子 → 父 | Emit |
| 父子双向 | v-model |
| 祖孙跨层级 | provide / inject |
| 任意组件间共享 | Pinia（见独立笔记） |

## 插槽

组件的"占位符"——子组件预留 `<slot>` 位置，父组件填入内容。类似函数传参：Props 传数据，Slot 传模板。

> `#name` 是 `v-slot:name` 的简写，等同 `@` 之于 `v-on:`、`:` 之于 `v-bind:`。

### 默认插槽

```vue
<!-- 子组件 Card.vue -->
<template>
  <div class="card">
    <slot>默认内容：没有传入时显示</slot>
  </div>
</template>
```

```vue
<!-- 父组件 -->
<Card>
  <p>这是父组件传入的内容</p>
</Card>
```

### 具名插槽

子组件用 `name` 定义多个插槽：

```vue
<!-- 子组件 Layout.vue -->
<template>
  <header><slot name="header" /></header>
  <main><slot /></main>              <!-- 默认插槽 -->
  <footer><slot name="footer" /></footer>
</template>
```

父组件用 `#name` 填充：

```vue
<!-- 父组件 -->
<Layout>
  <template #header>
    <h1>标题</h1>
  </template>

  <p>主体内容</p>                     <!-- 填充默认插槽 -->

  <template #footer>
    <span>底部</span>
  </template>
</Layout>
```

### 作用域插槽

子组件通过 `slot` 向父组件暴露数据：

```vue
<!-- 子组件 List.vue -->
<script setup>
const items = ['苹果', '香蕉', '橘子']
</script>

<template>
  <ul>
    <li v-for="item in items" :key="item">
      <slot :item="item" :index="items.indexOf(item)" />
    </li>
  </ul>
</template>
```

父组件用 `#default="props"` 接收：

```vue
<!-- 父组件 -->
<List>
  <template #default="{ item, index }">
    <span>{{ index + 1 }}. {{ item }}</span>
  </template>
</List>
```

## 响应式

### ref —— 包装基本类型

```vue
<script setup>
import { ref } from 'vue'

const count = ref(0)       // 创建
count.value++              // JS 中通过 .value 读写
</script>

<template>
  {{ count }}              <!-- 模板中自动解包，无需 .value -->
</template>
```

### reactive —— 包装对象/数组

```vue
<script setup>
import { reactive } from 'vue'

const state = reactive({ name: 'Vue', version: 3 })
state.name = 'Vue 3'       // 直接访问属性，无需 .value
</script>

<template>
  {{ state.name }}
</template>
```

| API | 适用类型 | 访问方式 | 解构 |
|-----|---------|---------|------|
| `ref` | 基本类型 / 任意 | `.value` | 可以 |
| `reactive` | 对象 / 数组 | 直接访问 | 会丢失响应式 |

### computed —— 计算属性

```vue
<script setup>
import { ref, computed } from 'vue'

const firstName = ref('张')
const lastName = ref('三')

// 自动缓存，依赖不变则不重算
const fullName = computed(() => firstName.value + lastName.value)
</script>

<template>
  {{ fullName }}
</template>
```

### watch —— 侦听器

```vue
<script setup>
import { ref, watch } from 'vue'

const keyword = ref('')

watch(keyword, (newVal, oldVal) => {
  console.log('从', oldVal, '变为', newVal)
})

// 监听多个源
watch([a, b], ([newA, newB]) => { /* ... */ })
</script>
```

### watchEffect —— 自动追踪依赖

```vue
<script setup>
import { ref, watchEffect } from 'vue'

const keyword = ref('')

// 自动追踪回调内使用到的响应式数据，不需要手动指定监听目标
// 立即执行一次，之后依赖变化时自动重新执行
watchEffect(() => {
  console.log('搜索：', keyword.value)
})
</script>
```

| | `watch` | `watchEffect` |
|---|---------|---------------|
| 指定依赖 | 手动指定 | 自动追踪 |
| 首次执行 | 不执行（lazy） | 立即执行 |
| 获取旧值 | 能 | 不能 |

## 生命周期

### Options API 钩子

```
beforeCreate → created → beforeMount → mounted → beforeUpdate → updated → beforeUnmount → unmounted
```

| 钩子 | 说明 |
|------|------|
| `beforeCreate` | 实例初始化，data/methods 尚不可用 |
| `created` | data/methods 已可用，**适合发请求**，DOM 未挂载 |
| `beforeMount` | 编译完成，即将挂载到 DOM |
| `mounted` | DOM 挂载完毕，**适合操作 DOM / 初始化第三方库** |
| `beforeUpdate` | 数据变化，DOM 即将更新 |
| `updated` | 数据变化，DOM 已更新 |
| `beforeUnmount` | 组件即将卸载，**适合清理定时器 / 取消订阅** |
| `unmounted` | 组件已卸载，所有指令解绑 |

### Composition API（`<script setup>`）

```vue
<script setup>
import {
  onBeforeMount, onMounted,
  onBeforeUpdate, onUpdated,
  onBeforeUnmount, onUnmounted
} from 'vue'

// 等同 mounted —— 最常用
onMounted(() => {
  console.log('DOM 已挂载')
  // 初始化第三方库、发请求、操作 DOM
})

// 等同 created —— setup 本身就是 created
// 直接在 setup 顶层写同步代码即可，无需钩子

// 等同 beforeUnmount —— 清理副作用
onBeforeUnmount(() => {
  clearInterval(timer)
  window.removeEventListener('resize', handler)
})
</script>
```

### Options API → Composition API 映射

| Options API | Composition API |
|-------------|----------------|
| `beforeCreate` | 直接在 `setup` 顶层写 |
| `created` | 直接在 `setup` 顶层写 |
| `beforeMount` | `onBeforeMount()` |
| `mounted` | `onMounted()` |
| `beforeUpdate` | `onBeforeUpdate()` |
| `updated` | `onUpdated()` |
| `beforeUnmount` | `onBeforeUnmount()` |
| `unmounted` | `onUnmounted()` |

> Vue 3 没有 `onBeforeCreate` / `onCreated`，因为 `setup()` 本身就运行在这两个阶段之间。