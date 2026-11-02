# AGENTS.md

This file provides guidance to Codex (Codex.ai/code) when working with code in this repository.

以后所有回复都是用中文模式

## 项目概述

**phantom-ui-ops** 是一个基于 Vue.js 2.5.17 的零售运营企业级管理系统（无人售货机管理）。这是一个使用 Qiankun 进行模块化加载的单页应用微前端架构。

**技术栈：**
- Vue 2.5.17 + Vue Router 3.0.2 + Vuex 3.0.1
- Element UI 2.15.5（组件库）
- Webpack 4.16.5（构建工具）
- Qiankun 2.10.15（微前端）
- Axios 0.18.0（HTTP 客户端）
- Node.js 12.8.1（开发环境）

## 常用命令

### 开发
```bash
npm run dev              # 启动开发服务器，端口 7777
npm run dev:cn           # 自动切换到 Node 12.8.1 并启动开发服务器
npm run changeNodeVersion # 切换到 Node 12.8.1
```

### 构建
```bash
npm run build:prod       # 生产环境构建
npm run build:test       # 测试环境构建
npm run build:sit        # SIT 环境构建
npm run build:staging    # 预发布环境构建
```

### 代码质量
```bash
npm run lint             # 对 .js 和 .vue 文件运行 ESLint
npm run test             # lint 命令的别名
npm run precommit        # pre-commit hook，使用 lint-staged
```

### 工具
```bash
npm run svgo             # 优化 SVG 图标
```

## 架构概览

### 目录结构
```
src/
├── api/                  # API 服务层，按模块组织
├── assets/               # 静态资源（字体、图片）
├── components/           # 全局可复用组件
├── directive/            # 自定义 Vue 指令
├── filters/              # 全局 Vue 过滤器
├── mixin/                # 全局混入（common.js, scrollbarReset, goPrev, asyncTask）
├── router/               # 路由配置
├── store/                # Vuex store 模块
├── styles/               # 全局 SCSS 样式
├── utils/                # 工具函数
├── views/                # 页面级组件
├── App.vue               # 根组件
└── main.js               # 入口文件
```

### 核心架构模式

#### 1. **微前端架构**
- 基于 Qiankun 的微前端加载，位于 `src/views/layout/AppMain.vue`
- 主应用作为子应用的容器
- 基于路由的动态模块加载

#### 2. **状态管理 (Vuex)**
只有 `user` 和 `app` 模块使用全局 Vuex 状态，其他所有数据都由各个页面自己管理。

**Store 模块：**
- `app` - 应用全局状态
- `user` - 身份认证和用户信息
- `permission` - 基于角色的访问控制和路由生成
- `machineWarn` - 机器告警状态
- `common` - 共享应用状态
- `dataDictionary` - 数据字典管理
- `deptTree` - 部门树结构
- `checkPage` - 页面检查工具

#### 3. **路由管理**
- 基于用户权限的动态路由生成，通过 `store.dispatch('GenerateStaffRoutes')`
- 基于 `src/main.js:94-160` 中的路由守卫进行基于角色的访问控制
- 路由元信息用于访问控制和缓存
- 白名单路由：`/login`、`/manager`、`/qingce`、`/en/login`、`/phone`

#### 4. **全局混入**（在 main.js 中应用）
- `common.js` - 共享业务逻辑、OSS URL、订单反馈原因
- `scrollbarReset.js` - 滚动条行为管理
- `goPrev.js` - 导航历史管理
- `asyncTask.js` - 异步任务处理（已全局混入）

#### 5. **全局组件**
- `ai-icon-svg` - SVG 图标组件
- `switchGroup` - 自定义开关组
- `Pagination` - 分页组件

#### 6. **指令**
- `waves` - 水波纹效果
- `input` - Input 全局指令
- `horizontalScroll` - 水平滚动
- `clipboard` - 剪贴板功能
- `elSelectLoadmore` - Element UI 选择器加载更多
- `hasPermi` - 基于权限的显示控制

## 重要配置

### 本地开发代理配置

为避免 `config/index.js` 中的合并冲突，创建一个本地的 `config/dev.conf.js` 文件（git 忽略）用于代理配置。该文件包含各种 API 端点的代理配置：

```javascript
module.exports = {
  proxyTable: {
    '/jwt': { target: 'https://test.ainfinit.com/' },
    '/api': { target: 'https://test.ainfinit.com/' },
    '/oss': { target: 'https://ainfinit-basic.oss-cn-beijing.aliyuncs.com' },
    '/baiduMap': { target: 'https://api.map.baidu.com' },
    '/connection': { target: 'https://t2.ainfinit.com/', ws: true }, // WebSocket
    '/eventSource': { target: 'https://t2.ainfinit.com/' }, // SSE
    // ... 完整示例见 README.md
  }
}
```

### OSS 上传前缀

通过 `/api/tool/oss/upload` 上传文件时，需包含 `subPrefix` 参数：
- `goodsImg` - 商品图片
- `replFinishImg` - 补货完成图片
- `replSignImg` - 补货签名图片
- `feedbackImg` - 用户反馈图片
- `adResource` - 广告图片/视频
- `scanCodeImg` - 扫码贴
- `certImg` - 证件照片

## 开发模式

### Tab 状态持久化

带有标签页的页面可以在刷新后保持选中的标签：

```html
<!-- v-tab-change:activeName 传递 v-model 的 key -->
<!-- setTabName 在刷新后的初始化时调用 -->
<el-tabs v-model="activeName" v-tab-change:activeName="setTabName">
  <el-tab-pane label="标签1" name="tab1"></el-tab-pane>
</el-tabs>
```

```javascript
setTabName({action, key}, activeName) {
  // action: 'setActiveName'（写回）或 'setPath'（更新 URL）
  // key: v-model 的值
}
```

### 文件下载 / Excel 导出

API 定义需要 `responseType: 'blob'`：

```javascript
export function reportExport(data) {
  return fetch({
    url: '/api/convenience/report/export',
    responseType: 'blob',
    method: 'post',
    data
  })
}
```

使用方式：
```javascript
import { downFile } from '@/utils/common'

reportExport({page: 1, limit: 1}).then(data => {
  if (data) downFile(data) // 使用时间戳文件名下载
})
```

### 异步导出任务

异步导出混入已全局混入，直接使用 `this.submitAsyncTask()` 即可：

```javascript
this.submitAsyncTask({
  exportType: 1,  // 完整导出类型列表见 README.md
  groupName: 'operation',
  handlerName: 'deviceExportHandler',
  data: { /* 筛选参数 */ }
})
```

**重要**：对于 `exportType = 1`（订单导出），数组参数必须包装：

```javascript
import { ensureArray } from '@/utils/common'

const keys = ['deviceTypes', 'machineIds', 'operDeptIds']
keys.forEach(item => {
  if (data[item]) data[item] = ensureArray(data[item])
})
```

### 数据字典过滤器

使用 ID 从数据字典查找名称：

```javascript
{{ typeId | searchList(typeList, 'keyField', 'nameField', 'default') }}
{{ typeId | searchOptions }}  // 对比 value，返回 label
{{ typeCode | searchCode }}   // 对比 code，返回 name
```

### 空数据默认值

```javascript
{{ name | defaultText('默认值', '追加文本') }}
```

### 自定义属性配置（多租户）

1. 在数据字典"商户自定义显示字段（customProp）"下添加字段
2. 在"个性化配置页"中为每个商户配置
3. 在组件中，检查配置并条件渲染：

```javascript
created() {
  this.isRELCODE_PROP = this.$store.state.user.userData.relCode || false
}
```

```html
<el-table-column v-if="isRELCODE_PROP" label="自定义字段*" />
```

### 时间操作

始终传递时间戳（而非 Date 对象）给 moment 操作：

```javascript
// 正确
this.$moment(startTimestamp).subtract(1, 'day').startOf('day').valueOf()

// 错误 - Date 对象解析问题
this.$moment(new Date()).subtract(1, 'day')...
```

### SVG 图标

三种使用方式：

1. 通用组件：`<ai-icon-svg icon-class="file" />`
2. 字体文件（单色）：`<i class="icon icon-file"/>`
3. 字体文件（多色）：`<i class="icon-multi-color icon-file"/>`

在 `index.scss` 中远程引入 CSS：
```scss
@import(https://static.ainfinit.com/static/svg-font-file/pp/250611/icon-multi-color.css);
@import(https://static.ainfinit.com/static/svg-font-file/pp/250611/icon-single-color.css);
```

## 全局过滤器 (src/filters/)

| 过滤器 | 用途 |
|--------|------|
| `defaultText` | null/undefined 的默认值 |
| `divideNumber` | 除以 n |
| `diffTimeFormat` | 时间差转时长字符串 |
| `numberPrecision` | 精度处理 |
| `searchList` | 按 key 查找字典 |
| `searchOptions` | 按 value 查找字典 |
| `searchCode` | 按 code 查找字典 |

## 实时功能

- **WebSocket**：`/connection` 代理用于实时更新
- **EventSource**：`/eventSource` 代理用于服务器发送事件
- **全局轮询**：App.vue 包含通知的全局轮询

## 组件头部格式

```html
<!--
 * @description: 组件描述
 * @fileName: componentName.vue
 * @author: 作者名
 * @date: YYYY-MM-DD
!-->
```

## 保留的查询参数

- `query.action = 'feedback'` - 由 `src/mixin/common.js` 用于刷新后标签状态持久化

## 页面 UI 规范（强制）

新增或修改 `src/views/` 下的列表页、详情页、弹窗前，必须先阅读：

1. `docs/ui-spec.md`
2. `src/views/baseSetManager/componentSpec/index.vue`
3. 与当前需求同类型的既有业务页面（列表页优先参考订单或设备列表页）

`docs/ui-spec.md` 是面向 AI 的简明规则，`componentSpec/index.vue` 是可运行的完整示例。两者均为 UI 规范来源；修改规范页时，应同步更新简明规则。

### 列表页约束

- 根容器使用 `app-container main-body-container`。
- 查询表单使用 `main-form`；设备类筛选可使用 `main-form device-main-form`。
- 表格使用 `main-table`，并设置 `header-cell-class-name="main-table-header"`；宽表格使用 `v-horizontal-ai-scroll`。
- 查询和重置优先复用全局混入的 `handleFilter`、`handleReset`。
- 分页优先使用 `AiPagination`，绑定全局混入的 `listQuery.page`、`listQuery.limit`；禁止在页面重复声明分页字段。
- 订单/设备列表的导出按钮放在筛选表单最后的 `special-100` 表单项中并右对齐；不创建脱离筛选区的临时操作栏。
- 表格普通字段优先使用 `defaultText`；枚举、数据字典字段优先使用 `searchOptions`、`searchList`、`searchCode`。

### 组件库约束

优先使用已经全局注册的 `@ainfinit/ainfinit-ui`：`AiPagination`、`BaseDialog`、`AiSelectLoadmore`、`AiDeptCascader`、`AiDeptTreeSelect`、`AiSelectDevice`、`AiTooltip`、`AiTipText`、`AiJsonView`、`AiQrcodeVue`、`AiIconSvg`、`DragItem`、`AiSalesPlanItem`。

已有组件可以覆盖时，不得重新实现同类组件或另起页面视觉体系。

### JavaScript 兼容性

- 本项目不支持可选链操作符 `?.`，禁止在 `.js`、`.vue` 中使用。
- 需要判空时，使用项目现有的显式判断、短路判断或默认值写法，例如 `data && data.name`、`(data || {}).name`。

## 入口文件

- `src/main.js` - 应用入口、Vue 初始化、路由守卫、全局混入
- `src/App.vue` - 带有布局和全局轮询的根组件
- `src/router/index.js` - 路由配置
- `src/store/index.js` - Vuex store 注册
