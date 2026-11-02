# Phantom Global 双仓关系

> 个人本地文档，不提交。依据 2026-10-10 的本地工作树整理。

## 1. 仓库清单

| 仓库 | 远端 | 产品职责 | 主要用户 |
| --- | --- | --- | --- |
| `phantom-ui-ops-global` | `global/phantom-ui-ops-global.git` | 平台运营、设备运维及平台级配置 | 平台方、生产商 |
| `phantom-ui-global` | `global/phantom-ui-global.git` | 设备、订单、库存、商品、补货、报表等综合管理 | 运营商、商户 |

两个目录都是独立 Git 仓库，不是 monorepo 子项目。

## 2. 运行时关系

```text
phantom-ui-ops-global ─┐
                       ├─→ 共享后端服务
phantom-ui-global ─────┘    /api/system
                            /api/operation
                            /api/iot
                            /api/analysis 等

phantom-ui-ops-global ──→ /operationsManagement/#/
                           phantom-h5 运维子应用
```

- 未发现两个 Web 仓库之间的直接 import、构建依赖或互相挂载。
- 两仓通过相似的 API、动态菜单结构和业务模型形成间接契约。
- `phantom-ui-ops-global` 加载的微应用是独立的 `phantom-h5`，不是 `phantom-ui-global`。

## 3. 用户边界

### phantom-ui-ops-global

- 登录接口：`/api/system/auth/ops/login`。
- `getCurrentStaff` 只保留 `orgCategory === 1 || orgCategory === 4`。
- 对应代码注释表明这两类是生产商与平台方。

### phantom-ui-global

- 登录接口：`/api/system/auth/staff/login`。
- 分组选择会排除 `orgCategory === 1` 和 `orgCategory === 4`。
- `App.vue` 会提示平台方、生产商前往新平台。

## 4. 共享代码现状

对 `src`、`build`、`config`、`scripts`、`test` 的本地文件进行相对路径和 SHA-256 对比：

| 指标 | 数量 |
| --- | ---: |
| 运维仓文件 | 637 |
| 商户仓文件 | 792 |
| 相同相对路径 | 420 |
| 内容完全一致 | 78 |
| 同路径但内容不同 | 342 |

典型共享区域：

- 分页、剪贴板、水波纹、认证、事件总线等基础模块。
- 部分 mixin、Vuex 模块、API 定义和动态路由转换逻辑。
- Webpack 4 构建框架、本地代理加载方式和异步导出模式。

结论：两仓来自共同代码基线，但分化程度已经较高。不要整目录覆盖同步；只能按模块确认业务边界后做小范围迁移。

## 5. 环境差异

| 项目 | ops-global | global |
| --- | --- | --- |
| Node / npm | Node 16 / npm 7+ | Node 16 / npm 7+ |
| 微应用 | `@micro-zoe/micro-app` | 未发现微应用初始化 |
| 国际化 | 旧式语言状态，无完整 i18n 目录 | `vue-i18n`，含英文资源与切换组件 |
| 自动化测试 | 未配置 Jest | Jest + Vue Test Utils |
| 构建变体 | prod、test、sit、staging、test01 | 另有 dev1～dev3、OSS 构建 |
| 地图扩展 | 高德加载器 | Google Maps、gcoord |

## 6. 主要风险

1. 已跟踪文档仍描述 Qiankun，但实际源码使用 Micro App 或完全没有微前端初始化。
2. 同路径文件大量分化，人工跨仓复制容易覆盖产品差异。
3. 后端菜单直接携带前端组件路径，重命名页面会破坏服务端菜单契约。
4. `phantom-h5` 源仓未附加，当前只能确认 `token`、`vmCode` 两项数据契约。
5. 尚未确认共享模块的权威来源，也未确认平台方从旧平台迁移的完成状态。
6. 两仓都依赖本地 `config/dev.conf.js`；该文件可能包含环境地址，不应进入知识库或输出。

## 7. 跨仓改动检查清单

- 先判断需求属于平台/生产商，还是运营商/商户。
- 搜索另一个仓库是否存在同路径实现，但不要默认两边都要修改。
- 对比登录分组、权限、国际化和产品专属功能后再移植。
- API 字段或动态菜单组件路径变化时，明确后端兼容范围。
- 涉及 `/operationsManagement/` 时，确认 `phantom-h5` 的版本和数据契约。
