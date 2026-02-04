在实际的项目开发中，我们很少直接在组件里写 `axios.get(...)`。为了保证代码的可维护性和安全性，通常会采取**“二次封装 + 模块化管理”**的方案。

以下是一个标准的、可直接用于生产环境的 Axios 使用流程：

---

### 1. 项目目录结构

建议将网络请求相关的代码集中存放，结构如下：

Plaintext

```
src/
└── api/
    ├── request.ts    // 1. Axios 实例封装（拦截器）
    ├── user.ts       // 2. 用户相关接口
    └── goods.ts      // 3. 商品相关接口
```

---

### 2. 第一步：创建 Axios 实例并配置拦截器

这是整个项目的“交通枢纽”，负责统一处理 **Token 注入**、**状态码拦截**和**错误提示**。

**`src/api/request.ts`**

TypeScript

```
import axios from 'axios'

// 1. 创建实例
const service = axios.create({
  baseURL: import.meta.env.VITE_API_URL, // 从环境变量读取后端地址
  timeout: 5000 // 5秒超时
})

// 2. 请求拦截器 (Request Interceptor)
service.interceptors.request.use(
  (config) => {
    // 自动从 localStorage 获取 token 并塞入 Header
    const token = localStorage.getItem('token')
    if (token) {
      config.headers['Authorization'] = `Bearer ${token}`
    }
    return config
  },
  (error) => Promise.reject(error)
)

// 3. 响应拦截器 (Response Interceptor)
service.interceptors.response.use(
  (response) => {
    // 简化返回：直接返回后端的 data 部分，过滤掉 axios 包装的冗余信息
    const res = response.data
    if (res.code !== 200) {
      // 统一处理业务错误（如：密码错误）
      console.error(res.message || 'Error')
      return Promise.reject(new Error(res.message || 'Error'))
    }
    return res
  },
  (error) => {
    // 处理 HTTP 错误码（如：401 Token 过期）
    if (error.response?.status === 401) {
      // 强制跳转登录页逻辑...
    }
    return Promise.reject(error)
  }
)

export default service
```

---

### 3. 第二步：模块化定义 API

不要把所有请求都塞在一个文件里，按业务拆分更清晰。

**`src/api/user.ts`**

TypeScript

```
import request from './request'
import type { UserProfile } from '@/types/user' // 之前你定义的类型

// 获取用户信息
export function getUserInfo(userId: string) {
  return request<UserProfile>({ // 使用泛型约定返回数据的形状
    url: `/user/${userId}`,
    method: 'get'
  })
}

// 登录请求
export function login(data: object) {
  return request({
    url: '/login',
    method: 'post',
    data
  })
}
```

---

### 4. 第三步：在 Vue 组件中调用

在组件中使用时，逻辑会非常干净，且拥有完整的 **TypeScript 类型补全**。

代码段

```
<script setup lang="ts">
import { getUserInfo } from '@/api/user'
import { onMounted, ref } from 'vue'

const userInfo = ref()

onMounted(async () => {
  try {
    // 这里的 res 已经被拦截器处理过，且具有 UserProfile 类型
    const res = await getUserInfo('123')
    userInfo.value = res.data
  } catch (err) {
    // 错误处理逻辑
  }
})
</script>
```

---

### 5. 为什么要这么折腾？

- **统一配置**：以后想改 API 地址或超时时间，只需改一个地方。
    
- **安全防护**：所有的请求都会自动带上 Token，不需要在每个组件里手动写。
    
- **体验一致**：全项目统一报错（比如弹出一个 Message 提示框），而不是每个页面各写各的。
    
- **TS 类型安全**：通过泛型 `request<UserProfile>`，你在写 `res.data.` 的时候，编辑器会自动提示 `name`, `avatar` 等属性。
    

---

### 总结

这就是 Axios 的“工业级”用法：**统一实例 -> 拦截器控全局 -> 模块化导接口 -> 组件内直接调**。

**既然聊到了封装，你想了解一下如何给后端返回的通用格式（比如都有 `code`, `message`, `data`）定义一个全局的 TS 接口吗？这能让你的 API 调用变得极其丝滑。**