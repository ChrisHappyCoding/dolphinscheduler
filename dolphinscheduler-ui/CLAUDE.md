[根目录](../CLAUDE.md) > **dolphinscheduler-ui**

---

# dolphinscheduler-ui 模块文档

> 最后更新：2026-01-26 13:07:02 CST

---

## 变更记录 (Changelog)

### 2026-01-26 13:07:02 CST
- 初始化模块文档
- 识别核心组件与路由
- 记录主要页面与状态管理

---

## 模块职责

`dolphinscheduler-ui` 是 **Web 前端界面模块**，基于 Vue 3 + TypeScript + Vite 构建，提供可视化的工作流管理界面。

### 核心功能

- **工作流设计器**：拖拽式 DAG 工作流编辑器（基于 AntV X6）
- **项目管理**：项目创建、配置、成员管理
- **工作流定义**：工作流创建、编辑、版本管理
- **任务实例**：工作流实例执行、监控、日志查看
- **数据源管理**：数据源配置、连接测试
- **资源中心**：资源文件上传、管理
- **监控告警**：系统监控、告警配置
- **用户权限**：用户、租户、告警组管理
- **系统设置**：队列、Worker 组、环境配置

---

## 入口与启动

### 入口文件

```
src/main.ts
```

### 启动方式

```bash
# 安装依赖
pnpm install

# 配置后端地址
# 编辑 .env.development，设置 VITE_APP_DEV_WEB_URL=http://localhost:12345

# 启动开发服务器
pnpm run dev

# 访问地址
http://localhost:5173
```

### 构建生产版本

```bash
# 配置后端地址
# 编辑 .env.production，设置 VITE_APP_PROD_WEB_URL

# 构建生产版本
pnpm run build:prod

# 输出目录
dist/
```

---

## 技术栈

### 核心框架

- **Vue**: 3.2.39 (Composition API)
- **TypeScript**: 4.8.3
- **Vite**: 3.1.2
- **Vue Router**: 4.1.5
- **Pinia**: 2.0.22 (状态管理)

### UI 组件库

- **Naive UI**: 2.33.5 (主 UI 库)
- **Naive UI Icons**: 图标库

### 工具库

- **AntV X6**: 1.34.1 (流程图编辑器)
- **Monaco Editor**: 0.50.0 (代码编辑器)
- **ECharts**: 5.3.3 (图表库)
- **D3.js**: 7.8.5 (数据可视化)
- **Axios**: 0.27.2 (HTTP 请求)

### 代码质量

- **ESLint**: 8.23.1 (代码检查)
- **Prettier**: 2.7.1 (代码格式化)
- **TypeScript**: 类型检查

---

## 目录结构

```
dolphinscheduler-ui/
├── src/
│   ├── main.ts                    # 入口文件
│   ├── App.vue                    # 根组件
│   ├── views/                     # 页面组件
│   │   ├── home/                  # 首页
│   │   ├── login/                 # 登录页
│   │   ├── projects/              # 项目管理
│   │   │   ├── list/              # 项目列表
│   │   │   └── detail/            # 项目详情
│   │   ├── datasource/            # 数据源管理
│   │   ├── resource/              # 资源中心
│   │   ├── security/              # 安全管理
│   │   ├── monitor/               # 监控中心
│   │   └── ui-setting/            # UI 设置
│   ├── components/                # 公共组件
│   │   ├── chart/                 # 图表组件
│   │   ├── crontab/               # Cron 表达式编辑器
│   │   ├── form/                  # 表单组件
│   │   ├── modal/                 # 模态框组件
│   │   ├── monaco-editor/         # Monaco 编辑器
│   │   └── log-modal/             # 日志模态框
│   ├── layouts/                   # 布局组件
│   │   └── content/               # 主布局
│   │       ├── navbar/            # 导航栏
│   │       └── sidebar/           # 侧边栏
│   ├── router/                    # 路由配置
│   │   ├── index.ts               # 路由主文件
│   │   ├── routes.ts              # 路由定义
│   │   └── modules/               # 路由模块
│   ├── store/                     # Pinia 状态管理
│   ├── locales/                   # 国际化
│   │   ├── en_US/                 # 英文
│   │   └── zh_CN/                 # 中文
│   ├── common/                    # 公共工具
│   ├── service/                   # API 服务
│   └── types/                     # TypeScript 类型定义
├── public/                        # 静态资源
├── .env.development               # 开发环境配置
├── .env.production                # 生产环境配置
├── package.json                   # 依赖配置
├── vite.config.ts                 # Vite 配置
├── tsconfig.json                  # TypeScript 配置
└── index.html                     # HTML 模板
```

---

## 核心路由

### 路由模块

```typescript
// router/modules/projects.ts
export default [
  {
    path: '/projects',
    name: 'projects',
    component: () => import('@/views/projects/list'),
    meta: {
      title: '项目管理'
    }
  },
  {
    path: '/projects/:projectCode/workflow/definitions',
    name: 'workflow-definition',
    component: () => import('@/views/projects/workflow/definition'),
    meta: {
      title: '工作流定义'
    }
  },
  // ...
]
```

### 主要路由

| 路径 | 组件 | 说明 |
|-----|------|------|
| `/login` | `views/login` | 登录页 |
| `/home` | `views/home` | 首页 |
| `/projects` | `views/projects/list` | 项目列表 |
| `/projects/:projectCode/workflow/definitions` | `views/projects/workflow/definition` | 工作流定义 |
| `/projects/:projectCode/workflow/instances` | `views/projects/workflow/instance` | 工作流实例 |
| `/datasource` | `views/datasource` | 数据源管理 |
| `/resource` | `views/resource` | 资源中心 |
| `/security` | `views/security` | 安全管理 |
| `/monitor` | `views/monitor` | 监控中心 |
| `/ui-setting` | `views/ui-setting` | UI 设置 |

---

## 状态管理

### Pinia Store

```typescript
// store/user.ts
export const useUserStore = defineStore('user', {
  state: () => ({
    userInfo: null as UserInfo | null,
    sessionId: ''
  }),
  actions: {
    setUserInfo(userInfo: UserInfo) {
      this.userInfo = userInfo
    },
    async logout() {
      // ...
    }
  }
})
```

### 主要 Store

| Store | 说明 |
|-------|------|
| `useUserStore` | 用户状态管理 |
| `useLocaleStore` | 国际化设置 |
| `useThemeStore` | 主题设置 |

---

## 核心 API 服务

### API 调用示例

```typescript
// service/projects/index.ts
import { axios } from '@/service/service'
import { hump } from '@/common/common'

export const getProjectList = (params: any) => {
  return axios({
    url: '/projects',
    method: 'get',
    params
  })
}

export const createProject = (data: any) => {
  return axios({
    url: '/projects',
    method: 'post',
    data: hump(data)
  })
}
```

### 主要 API 服务

| 服务模块 | 文件路径 | 功能 |
|---------|---------|------|
| 项目管理 | `service/projects/` | 项目 CRUD |
| 工作流 | `service/workflow/` | 工作流管理 |
| 数据源 | `service/datasource/` | 数据源管理 |
| 资源 | `service/resource/` | 资源文件管理 |
| 监控 | `service/monitor/` | 监控数据 |
| 用户 | `service/user/` | 用户管理 |

---

## 核心 UI 组件

### 1. 工作流编辑器 (DAG)

基于 AntV X6 的拖拽式工作流编辑器：

```vue
<template>
  <dag-graph
    :canvasData="canvasData"
    @save="handleSave"
    @nodeClick="handleNodeClick"
  />
</template>
```

**功能**：
- 拖拽创建任务节点
- 连线配置任务依赖
- 任务属性配置
- 工作流校验
- 保存与发布

### 2. Cron 表达式编辑器

```vue
<template>
  <crontab
    v-model="cronExpression"
    :locale="locale"
  />
</template>
```

**功能**：
- 可视化 Cron 配置
- 支持秒、分、时、日、月、周
- 实时预览
- 中文/英文支持

### 3. Monaco 代码编辑器

```vue
<template>
  <monaco-editor
    v-model="code"
    :language="language"
    :options="editorOptions"
  />
</template>
```

**功能**：
- 语法高亮
- 代码补全
- 错误提示
- 支持多种语言（SQL、Shell、Python 等）

### 4. 日志查看器

```vue
<template>
  <log-modal
    v-model:show="showLog"
    :taskInstanceId="taskInstanceId"
  />
</template>
```

**功能**：
- 实时日志查看
- 日志下载
- 日志搜索
- 自动刷新

---

## 国际化

### 语言配置

```typescript
// locales/en_US/index.ts
export default {
  common: {
    confirm: 'Confirm',
    cancel: 'Cancel',
    ...
  },
  project: {
    create_project: 'Create Project',
    project_name: 'Project Name',
    ...
  },
  ...
}
```

### 支持语言

- 中文简体 (zh_CN)
- 英文 (en_US)

### 切换语言

```typescript
import { useLocaleStore } from '@/store/locale'

const localeStore = useLocaleStore()
localeStore.changeLocale('en_US')
```

---

## 主题定制

### 主题配置

```typescript
// 主题切换
import { useThemeStore } from '@/store/theme'

const themeStore = useThemeStore()
themeStore.setTheme('dark') // or 'light'
```

### 主题样式

基于 CSS Variables 实现主题切换：

```css
:root {
  --primary-color: #2932e1;
  --text-color: #333333;
  --background-color: #ffffff;
}

.dark {
  --primary-color: #4c5bff;
  --text-color: #eeeeee;
  --background-color: #1f1f1f;
}
```

---

## 开发规范

### 代码风格

- **代码格式化**: Prettier
  ```bash
  pnpm run prettier
  ```

- **类型检查**: TypeScript
  ```bash
  vue-tsc --noEmit
  ```

- **代码检查**: ESLint
  ```bash
  pnpm run lint
  ```

### 命名规范

- **组件文件**: PascalCase (如 `WorkflowList.vue`)
- **工具文件**: camelCase (如 `formatDate.ts`)
- **类型文件**: PascalCase (如 `types.ts`)
- **常量文件**: UPPER_CASE (如 `CONSTANTS.ts`)

### 组件规范

```vue
<template>
  <div class="component-name">
    <!-- 模板内容 -->
  </div>
</template>

<script lang="ts" setup>
import { ref, computed } from 'vue'

// Props 定义
interface Props {
  modelValue: string
}
const props = defineProps<Props>()

// Emits 定义
const emit = defineEmits<{
  (e: 'update:modelValue', value: string): void
}>()

// 响应式数据
const data = ref('')

// 计算属性
const computedData = computed(() => {
  return data.value
})

// 方法
const handleClick = () => {
  // ...
}
</script>

<style lang="scss" scoped>
.component-name {
  // 样式
}
</style>
```

---

## 常见问题 (FAQ)

### Q1: 如何添加新的页面？

1. 在 `views/` 下创建页面组件
2. 在 `router/modules/` 下添加路由配置
3. 在 `locales/` 下添加国际化文本
4. 在侧边栏菜单中添加菜单项

### Q2: 如何调用后端 API？

```typescript
import { getProjectList } from '@/service/projects'

const loadProjects = async () => {
  const result = await getProjectList({
    pageSize: 10,
    pageNo: 1
  })
  // 处理结果
}
```

### Q3: 如何使用状态管理？

```typescript
import { useUserStore } from '@/store/user'

const userStore = useUserStore()
const userInfo = userStore.userInfo

// 更新状态
userStore.setUserInfo({
  id: 1,
  userName: 'admin'
})
```

### Q4: 如何进行表单验证？

使用 Naive UI 的表单验证：

```vue
<n-form
  ref="formRef"
  :model="formData"
  :rules="rules"
>
  <n-form-item label="项目名称" path="name">
    <n-input v-model:value="formData.name" />
  </n-form-item>
</n-form>
```

---

## 相关文件清单

### 主要源文件

```
dolphinscheduler-ui/src/
├── main.ts                           # 入口文件
├── App.vue                           # 根组件
├── views/                            # 页面组件
│   ├── home/                         # 首页
│   ├── login/                        # 登录页
│   ├── projects/                     # 项目管理
│   │   ├── list/                     # 项目列表
│   │   ├── workflow/                 # 工作流
│   │   └── task/                     # 任务
│   ├── datasource/                   # 数据源
│   ├── resource/                     # 资源中心
│   ├── security/                     # 安全管理
│   ├── monitor/                      # 监控中心
│   └── ui-setting/                   # UI 设置
├── components/                       # 公共组件
│   ├── chart/                        # 图表组件
│   ├── crontab/                      # Cron 编辑器
│   ├── form/                         # 表单组件
│   ├── modal/                        # 模态框
│   ├── monaco-editor/                # Monaco 编辑器
│   └── log-modal/                    # 日志查看器
├── layouts/                          # 布局组件
│   └── content/                      # 主布局
├── router/                           # 路由配置
│   ├── index.ts
│   ├── routes.ts
│   └── modules/
├── store/                            # 状态管理
├── locales/                          # 国际化
├── service/                          # API 服务
├── common/                           # 公共工具
└── types/                            # 类型定义
```

### 配置文件

```
dolphinscheduler-ui/
├── .env.development                  # 开发环境配置
├── .env.production                   # 生产环境配置
├── package.json                      # 依赖配置
├── vite.config.ts                    # Vite 配置
├── tsconfig.json                     # TypeScript 配置
├── .eslintrc.js                      # ESLint 配置
└── .prettierrc.js                    # Prettier 配置
```

---

**文档维护者**: AI 自动化系统
**最后更新**: 2026-01-26 13:07:02 CST
