# 生产测试微应用契约

## 范围

本文约束 `hawkeye-southeast-asia` 宿主与 `ant` 生产测试微应用之间的运行时接口。它只描述代码中已存在的契约，不包含环境地址、账号、令牌或部署凭据的实际值。

## 角色

| 角色 | 仓库 | 入口 |
| --- | --- | --- |
| 宿主应用 | `hawkeye-southeast-asia` | `src/views/common/productionTest.vue` |
| 子应用 | `ant` | `src/main.micro.js`、`src/router/micro.js` |
| 构建选择 | `ant` | `BUILD_TARGET=micro` |

宿主负责装载、身份与语言传递、初始路由和移动端返回行为。子应用负责设备详情、生产测试页面、权限读取、状态管理和设备通信。

## 装载契约

宿主 `<micro-app>` 当前使用：

| 字段 | 当前值或规则 |
| --- | --- |
| `name` | `hawkeye-southeast` |
| `router-mode` | `search` |
| `keep-router-state` | 启用 |
| Web 正式 URL | 当前站点的 `/productionTest/` |
| Web 开发 URL | 当前所选服务地址；本地联调时可替换为 `ant` 的 `dev:micro` 地址 |
| 移动端 URL | 当前服务区域地址下的 `/productionTest/` |
| 默认页 | `/#/deveiceDetail` |
| 默认页参数 | `vmCode`、`locale` |

`deveiceDetail` 是现有路由拼写，修改拼写属于破坏性契约变更，必须同时修改宿主、子应用路由和已部署入口。

## 数据契约

### 宿主到子应用

| 通道 | 字段 | 含义 |
| --- | --- | --- |
| `<micro-app :data>` | `locale` | 当前界面语言；缺失时子应用移动端默认 `en`，PC 分支默认 `zh` |
| `microApp.setGlobalData` | `token` | 当前认证令牌 |
| `microApp.setGlobalData` | `expire` | 令牌过期信息，供子应用恢复认证存储 |
| 默认路由 query | `vmCode` | 当前设备编号 |
| 默认路由 query | `locale` | 首屏语言兜底 |

子应用对语言的读取优先级为 URL 参数、micro-app data、运行形态默认值。收到 data 后，子应用会将字段写入带 `micro_` 前缀的本地存储，并在语言变化时重新加载状态码映射。

### 子应用内部初始化

1. 从 micro-app global data 读取 token 和过期信息。
2. 初始化认证存储并标记会话可用。
3. 请求当前用户权限元素和用户信息。
4. 根据精简路由进入设备详情或生产测试页面。

当前没有定义独立的子应用到宿主业务消息协议。导航状态主要通过 micro-app 路由能力处理。

## 子应用路由

`ant/src/router/micro.js` 当前暴露：

| 路径 | 作用 |
| --- | --- |
| `/` | 重定向到 `/deveiceDetail` |
| `/deveiceDetail` | 设备详情入口 |
| `/smartVm` | 智能设备入口 |
| `/smartVmProduction` | 智能设备生产信息 |
| `/smartProductionTest` | 生产测试 |
| `/calibration` | 校准页面 |

新增微应用页面时，应先判断是否真的需要加入精简路由；不要把完整国内 H5 菜单直接带入微应用产物。

## 导航与生命周期

- 宿主监听子应用路由，用于判断 iOS 原生容器中的返回行为。
- 子应用处于首个设备详情页时，宿主返回操作应退出当前原生 WebView；处于更深层页面时，应先让子应用路由后退。
- 宿主在组件挂载时写入全局认证数据。子应用入口假定自己运行在 micro-app 环境中，并直接访问 `window.microApp`。
- 子应用页面依赖宿主或当前服务域名提供的 API、WebSocket 和静态资源可访问性。

## 构建与部署边界

- `ant` 使用 `BUILD_TARGET=micro` 选择 `src/main.micro.js` 和 `index.micro.html`。
- `ant` 的 OSS 构建将 micro 环境映射到独立的 `*-micro` 资源目录。
- 宿主运行时固定从 `/productionTest/` 装载入口，但该入口如何映射到具体 micro 产物由部署层负责。
- 修改公共路径、资源目录或 HTML 入口时，必须同时检查宿主 URL、服务端路由回退和静态资源加载路径。

## 变更检查表

修改契约时至少核对：

1. 宿主和子应用使用的应用名一致。
2. 默认路由及 `vmCode`、`locale` 参数仍可被子应用读取。
3. token 与 `expire` 的结构仍符合 `ant/src/utils/auth.js`。
4. Web、移动端以及亚洲/国际服务区域都能得到正确的子应用 URL。
5. 子应用刷新和深层路由返回不会脱离宿主。
6. micro 构建产物被部署到 `/productionTest/` 对应入口。
7. 不把环境实际值、token 或账号写入源码和文档。

## 待确认项

- `<micro-app>` 的 `name` 为 `hawkeye-southeast`，宿主路由监听与读取当前路由时使用 `productionTest` 键。需要结合当前 `@micro-zoe/micro-app` 版本和实际运行结果确认键名规则。
- `ant/src/main.micro.js` 识别 `phantom-h5` PC 宿主，但该宿主不在当前三个仓库的已确认关系中。
- 当前仓库没有完整记录 `/productionTest/` 到 micro OSS 目录的服务端映射和发布顺序。
