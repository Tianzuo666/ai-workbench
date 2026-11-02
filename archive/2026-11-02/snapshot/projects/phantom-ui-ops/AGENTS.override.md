# 个人本地覆盖规则

本文件仅供本机 Codex 使用，不应提交到仓库。

## 规则继承

- 开始工作前先阅读仓库已跟踪的 `AGENTS.md`，继续遵守其中仍然有效的业务规则、页面规范和操作边界。
- 本文件中的事实修正优先于 `AGENTS.md` 中与其冲突的描述。
- 所有回复使用中文。
- 实现前明确假设和成功标准；只做需求直接需要的最小改动。
- 未经用户明确批准，不运行安装、构建、测试、lint、类型检查、格式化或开发服务器。
- 不读取或输出 `.env`、令牌、凭据和其他敏感配置。

## 当前项目事实

- `phantom-ui-ops` 是独立的运营、运维和设备管理 SPA，不是 `phantom-ui` 的 Git 子项目。
- 开发环境以 `.nvmrc`、`package.json#engines` 和实际脚本为准：Node.js 16，npm 7 及以上。
- 核心栈为 Vue 2.5.17、Vue Router 3.0.2、Vuex 3.0.1、Webpack 4.16.5、Element UI 2.15.5。
- 微前端实现使用 `@micro-zoe/micro-app`，不是 Qiankun。
- 当前已确认的微应用入口是 `src/views/marketManager/machine/base/deviceOperation.vue`，它加载 `phantom-h5` 的 `/operationsManagement/#/`，并传递 `token` 与 `vmCode`。
- `src/views/layout/AppMain.vue` 中的 `appContainer` 暂未发现实际挂载逻辑，不要据此推断存在全局微前端路由。
- Vuex 实际注册 `app`、`user`、`permission`、`machineWarn`、`common`、`dataDictionary`、`deptTree`、`checkPage`、`statusCode` 模块。
- 后端菜单通过 `GenerateStaffRoutes` 映射为本地 `src/views` 组件；缺失组件回退到 410 页面。
- 本地开发端口为 7777；OSS 环境目录为 `op`、`os`、`ot`。

## 页面和兼容性

- 修改 `src/views/` 下的列表页、详情页或弹窗前，必须阅读 `docs/ui-spec.md`、`src/views/baseSetManager/componentSpec/index.vue` 和同类型既有页面。
- 优先复用 `@ainfinit/ainfinit-ui` 的现有组件，不重复实现同类组件。
- 禁止在 `.js` 和 `.vue` 中使用可选链 `?.`。
- 跨仓库工作前阅读 `C:\Users\zuoshi\.codex\project-knowledge\phantom-ui-suite\repository-map.md`；不要假设 `phantom-ui` 与本仓库的同路径文件保持同步。

