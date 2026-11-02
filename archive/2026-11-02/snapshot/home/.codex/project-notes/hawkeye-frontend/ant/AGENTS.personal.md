# ant 个人协作说明

- 本仓库同时承担国内独立 H5 和生产测试微应用两种角色，整体架构见 `docs/architecture.md`。
- 普通构建使用 `src/main.js` 和完整动态路由；`BUILD_TARGET=micro` 使用 `src/main.micro.js`、`src/router/micro.js` 与 `index.micro.html`，仅提供设备详情及生产测试相关页面。
- `hawkeye-southeast-asia` 是已确认的微应用宿主之一。它负责装载 `/productionTest/`、传入 token、过期时间、语言和设备编号；本仓库负责消费这些数据并提供微应用路由与生产测试能力。
- 修改 `src/main.micro.js`、`src/router/micro.js`、生产测试 Vuex/WebSocket 逻辑、认证存储格式或微应用产物路径时，必须同步核对海外宿主的个人契约文档及宿主入口。
- 与 `hawkeye-southeast-asia` 同路径的代码不等于可直接同步；两个仓库的框架版本、构建链、环境、国际化和业务能力均有差异。
- `hawkeye` 是独立微信小程序客户端。跨端复用以接口契约和业务规则为主，不复制 H5 页面实现。
- 不在仓库文档中写入本地代理、`.env`、令牌、账号或其他敏感配置的实际值。
