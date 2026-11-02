# 个人 Codex 项目说明（不提交）

本文件只在当前电脑使用，并通过 `.git/info/exclude` 排除。以后所有回复使用中文。

## 工作原则

- 修改前先明确假设、范围和成功标准；存在会影响实现方向的歧义时先询问。
- 只做请求直接需要的改动，不顺手重构、整理或格式化无关代码。
- 不读取或输出 `.env`、本地代理目标、令牌、密钥和其他凭据。
- 未获得用户明确批准，不运行安装、构建、测试、lint、类型检查、格式化或开发服务器。
- 不自行提交、推送、创建分支、MR 或 PR。
- 涉及跨仓关系或架构判断时，先阅读 `.codex-local/architecture/` 下的个人文档，并以当前源码为最终依据。

## 当前仓库事实

- 仓库：`phantom-ui-ops-global`；产品定位是平台方、生产商使用的运营/运维后台。
- 技术栈：Vue 2.5.17、Vue Router 3.0.2、Vuex 3.0.1、Element UI 2.15.5、Webpack 4.16.5。
- 运行环境以 `.nvmrc` 和 `package.json#engines` 为准：Node.js 16、npm 7+。
- 登录入口为 `/api/system/auth/ops/login`，登录后只保留 `orgCategory` 为 `1` 或 `4` 的分组。
- 菜单由后端返回，`src/store/modules/permission.js` 将组件字符串映射为页面并动态注册路由。
- 当前微前端实现是 `@micro-zoe/micro-app`，不是 Qiankun。
- `src/views/marketManager/machine/base/deviceOperation.vue` 加载 `/operationsManagement/#/` 下的 `phantom-h5`，传入全局 `token` 和页面数据 `vmCode`。
- `phantom-ui-global` 是平级的商户/运营商后台，不是本仓库的子应用。

## 已知文档漂移

- 已跟踪的 `AGENTS.md`、`CLAUDE.md` 中关于 Qiankun 的描述已经过时。
- 已跟踪的 `AGENTS.md` 中 Node.js 12.8.1 的描述已经过时。
- README 中曾出现端口 7000；当前 `config/index.js` 的开发端口是 7777。
- 遇到冲突时优先采用 `package.json`、`.nvmrc`、入口源码和构建配置。

## 个人知识库

- 双仓关系：`.codex-local/architecture/repository-map.md`
- 本仓架构：`.codex-local/architecture/ops-global.md`
