# 个人本地项目规则

本文件仅供本机 Codex 使用，不应提交到仓库。

## 工作方式

- 所有回复使用中文。
- 实现前明确假设和成功标准；存在多种解释时先说明取舍。
- 只做需求直接需要的最小改动，不顺手重构或清理无关代码。
- 未经用户明确批准，不运行安装、构建、测试、Jest、lint、类型检查、格式化或开发服务器。
- 不读取或输出 `.env`、令牌、凭据和其他敏感配置。
- 修改前先检查目标目录是否出现更深层的 `AGENTS.md` 或 `AGENTS.override.md`。

## 项目事实

- `phantom-ui` 是独立的商户、运营商和经营业务 SPA，与 `phantom-ui-ops` 是并列仓库，不是其子应用。
- 开发环境以 `.nvmrc`、`package.json#engines` 和实际脚本为准：Node.js 16，npm 7 及以上。
- 核心栈为 Vue 2.5.17、Vue Router 3.0.2、Vuex 3.0.1、Webpack 4.16.5、Element UI 2.15.5。
- 当前仓库没有 `@micro-zoe/micro-app` 或 Qiankun 依赖，不要把 Ops 的微前端说明套用到本仓库。
- 后端菜单通过 `GenerateStaffRoutes` 映射为本地 `src/views` 组件；缺失组件回退到 410 页面。
- `src/main.js` 当前直接导入 `src/mock/index.js`；调整接口联调行为时必须考虑这一差异。
- Vuex 注册 `app`、`user`、`permission`、`machineWarn`、`common`、`dataDictionary`、`statusCode` 模块。
- 本地开发端口为 7777；OSS 环境目录为 `pp`、`ps`、`pt`。
- 仓库包含 Jest 配置和单元测试脚本，但执行测试仍需用户明确批准。

## 页面和兼容性

- 修改页面前优先参考本仓库同类型既有业务页面和现有 `@ainfinit/ainfinit-ui` 用法，不直接照搬 Ops 的业务专属页面。
- 对需要与 Ops 保持一致的通用列表、详情或弹窗，可将 `phantom-ui-ops/docs/ui-spec.md` 作为对照，但必须先核实本仓库已有样式和组件是否一致。
- 优先复用已有组件，不另起视觉体系或创建一次性抽象。
- 禁止在 `.js` 和 `.vue` 中使用可选链 `?.`，保持当前 Babel/Webpack 工具链兼容。
- 跨仓库工作前阅读 `C:\Users\zuoshi\.codex\project-knowledge\phantom-ui-suite\repository-map.md`；不要假设两个仓库的同路径文件保持同步。

