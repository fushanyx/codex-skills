# 项目上下文分析指引

自动识别前端项目的技术栈、代码风格和约定，为提示词生成提供准确的上下文信息。

---

## 分析流程

### 1. 技术栈识别

通过以下文件自动识别项目使用的技术：

| 文件 | 分析内容 |
|------|---------|
| `package.json` | 框架（React/Vue/Angular/Svelte）、构建工具（Vite/Webpack）、UI库、状态管理、路由 |
| `tsconfig.json` | TypeScript 版本和配置、路径别名、严格模式 |
| `tailwind.config.*` | Tailwind CSS 配置、自定义主题、断点 |
| `.eslintrc*` / `biome.json` | 代码规范、规则集 |
| `prettier` / `.editorconfig` | 格式化约定 |
| `next.config.*` / `vite.config.*` | 框架特定配置 |

### 2. 代码风格分析

从已有代码中提取以下约定：

**命名风格**：
- 组件文件名：`PascalCase.tsx` vs `kebab-case.tsx`
- 变量/函数：`camelCase` vs `snake_case`
- 常量：`UPPER_SNAKE_CASE` vs `camelCase`
- CSS 类名：`kebab-case` vs `camelCase`（CSS Modules）

**组件约定**：
- 函数式 vs 类式
- Props 定义：`interface` vs `type`
- 导出方式：`export default` vs `export const`
- 文件组织：单文件组件 vs 文件夹组件（index.tsx + styles.ts）

**导入约定**：
- 绝对路径（`@/components/`）vs 相对路径（`../../components/`）
- 导入排序方式
- 是否使用 barrel files（index.ts 聚合导出）

**样式约定**：
- CSS-in-JS vs CSS Modules vs Tailwind vs 原生 CSS
- 样式文件位置和命名
- 是否使用 CSS 变量 / 设计 Token

### 3. 目录结构理解

识别项目的目录约定：

```
典型结构模式：
src/
├── components/     → 可复用组件
├── pages/ / views/ → 页面级组件
├── hooks/          → 自定义 Hooks
├── stores/         → 状态管理
├── utils/ / lib/   → 工具函数
├── services/ / api/→ API 请求层
├── types/          → TypeScript 类型定义
├── styles/         → 全局样式
└── assets/         → 静态资源
```

分析时需要确认：
- 实际使用了哪些目录（不同项目结构差异很大）
- 新文件应该放在哪个目录
- 文件命名规则

### 4. 状态管理识别

| 检测方式 | 判断结果 |
|---------|---------|
| `useStore` / `create()` from zustand | Zustand |
| `useSelector` / `useDispatch` / `createSlice` | Redux Toolkit |
| `defineStore` / Pinia 导入 | Pinia |
| `useRecoilState` / atom / selector | Recoil |
| `observable` / `makeAutoObservable` | MobX |
| `provide` / `inject` / `ref` / `reactive` | Vue 组合式 API |

### 5. 分析结果格式

将分析结果整理为以下结构，嵌入到优化后的提示词「技术约束」部分：

```
技术栈: [React 18 + TypeScript 5 + Vite]
UI 框架: [Ant Design 5.x]
样式方案: [Tailwind CSS 3 + CSS Modules]
状态管理: [Zustand (全局) + useState (局部)]
路由: [React Router v6]
请求层: [Axios + React Query]
代码风格: [函数式组件, interface 定义 Props, 默认导出, 绝对路径 @/]
命名约定: [组件 PascalCase, 文件 PascalCase.tsx, 工具函数 camelCase]
```

### 6. 快速分析脚本

如果项目很大，优先读取以下文件快速获取关键信息：

1. `package.json` 的 `dependencies` 和 `devDependencies`
2. `src/` 下第一层目录结构
3. 需求涉及目录下的 2-3 个同类文件（分析风格）
4. 如果有 `index.ts` barrel file，快速浏览导出内容

避免全量扫描大项目，按需深入即可。
