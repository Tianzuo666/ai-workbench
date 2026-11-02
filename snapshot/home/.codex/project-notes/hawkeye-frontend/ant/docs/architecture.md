# phantom-h5 架构说明

## 项目职责

本仓库是智能售货设备的国内运营 H5，同时提供可由其他主应用装载的生产测试微应用。两种运行形态复用业务源码，但入口、路由范围、认证初始化和部署方式不同。

## 双入口

| 运行形态 | 选择条件 | JavaScript 入口 | HTML 入口 | 路由 |
| --- | --- | --- | --- | --- |
| 独立 H5 | 默认 | `src/main.js` | `index.html` | `src/router/index.js` 及后端动态菜单 |
| 生产测试微应用 | `BUILD_TARGET=micro` | `src/main.micro.js` | `index.micro.html` | `src/router/micro.js` |

Webpack 配置在开发和生产构建中读取 `BUILD_TARGET`，并切换入口与 HTML 模板。业务代码不能只在普通入口初始化而默认微应用也能获得同样能力。

## 独立 H5 启动流程

1. `src/main.js` 注册 UI、过滤器、指令、全局工具和微信 SDK。
2. 登录后由 `src/store/modules/user.js` 获取员工信息、权限元素和菜单。
3. `GenerateRoutes` 将后端菜单转换为 Vue Router 路由并通过 `addRoutes` 注入。
4. `src/App.vue` 和业务模块管理设备状态、WebSocket 及页面级生命周期。

独立 H5 面向完整国内运营业务，不应受微应用精简路由限制。

## 微应用启动流程

1. `src/main.micro.js` 注册生产测试需要的 UI、过滤器、指令和全局工具。
2. 读取宿主传入的语言 data，并写入 `micro_` 前缀的本地存储。
3. 从 micro-app global data 读取 token 和过期信息，恢复认证会话。
4. 请求权限元素和当前员工信息。
5. 使用 `src/router/micro.js` 中的设备详情、生产测试和校准路由。

已确认的移动端海外宿主是 `hawkeye-southeast-asia`，其应用名为 `hawkeye-southeast`。完整字段和导航约定记录在该仓库的 `docs/production-test-micro-app-contract.md`。

## 主要模块

| 目录 | 职责 |
| --- | --- |
| `src/api/` | 按设备、商品、库存、财务、资产等业务域组织请求 |
| `src/router/` | 独立 H5 基础路由和微应用精简路由 |
| `src/store/` | 用户、权限、生产测试、缓存、全局数据等 Vuex 状态 |
| `src/views/` | 完整运营页面及生产测试页面 |
| `src/websocketMessage/` | 设备状态和传感器消息处理 |
| `src/utils/` | 请求、认证、公共方法和生产测试通信工具 |
| `build/`、`config/` | Webpack 3 构建和环境配置 |
| `deploy/` | OSS 环境目录映射与部署辅助代码 |

## 状态与通信

- `user` 管理认证、员工信息、权限元素和动态路由。
- `productionTest` 管理设备实时信息、图片、在线状态、型号和检测结果。
- `globalData` 承担部分跨页面共享状态。
- `keepalive` 管理缓存页面。
- WebSocket 消息由 `src/App.vue`、生产测试工具和 `src/websocketMessage/` 协同处理。

微应用与宿主共享认证和语言，但不共享 Vuex 实例；跨边界数据必须通过明确的 micro-app 契约传递。

## 构建与环境

- 开发基准为 Node 16.13.2，使用 npm。
- 独立 H5 支持 test、staging、prod 以及 OSS 构建。
- 微应用支持本地 `dev:micro` 和独立的 micro OSS 资源目录。
- 本地代理配置不提交到仓库，文档只描述字段结构，不记录实际环境值。

## 与其他仓库的边界

- `hawkeye-southeast-asia`：生产测试微应用宿主。宿主契约变化需要双仓同步。
- `hawkeye`：独立微信小程序。它共享部分 API 和业务规则，但不复用本仓库页面代码。
- 与海外 H5 同路径的源码已经分叉；移植功能时应按模块比较，不进行目录级覆盖。

## 变更原则

- 修改普通 H5 初始化时，确认微应用是否也需要同一能力。
- 修改共享业务模块时，分别验证独立路由和微应用精简路由下的依赖是否完整。
- 修改 token、locale、微应用名称、公共路径或生产测试路由时，同步更新宿主契约。
- 修改后端接口、状态枚举或 WebSocket 消息时，评估海外 H5 和小程序是否受影响。

## 待确认项

- `phantom-h5` PC 宿主的实际源码归属和发布流程。
- `/productionTest/` 与 micro OSS 目录之间的服务端映射。
- 国内独立 H5 与微应用的联合发布兼容策略。
