# CLAUDE.md

此文件为 Claude Code (claude.ai/code) 在此仓库工作时提供指导。

以后所有回复都是用中文模式

## 项目概述

phantom-ui 是一个基于 Vue.js 的 B2B 智能柜/无人店管理平台，为平台方、运营商、商户提供设备管理、订单处理、库存管理和用户管理等功能。

## 技术栈

- **Vue 2.5.17** - 主前端框架
- **Element UI 2.15.5** - UI 组件库
- **Vuex 3.0.1** - 状态管理 (模块: app, user, permission, machine, common, dataDictionary)
- **Vue Router 3.0.2** - 前端路由 (history 模式)
- **Webpack 4.16.5** - 模块打包工具
- **Qiankun 2.10.15** - 微前端框架，子应用 'convenience' 挂载在 `/Layout/convenience`
- **Axios 0.18.0** - HTTP 客户端

## 开发命令

```bash
# 开发环境（需要 Node v16.x）
npm run dev              # 启动开发服务器，端口 7777
npm run dev:cn           # 自动切换 Node 版本并启动

# 构建
npm run build:prod       # 生产环境构建
npm run build:test       # 测试环境构建
npm run build:sit        # 预发布环境构建
npm run build:dev1       # 开发环境构建

# OSS 构建 (需要 PUBLIC_PATH 和 buildVersion 环境变量)
npm run build:ossProd    # 生产环境 + OSS
npm run build:ossTest    # 测试环境 + OSS

# 代码质量
npm run lint             # 运行 ESLint
npm run jest             # 运行单元测试

# Node 版本
npm run changeNodeVersion # 切换到 Node 16
```

## 重要配置

### 本地配置文件

创建 `config/dev.conf.js` (不被 git 跟踪) 以避免合并冲突。从 README.md 复制配置，包含以下 API 代理设置：
- `/jwt` - 认证接口
- `/api` - 主 API 接口
- `/connection` - WebSocket (实时更新)
- `/eventSource` - EventSource 连接
- `/oss` - 文件上传
- `/baiduMap` - 地图服务
- `/node` - Node 服务端

### Node 版本

项目需要 Node v16.x。使用 `nvm use` 读取 `.nvmrc` 切换版本，或使用 `npm run dev:cn` 自动切换并启动。

## 架构

### 目录结构

```
src/
├── api/                 # API 模块，按业务域组织 (aliDevice, orderManager, warehouseManager 等)
├── assets/              # 静态资源 (字体、图片)
├── components/          # 全局可复用组件
├── directive/           # 自定义 Vue 指令 (waves, inputLimit, clipboard, hidePhone 等)
├── filters/             # 全局 Vue 过滤器
├── mock/                # Mock API 响应，使用 Mock.js
├── mixin/               # 全局混入 (common, goPrev, scrollbarReset, asyncTask)
├── router/              # 路由配置，基于权限的动态路由
├── store/               # Vuex 模块
├── styles/              # 全局 SCSS 样式
├── utils/               # 工具函数 (auth, request, common)
├── views/               # 页面组件，按功能组织
├── App.vue              # 根组件
└── main.js              # 应用入口
```

### 状态管理

只有用户和应用配置相关的状态使用 Vuex 全局管理，其他数据由各业务页面自行管理。

Store 模块：
- `app` - 应用状态 (侧边栏、设备、语言)
- `user` - 用户认证、权限、自定义属性
- `permission` - 基于权限的路由和菜单
- `machine` - 设备相关状态
- `common` - 公共共享状态
- `dataDictionary` - 数据字典

### 路由

- 通过 `store.dispatch('GetStaffInfo')` 和 `GenerateStaffRoutes` 基于用户权限动态生成路由
- 路由使用 Layout 组件进行侧边栏导航
- 微前端子路由：`/Layout/convenience`
- History 模式，自动滚动重置

### 微前端 (Qiankun)

- 子应用 'convenience' 挂载在 `/Layout/convenience`
- 通过 `initGlobalState` 共享全局状态：商店名称和权限菜单角标
- 主应用和子应用切换时处理 CSS 沙箱隔离问题

## 核心业务模块

`src/api/` 中的 API 模块：
- `aliDevice` - 智能设备操作
- `orderManager` - 订单处理、客诉订单、异常订单
- `warehouseManager` - 补货审批、库存跟踪
- `user` - 用户和组织管理
- `common` - 通用操作
- `advertisement` - 内容管理

## 常用模式

### 文件上传

上传图片到 `/api/tool/oss/upload`，需带 `subPrefix` 参数：
- `goodsImg` - 商品图片
- `replFinishImg` - 补货完成图
- `replSignImg` - 补货签名图
- `feedbackImg` - 用户反馈图
- `adResource` - 广告图片/视频
- `scanCodeImg` - 扫码贴
- `certImg` - 证件照

### 商户个性化配置

1. 在数据字典中声明字段，分类为 【商户自定义显示字段】
2. 在个性化配置页添加/编辑配置项
3. 在组件中获取：`this.$store.state.user.userData.relCode || false`
4. 使用 `v-if` 控制显示/隐藏

### 异步导出

使用 `this.submitAsyncTask({ exportType, data })` - 已全局混入，无需手动引入。exportType 值参考 README.md (1=设备导出, 3=订单反馈等)。

订单导出 (exportType=1) 时，数组参数必须用 `ensureArray()` 包裹：
```js
import { ensureArray } from '@/utils/common'
data.deviceTypes = ensureArray(data.deviceTypes)
```

### Tab 状态保存

使用 `v-tab-change:activeName="methodName"` 指令在页面刷新后保持选中的 tab。方法接收 `{action, key}, activeName`。

### 文件下载

Blob 文件下载（如 Excel 导出）：
```js
// API: responseType: 'blob'
import { downFile } from '@/utils/common'
downFile(data) // 文件名默认为时间戳
```

### 全局混入

- `commonMixin` - 通用功能，已全局混入
- `goPrevMixin` - 返回功能
- `scrollbarResetMixin` - 滚动条重置
- `asyncTask` - 异步任务提交（已全局混入）

### 自定义指令

- `v-waves` - 水波纹效果
- `v-inputLimit` - 输入字符限制
- `v-clipboard` - 剪贴板操作
- `v-hidePhone` - 手机号脱敏
- `v-hasPermi` - 权限检查
- `v-tab-change` - Tab 状态保持

### 全局组件

- `ai-icon-svg` - SVG 图标组件
- `Pagination` - 分页组件
- `PhoneDisplay` - 手机号显示
- `Tooltip` - 自定义提示
- `switchGroup` - 开关组

### 过滤器

- `searchList` - 数据字典 id 查名称
- `defaultText` - null/undefined 默认值
- `divideNumber` - 除法运算
- `diffTimeFormat` - 时间差转时长
- `numberPrecision` - 精度处理

### Moment.js 注意事项

时间操作时确保传入时间戳而非时间对象：
```js
this.$moment(startTimestamp).subtract(1, 'day').startOf('day').valueOf()
```

### 实时功能

- WebSocket 连接 `/connection` 实现实时更新
- EventSource 消息通知
- 自动轮询获取角标数量

## 测试

Jest 单元测试位于 `test/unit/` 目录，文件名以 `.spec.js` 结尾：
```bash
npm run jest                                    # 运行所有测试
npm run jest test/unit/select.spec.js          # 运行指定文件
npm run jest test/unit/select.spec.js -- -t "用例名称"  # 运行指定测试用例
```

## 部署

生产环境使用 Nginx 部署，配置参考 README.md。使用 `publicPath` 环境变量时，静态资源可从 OSS 服务。
