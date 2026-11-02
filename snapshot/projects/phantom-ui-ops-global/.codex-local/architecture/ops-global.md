# phantom-ui-ops-global 架构速查

> 个人本地文档，不提交。源码事实优先于已跟踪说明文件。

## 产品职责

本仓库是平台方、生产商使用的运营与设备运维后台。它与 `phantom-ui-global` 共用大量历史代码及后端服务，但面向不同组织类型。

## 启动链路

1. `src/main.js` 注册 Element UI、公共组件、指令、过滤器和全局 mixin。
2. `microApp.start()` 初始化 Micro App 运行时。
3. Vue 挂载 Router 与 Vuex。
4. 路由守卫读取 token；首次进入时调用 `GetStaffInfo`。
5. 后端返回菜单后，`GenerateStaffRoutes` 将组件字符串映射为 Vue 页面并调用 `router.addRoutes`。

## 登录与权限

- 登录：`POST /api/system/auth/ops/login`。
- 用户分组：`GET /api/system/currentStaff/groups`。
- 菜单：`GET /api/system/currentStaff/menus`。
- 视图权限：`GET /api/system/currentStaff/views`。
- 组织过滤：只保留 `orgCategory` 为 `1` 或 `4` 的分组。
- 固定路由只包含登录、错误页、Layout 和首页；大部分业务页面依赖动态菜单。

## 状态管理

Vuex 模块包括：

- `app`：布局、标签分组和页面缓存。
- `user`：登录态、用户、菜单、视图权限和分组切换。
- `permission`：动态路由生成。
- `machineWarn`：设备告警。
- `common`、`dataDictionary`、`deptTree`、`checkPage`、`statusCode`：共享业务状态。

页面自身数据仍以组件内状态为主，不要无必要地新增全局 Vuex 状态。

## Micro App 契约

入口页面：`src/views/marketManager/machine/base/deviceOperation.vue`。

```text
name: phantom-h5
url: /operationsManagement/#/
global data: { token }
page data: { vmCode }
```

调整该页面时需要确认：

- 子应用是否仍使用 `phantom-h5` 作为应用名。
- token 更新或退出登录后，子应用如何同步。
- 同一页面仅 query 变化时是否需要再次 `setData`。
- 生产部署是否正确代理 `/operationsManagement/`。

## 构建与环境

- Node.js 16，npm 7+。
- 开发端口 7777。
- Webpack 4，配置入口位于 `build/` 与 `config/index.js`。
- 环境脚本包括 prod、test、sit、staging、test01。
- `build:staging` 当前使用 `env_config=test` 和 `publicPath=os`，不要仅凭脚本名推断环境文件。
- 本地代理来自被忽略的 `config/dev.conf.js`，不要读取、复制或输出其实际地址，除非用户明确要求。

## 高频公共模式

- Blob 导出：接口设置 `responseType: 'blob'`，再调用 `downFile`。
- 异步导出：使用全局 `this.submitAsyncTask`。
- 订单类数组筛选参数：使用 `ensureArray`。
- Tab 恢复：使用 `v-tab-change`，保留查询参数 `action`。
- 时间计算：向 Moment 传时间戳。

## 文档冲突处理

- Qiankun 描述已过时；当前代码使用 `@micro-zoe/micro-app`。
- Node 12 描述已过时；以 `.nvmrc` 和 `package.json#engines` 的 Node 16 为准。
- 端口 7000 描述已过时；以 `config/index.js` 的 7777 为准。
