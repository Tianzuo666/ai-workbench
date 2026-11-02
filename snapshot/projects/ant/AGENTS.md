# AGENTS.md

此文件为 Codex (Codex.ai/code) 在本代码仓库中工作时提供指导。

**以后所有回复都用中文模式**

## 项目概述

这是 "phantom-h5"，一个为"简单购"智能售货机管理平台构建的 Vue.js 2.x 单页应用。它是智能售货设备的运营管理中心，支持库存管理、设备监控、财务运营和物联网集成等功能。

## 开发命令

```bash
# 安装依赖
npm install

# 切换到开发基准 Node.js 版本 (16.13.2) 并启动开发服务器
npm run dev:cn

# 启动开发服务器（支持 Node 16.x）
npm run dev
npm start

# 构建不同环境
npm run build:test     # 测试环境 (publicPath: at)
npm run build:staging  # 预发布环境 (publicPath: as)
npm run build:prod     # 生产环境 (publicPath: ap)
npm run build:oss      # OSS 环境及自定义版本

# 测试
npm run unit           # 运行 Jest 单元测试（含覆盖率）
npm run e2e            # 运行 Nightwatch 端到端测试
npm test               # 运行所有测试

# 代码质量检查
npm run lint           # 运行 ESLint

# Node 版本管理
npm run changeNodeVersion  # 自动切换到 Node 16.13.2
```

**重要提示**：项目支持 Node.js 16.x（`>=16.0.0 <17.0.0`），开发基准版本为 16.13.2。使用 `npm run dev:cn` 可自动切换版本并启动开发服务器。

## 架构

### 动态路由系统

路由通过 `src/store/modules/user.js` 中的 `GenerateRoutes` action 从后端菜单数据动态生成。流程如下：

1. 认证时，`GenerateRoutes` 通过 `getStaffViews()` 和 `getStaffMenus()` 获取用户信息和菜单
2. 菜单数据（类型 3 和类型 4）通过 `resetRouter()` 合并并转换为路由配置
3. 使用 `router.addRoutes()` 添加路由
4. 通配符重定向到 `/msg` 处理未匹配的路径

基础路由定义在 `src/router/index.js` 中（登录、分享、图表等）。

### Vuex Store 结构

位于 `src/store/`：

- **user**: 用户认证、动态路由生成、分组/商户切换
- **wx**: 微信 SDK 集成和签名管理
- **stockout**: 缺货相关状态
- **keepalive**: Vue 的 keep-alive 页面缓存状态
- **globalData**: 全局应用数据
- **productionTest**: 生产测试工具

### API 组织

`src/api/` 中的 API 模块按业务领域组织（如 `device/`、`commodity/`、`finance/`、`assets/`）。

### WebSocket 集成

实时更新在 `src/websocketMessage/` 中处理，用于设备状态和传感器数据。

## 开发规范

### 本地配置

为了避免 `config/index.js` 中的合并冲突，创建本地 `config/dev.conf.js` 文件：

```javascript
module.exports = {
  proxyTable: {
    '/api': {
      target: 'https://test.ainfinit.com/',
      changeOrigin: true,
      secure: true,
      ws: true,
      pathRewrite: { '^/api': '/api' }
    }
  }
}
```

添加业务逻辑时，在此处添加新的代理配置。

### OSS 图片上传

上传接口：`/api/tool/oss/upload`

必需参数：`subPrefix` 用于分类上传：

- `GOODS_IMAGE` - 商品图片
- `REPL_FINISH_IMAGE` - 补货完成图
- `REPL_SIGN_IMAGE` - 补货签名图
- `FEEDBACK_IMAGE` - 用户反馈图
- `AD_RESOURCE` - 广告图片/视频
- `SCAN_CODE` - 扫码贴
- `CERT_IMG` - 证件照

### 空状态图片

位置：OSS 上的 `/mp-h5/img/nomore/`

使用方式：`` `${ossImgUrl}/img/nomore/xxx.png` ``

### 文件头注释

在 Vue 文件中添加：

```vue
<!--
 * @description: [描述]
 * @fileName: 文件名.vue
 * @author: 作者名
 * @date: YYYY-MM-DD
!-->
```

### 微信集成

- 微信签名动态生成
- SDK 集成用于微信生态功能
- 微信浏览器环境的特殊处理
- 忽略微信特定模板标签：`<wx-open-launch-weapp>`

### 全局工具

- Vue 原型上的 `common` 对象：`this.common`
- Vue 原型上的 `moment`：`this.$moment`
- 日期格式化：全局可用的 `Date.prototype.Format(fmt)`

## 核心技术栈

- **Vue 2.5.2** 搭配 Vue Router 3.0.1 和 Vuex 3.1.1
- **Cube-ui** - 主要 UI 组件库
- **微信 JS SDK** - 微信生态集成
- **Konva** - Canvas 图形库
- **Vue-cropper** - 图片裁剪
- **Axios** - HTTP 客户端
- **Video.js** - 视频播放
- **Webpack 3.6.0** - 构建工具
- **ESLint** 配合 Standard.js 配置
- **Jest** - 单元测试
- **Nightwatch** - 端到端测试
