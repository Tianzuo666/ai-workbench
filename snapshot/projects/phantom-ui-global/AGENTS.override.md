# 个人 Codex 项目说明（不提交）

本文件只在当前电脑使用，并通过 `.git/info/exclude` 排除。以后所有回复使用中文。

## 工作原则

- 修改前先明确假设、范围和成功标准；存在会影响实现方向的歧义时先询问。
- 只做请求直接需要的改动，不顺手重构、整理或格式化无关代码。
- 不读取或输出 `.env`、本地代理目标、令牌、密钥和其他凭据。
- 未获得用户明确批准，不运行安装、构建、测试、lint、类型检查、格式化或开发服务器。
- 不自行提交、推送、创建分支、MR 或 PR。
- 涉及跨仓关系或架构判断时，先阅读 `.codex-local/architecture/`，并以当前源码为最终依据。

## 当前仓库事实

- 仓库：`phantom-ui-global`；产品定位是运营商、商户使用的综合管理后台。
- 技术栈：Vue 2.5.17、Vue Router 3.0.2、Vuex 3.0.1、Element UI 2.15.5、Webpack 4.16.5。
- 运行环境以 `.nvmrc` 和 `package.json#engines` 为准：Node.js 16、npm 7+。
- 登录入口为 `/api/system/auth/staff/login`；分组选择会排除 `orgCategory` 为 `1` 或 `4` 的平台方、生产商。
- 菜单由后端返回，`src/store/modules/permission.js` 将组件字符串映射为页面并动态注册路由。
- 国际化使用 `vue-i18n`，入口为 `src/i18n/index.js`；新增用户可见文案时同时考虑中英文资源。
- 仓库配置 Jest 与 Vue Test Utils，但仍须得到用户明确批准后才能运行测试。
- `phantom-ui-ops-global` 是平级的平台/生产商后台，不是本仓库的父应用或子应用。

## 已知文档漂移

- `CLAUDE.md` 中 Qiankun 和 `convenience` 子应用的描述未在当前入口源码和依赖中得到验证。
- README 仍称系统面向平台方，但当前代码会排除平台方、生产商并提示使用新平台。
- 遇到冲突时优先采用 `package.json`、`.nvmrc`、入口源码和构建配置。

## 个人知识库

- 双仓关系：兄弟仓库 `phantom-ui-ops-global/.codex-local/architecture/repository-map.md`
- 本仓架构：`.codex-local/architecture/application.md`
