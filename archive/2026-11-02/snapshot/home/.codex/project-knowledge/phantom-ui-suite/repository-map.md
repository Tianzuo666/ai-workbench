# Phantom UI 多仓库个人知识库

更新日期：2026-10-10

本文件是个人本地知识，不属于任一业务仓库，不应提交。

## 仓库清单

### `C:\Users\zuoshi\Desktop\phantom-ui-ops`

- 独立 Git 仓库，远端仓库名 `phantom-ui-ops`。
- 主要承载平台运营、运维、设备管理、版本与灰度、SIM、识别和内部管理能力。
- 当前分支和发布分支可能变化，不能用分支名判断长期仓库职责。
- 已有仓库级 `AGENTS.md` 和 `docs/ui-spec.md`，但其中部分架构描述已经落后于代码。

### `C:\Users\zuoshi\Desktop\phantom-ui`

- 独立 Git 仓库，远端仓库名 `phantom-ui`。
- 主要承载商户、运营商、经营分析、仓储、补货、优惠券和礼券等业务。
- 与 Ops 具有大量同源目录和实现，但 Git 根提交不同，不是当前历史中的分支关系。

## 仓库关系和调用方向

```text
后端菜单/API
   |---> phantom-ui
   `---> phantom-ui-ops -- micro-app ---> phantom-h5
                                           /operationsManagement/#/

phantom-ui ----- npm -----> @ainfinit/ainfinit-ui
phantom-ui-ops -- npm -----> @ainfinit/ainfinit-ui
```

- 未发现 `phantom-ui` 和 `phantom-ui-ops` 之间的代码包依赖或互相加载关系；它们是并列部署的独立 SPA。
- Ops 在 `src/main.js` 启动 `@micro-zoe/micro-app`。
- Ops 的 `src/views/marketManager/machine/base/deviceOperation.vue` 加载名为 `phantom-h5` 的子应用，通过全局数据传递 `token`，通过应用数据传递 `vmCode`。
- `phantom-h5` 仓库不在当前附加目录中，其源码位置、发布流程和接口契约仍待补充。
- 两个 SPA 都从后端菜单生成动态路由：后端的 `route`、`title`、`meta`、`component` 映射到本地路由和 `src/views` 组件。

## 技术栈和环境

| 项目 | phantom-ui-ops | phantom-ui |
| --- | --- | --- |
| Node.js | 16 | 16 |
| npm | 7+ | 7+ |
| Vue | 2.5.17 | 2.5.17 |
| Vue Router | 3.0.2 | 3.0.2 |
| Vuex | 3.0.1 | 3.0.1 |
| Webpack | 4.16.5 | 4.16.5 |
| 本地端口 | 7777 | 7777 |
| OSS 环境目录 | `op` / `os` / `ot` | `pp` / `ps` / `pt` |
| Ainfinit UI | `^2.1.20` | `^2.1.18` |
| 微应用宿主 | `micro-app`，仅确认 `phantom-h5` | 未发现 |
| Mock | 入口中注释 | 入口中直接导入 |
| 单元测试 | 未配置 Jest 脚本 | Jest 23 |
| package-lock | lockfile v3 | lockfile v2 |

两个仓库开发端口相同，需要同时启动时应通过本地配置调整其中一个端口，不要提交该个人配置。

## 共享与重复实现

2026-10-08 对两个 `src` 目录中的 JS、Vue、SCSS、CSS、JSON 文件做过只读路径与 SHA-256 对比：

- Ops：953 个文件。
- 主站：1041 个文件。
- 相同相对路径：576 个。
- 同路径且内容完全一致：156 个。
- 同路径但内容已经分化：420 个。
- 仅 Ops 存在：377 个。
- 仅主站存在：465 个。

分化主要集中在：

- `src/views/baseSetManager`
- `src/views/IotManager`
- `src/views/orderManager`
- `src/views/marketManager`
- `src/views/commodityManager`
- 公共页面组件和对应 API 模块

结论：两个仓库属于并行演进的复制代码体系。修复公共逻辑时必须主动检查另一仓库，但未经明确需求不要自动双边修改。

## 业务差异

Ops 较突出的独有或强化方向：

- 版本分发、全局版本、灰度配置。
- 设备控制、设备开关、物料、标签和运维记录。
- SIM 管理、第三方识别统计、客户端日志。
- `phantom-h5` 设备运维微应用入口。

主站较突出的独有或强化方向：

- 仓库、库存入库、盘点和进销存。
- 补货、补货调度和运营商经营页面。
- 优惠券、会员、礼券和营销活动。
- Google Maps、坐标转换以及更完整的经营数据分析。

## 已知风险

- Ops 已跟踪的 `AGENTS.md` 写有 Node 12.8.1 和 Qiankun 2.10.15，与当前代码不符；个人 `AGENTS.override.md` 已覆盖这些事实，但共享文档仍然陈旧。
- Ops 的 README 克隆示例仍指向 `phantom-ui.git`，容易克隆错仓库。
- 同路径文件大量分化，单仓库修复可能在另一仓库继续保留同类缺陷。
- 两仓库的 `@ainfinit/ainfinit-ui`、Moment Timezone 和锁文件格式存在版本漂移。
- Ops 仍引用部分 `phantom-ui` OSS 静态资源，资源命名空间存在隐式耦合。
- `AppMain.vue` 中的 `appContainer` 未发现实际挂载逻辑，可能是遗留实现。

## 跨仓库修改检查清单

1. 先确认需求归属：平台运维类通常在 Ops，商户经营类通常在主站。
2. 用相对路径检查另一仓库是否存在同名文件。
3. 如果两边都存在，比较当前实现和接口，不要直接复制覆盖。
4. 检查 `@ainfinit/ainfinit-ui` 版本是否支持所用组件或属性。
5. 检查动态菜单返回的 `component` 路径与本地视图是否匹配。
6. 检查发布目录前缀，避免把 `op/os/ot` 与 `pp/ps/pt` 混用。
7. 任何安装、构建、测试、lint 或格式化命令均需用户明确批准。

## 待补充信息

- `phantom-h5` 的本地仓库路径、负责人和发布流程。
- 两个产品在组织内部使用的正式业务名称和功能边界。
- `AppMain.vue#appContainer` 是否计划删除或重新启用。
- 哪些公共模块要求强制双仓同步，哪些允许独立演进。

