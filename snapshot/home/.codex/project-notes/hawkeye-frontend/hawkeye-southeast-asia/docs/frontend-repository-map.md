# 前端仓库关系图

## 文档目的

本文记录海外运营应用、国内运营 H5 和微信小程序之间已确认的职责、运行时调用方向及协作边界。它用于判断改动应该落在哪个仓库，以及哪些变更需要跨仓核对。

分析基线为 2026-10-08。结论来自各仓库的 `package.json`、README、构建入口、路由、微应用入口和 API 源码；未读取 `.env`、本地代理或凭据文件。

## 仓库清单

| 仓库 | 产品职责 | 主要运行形态 | 技术基线 | 包管理器 |
| --- | --- | --- | --- | --- |
| `hawkeye-southeast-asia` | 海外运营主应用 | Web、移动容器、微应用宿主 | Vue 2.6、Vue CLI 5、Vuex、Vant、Cube UI、micro-app | npm |
| `ant` | 国内运营 H5；生产测试子应用源码 | 独立 H5、micro-app 子应用 | Vue 2.5、Webpack 3、Vuex、Cube UI、微信 JS SDK | npm |
| `hawkeye` | 微信小程序运营客户端 | 原生微信小程序、多分包 | Vant Weapp、MobX、miniprogram-computed | npm |

三个目录都有独立 `.git` 和独立远端，因此是三个独立仓库，不是 monorepo 子项目。

## 运行时关系

```text
hawkeye-southeast-asia
    └─ micro-app 宿主
       └─ 当前服务域名 /productionTest/
          └─ ant 的 BUILD_TARGET=micro 产物
             ├─ src/main.micro.js
             └─ src/router/micro.js

hawkeye
    └─ 独立微信小程序
       └─ 与两个 H5 客户端共享部分后端接口和业务规则
```

已确认的调用方向只有宿主到生产测试子应用：

1. `hawkeye-southeast-asia/src/main.js` 启动 micro-app。
2. `hawkeye-southeast-asia/src/views/common/productionTest.vue` 加载 `/productionTest/`，传递认证数据、语言和设备编号。
3. `ant` 在 `BUILD_TARGET=micro` 时切换到 `src/main.micro.js` 和 `src/router/micro.js`。
4. 子应用读取宿主数据，初始化认证、语言、权限和生产测试页面。

`hawkeye` 中未发现 WebView、micro-app、`postMessage` 或对应消息监听，因此不能把业务相似性描述成直接运行时依赖。

## 代码所有权

| 能力 | 主所有者 | 协作仓库 | 边界 |
| --- | --- | --- | --- |
| 海外登录、服务区域、国际化、Web/移动适配 | `hawkeye-southeast-asia` | 无 | 海外差异留在宿主仓库 |
| 生产测试微应用页面与微应用入口 | `ant` | `hawkeye-southeast-asia` | 子应用负责页面，宿主负责装载和数据传递 |
| 国内完整 H5、微信环境和动态菜单 | `ant` | 无 | 不由海外仓库反向覆盖 |
| 微信小程序页面、组件、分包和交互 | `hawkeye` | 无 | 遵守小程序 UI 规范，不复制 H5 页面实现 |
| 后端接口和业务枚举的前端消费 | 各客户端分别维护 | 三个仓库 | 接口变更需要跨端影响检查 |

## 共享与分叉

在分析基线中，两个 H5 仓库的 `src` 下有 194 个相同相对路径：

- 128 个文件内容完全一致，其中 121 个是 SVG 资源。
- 66 个同路径文件内容不同。
- `device`、`smartVm`、`productionTest`、`user`、`auth` 和 `fetch` 等抽查的核心文件均已分叉。

因此两个 H5 仓库属于历史同源、分别演进。同步改动时应比较业务意图和接口契约，不应按目录批量覆盖。

三个客户端都消费部分相同后端领域，例如员工登录、组织切换、设备详情和文件上传。共享的是接口与规则，不是可直接复用的 UI 代码。

## 环境差异

| 仓库 | 环境与构建差异 |
| --- | --- |
| `hawkeye-southeast-asia` | `web` 与 `mobile` 两种模式；Web 使用反向代理，移动端使用完整服务地址；支持亚洲与国际服务区域 |
| `ant` | 独立 H5 与 `micro` 两种目标；支持 test、staging、prod 及 OSS 版本目录 |
| `hawkeye` | 由微信开发者工具运行；本地服务地址位于未跟踪配置；页面按主包和多个业务分包发布 |

海外仓库推荐 Node 16.13.0，`ant` 的开发基准为 Node 16.13.2。小程序仓库当前没有声明 Node engines。

## 跨仓改动检查

以下改动需要核对多个仓库：

- 微应用名称、入口 URL、默认路由、token、过期时间或语言字段变化：核对 `hawkeye-southeast-asia` 和 `ant`。
- 生产测试 API、WebSocket 消息或设备状态结构变化：核对两个 H5 仓库，必要时再核对小程序。
- 登录、组织切换、设备详情、上传参数或业务枚举变化：核对三个客户端的同领域 API。
- 只涉及海外服务区域、国际化或移动原生导航的变化：通常只修改 `hawkeye-southeast-asia`。
- 只涉及微信小程序组件和交互的变化：只修改 `hawkeye`，但接口变化仍需跨端检查。

## 风险与待确认项

- `/productionTest/` 如何映射到 `ant` 的 micro 构建产物，仓库内尚无完整部署说明。
- `ant/src/main.micro.js` 还识别名为 `phantom-h5` 的 PC 宿主，该宿主的源码归属尚未确认。
- 宿主组件使用的应用名与路由监听键目前不同，需结合当前 micro-app 版本和实际运行结果确认是否符合预期。
- 尚无统一的接口版本、状态枚举或跨仓发布兼容策略。
- 代码重合统计只反映分析基线，后续不能把数字当作长期不变的项目指标。

## 相关文档

- `docs/production-test-micro-app-contract.md`：生产测试宿主与子应用契约。
- `ant/docs/architecture.md`：国内 H5 与微应用双入口架构。
- `hawkeye/docs/architecture.md`：微信小程序分包及公共能力架构。
- `hawkeye/docs/miniprogram-ui-spec.md`：小程序页面与组件规范。
