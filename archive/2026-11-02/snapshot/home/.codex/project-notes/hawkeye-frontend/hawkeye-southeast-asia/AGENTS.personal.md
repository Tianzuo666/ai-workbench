# hawkeye-southeast-asia 个人协作说明

- 本仓库是海外 Web/移动端主应用，也是生产测试微应用的宿主。跨仓关系见 `docs/frontend-repository-map.md`。
- 生产测试子应用源码归属 `ant` 仓库；本仓库只负责装载入口、运行地址、认证与语言数据传递、宿主导航及移动容器适配。契约见 `docs/production-test-micro-app-contract.md`。
- 修改 `src/views/common/productionTest.vue`、micro-app 初始化、认证存储格式、语言字段或 `/productionTest/` 部署路径时，必须同步核对 `ant` 的 `src/main.micro.js`、`src/router/micro.js` 和微应用构建配置。
- 本仓库与 `ant` 存在历史同源代码，但核心模块已经分叉。不得按同路径直接覆盖或批量同步；应按业务需求逐项移植并保留海外环境、国际化和移动端差异。
- `hawkeye` 是独立微信小程序客户端。当前没有发现它与本仓库之间的 WebView 或微前端运行时调用；跨端协作以共享后端接口和业务规则为边界。
- 不在文档、代码或提交信息中记录 `.env`、本地代理、令牌、账号或其他敏感配置的实际值。
