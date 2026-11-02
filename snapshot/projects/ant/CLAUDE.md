# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

**以后所有回复都用中文模式**

## 项目概述

"phantom-h5" —— "简单购"智能售货机管理平台的 Vue.js 2.x 移动端 H5 单页应用。面向微信浏览器环境，支持设备管理、库存运营、财务、IoT 监控等功能。

## 开发命令

```bash
npm install                  # 安装依赖
npm run dev:cn               # 自动切换 Node 16.13.2 并启动开发服务器（推荐）
npm run dev                  # 启动开发服务器（需先切到 Node 16.x）
npm run lint                 # ESLint 检查（Standard.js 规则）
npm run unit                 # Jest 单元测试（含覆盖率）
npm run build:test           # 构建测试环境（publicPath: at）
npm run build:staging        # 构建预发布环境（publicPath: as）
npm run build:prod           # 构建生产环境（publicPath: ap）
```

项目支持 **Node.js 16.x**（`>=16.0.0 <17.0.0`），开发基准版本为 16.13.2。`npm run dev:cn` 通过 `nvm use 16.13.2` 自动切换。

## 本地开发配置

创建 `config/dev.conf.js`（已 gitignore）配置代理，避免 `config/index.js` 合并冲突：

```javascript
module.exports = {
  proxyTable: {
    '/api': {
      target: 'https://test.ainfinit.com/',
      changeOrigin: true, secure: true, ws: true,
      pathRewrite: { '^/api': '/api' }
    }
  }
}
```

## 架构

### 目录结构

```
src/
├── api/          # 按业务域组织的 API 模块（device/, commodity/, finance/, iot/ 等）
├── views/        # 页面组件，按业务域分目录（login/, device/, order/, iot/ 等）
├── views/common/ # 共享 UI 组件（dialog, picker, video, tree select 等）
├── store/        # Vuex store（7 个模块）
├── router/       # 路由配置（仅基础路由，动态路由由后端菜单生成）
├── utils/        # 工具函数（fetch.js, common.js, auth.js, encrypt.js, bus.js）
├── mixin/        # 共享 mixin（conf.js 全局配置, page.js 分页, scanQR.js 扫码）
├── filters/      # 全局 Vue 过滤器（filterPrice, parseTime, notNull 等）
├── directive/    # 自定义指令（longPress 长按）
├── styles/       # 全局 SCSS 样式
├── assets/       # SVG 图标等静态资源
├── websocketMessage/ # WebSocket 消息处理（重力传感器等）
├── main.js       # 入口：注册插件、全局组件、路由守卫、cube-ui、微信 SDK
└── theme.styl    # 主题变量
```

其他重要目录：`build/`（Webpack 配置）、`config/`（环境配置）、`deploy/`（OSS 上传脚本、微应用配置）。

### 动态路由系统

后端驱动的前端路由：

1. 用户登录后，`src/store/modules/user.js` 的 `GenerateRoutes` action 调用 `getStaffMenus()` + `getStaffViews()` 获取菜单
2. 菜单数据通过 `resetRouter()` 转换为路由配置，组件通过 require 映射到 `@/views/` 下的文件
3. 使用 `router.addRoutes()` 动态注册
4. 基础路由定义在 `src/router/index.js`（登录、分享、图表等白名单页面）

### 路由守卫（`src/main.js` 的 `router.beforeEach`）

- 白名单（免登录）：`/`, `/login`, `/phone`, `/pendReview`, `/temporaryPhone`, `/msg`, `/share`, `/qingce`
- 已登录 + 页面刷新时：重新获取用户信息并调用 `GenerateRoutes` 生成动态路由
- 未登录：保存目标路径到 sessionStorage，重定向到 `/login`
- 设置页面标题（默认"简单购运营中心"）

### HTTP 客户端（`src/utils/fetch.js`）

Axios 实例配置了请求/响应拦截器：

- **请求拦截**：自动设置 `Client: mp_wechat`、`Authorization`、`country` 头；GET 参数自动 trim 和数组 join；token 过期时自动刷新
- **响应拦截**：解包到 `response.data`；根据 Vuex `statusCode.statusMap`（运行时从 OSS JSON 加载）显示错误 toast；500 错误显示 traceId

### Vuex Store 模块

| 模块 | 职责 |
|------|------|
| `user` | 认证、动态路由生成、分组/商户切换、token 刷新 |
| `wx` | 微信 JS-SDK 配置状态 |
| `keepalive` | `<keep-alive>` include 列表管理 |
| `globalData` | 跨页面共享状态（设备选择、重量数据等） |
| `statusCode` | namespaced 模块；从 OSS 加载的状态码映射 |
| `stockout` | 缺货图片/统计 |
| `productionTest` | WebSocket SSE 连接对象（设备生产测试） |

全局 getters：`routerStatus`, `addRouters`, `elements`, `userinfo`, `imgBase64`, `ids`, `keepAliveArr`。

### WebSocket

`App.vue` 中初始化 WebSocket 连接用于设备生产测试（`wss://` 协议），处理二进制消息（设备截图 PNG）和 JSON 消息。`src/websocketMessage/gravitySensorLable.js` 作为 mixin 处理双重力传感器校准消息。

### 全局注入

- `Vue.mixin(conf)` — `src/mixin/conf.js`：注入 OSS 图片 URL、组织 ID 数组（蒙牛、杭白等品牌的功能开关）
- `Vue.prototype.common` — `src/utils/common.js`：~1000 行工具库（toast、防抖节流、二维码解析、树操作、价格格式化等）
- `Vue.prototype.$moment` — moment.js
- `Date.prototype.Format(fmt)` — 全局日期格式化
- `src/filters/` — 全局过滤器自动注册（`filterPrice` 分转元、`parseTime`、`notNull`、`searchOptions` 等）
- cube-ui 组件全局注册（Button, Toast, Dialog, Scroll, Picker 等）
- Vant 通过 CDN 动态加载（依赖 `window.Vue`）

### 微信集成

- 微信 JS-SDK 签名在路由守卫中动态更新
- 支持：扫码、图片选择、地理位置、分享
- 模板中可使用 `<wx-open-launch-weapp>` 标签（ESLint 已忽略）

### 部署

- 构建产物上传到阿里云 OSS（`deploy/index.js`）
- 不同环境通过 `publicPath` 区分：`at`(test)、`as`(staging)、`ap`(prod)
- 支持微应用集成（检测 `__MICRO_APP_ENVIRONMENT__`）

## 开发规范

### OSS 图片上传

接口：`/api/tool/oss/upload`，必需参数 `subPrefix`：

| subPrefix | 用途 |
|-----------|------|
| `GOODS_IMAGE` | 商品图片 |
| `REPL_FINISH_IMAGE` | 补货完成图 |
| `REPL_SIGN_IMAGE` | 补货签名图 |
| `FEEDBACK_IMAGE` | 用户反馈图 |
| `AD_RESOURCE` | 广告图片/视频 |
| `SCAN_CODE` | 扫码贴 |
| `CERT_IMG` | 证件照 |

空状态图片：`` `${ossImgUrl}/img/nomore/xxx.png` ``

### 文件头注释

```vue
<!--
 * @description: [描述]
 * @fileName: 文件名.vue
 * @author: 作者名
 * @date: YYYY-MM-DD
!-->
```

## 技术栈

Vue 2.5.2 · Vue Router 3.0.1 · Vuex 3.1.1 · cube-ui · Webpack 3.6.0 · Axios · 微信 JS-SDK · Konva · Video.js · Sass/Stylus · ESLint (Standard.js) · Jest · Nightwatch
