thread_id: 01a1622c-ade0-7612-89c8-079f49269c60
updated_at: 2026-10-22T10:21:21+00:00
rollout_path: \\?\C:\Users\zuoshi\.codex\sessions\2026\10\22\rollout-2026-10-22T13-54-17-01a1622c-ade0-7612-89c8-079f49269c60.jsonl
cwd: \\?\C:\Users\zuoshi\Desktop\hawkeye-southeast-asia
git_branch: fix-img-click

# 完成海外 App 从 Vue 2/HTML5+ 迁移到 uni-app Vue 3 的可行性与改造前后对比分析

Rollout context: 工作目录为 `C:\Users\zuoshi\Desktop\hawkeye-southeast-asia`。用户先要求分析重构为 uni-app，随后明确范围为“Android + iOS，保留 H5”，并追问改造前后的优劣。全程只读分析，未修改代码、构建或测试。

## Task 1: 评估迁移到 uni-app 的架构与风险

Outcome: success

Preference signals:

- 用户先说“分析下”，助手先进行只读梳理，未直接改代码；这表明类似架构评估应先确认现状、边界和风险，再提出实施方案。
- 用户明确选择“Android + iOS，保留 H5”，且暂不合并微信小程序；后续方案应以三端为范围，不默认扩展到小程序。

Key steps:

- 核实当前项目为 Vue 2.6 + Vue CLI 5，使用 Vuex、Vue Router、Vant 2、Cube UI，并通过 `VUE_APP_MODEL` 区分 Web/mobile。
- 统计仓库有 222 个 Vue 文件、`src/views` 下 206 个 Vue 文件、41 个 API JS 文件；206 个文件不等于独立业务页面，完整页面范围仍需结合服务端菜单确认。
- 确认 App 基于 HBuilder/HTML5+，大量使用 `plus.*`：扫码、定位、文件、下载、推送、相机、图库、原生 WebView、多 WebView 导航等。
- 确认权限路由由后端菜单动态生成，旧实现通过 `src/store/modules/user.js` 动态加载组件并注册路由；uni-app 需要改为页面预注册、菜单到页面标识的映射和权限控制。
- 确认生产测试通过 `src/views/common/productionTest.vue` 使用 micro-app 加载 `/productionTest/`，传递 `token`、`expire`、`locale`、`vmCode`；建议首期通过 `web-view` 保留独立 H5 子应用边界，但认证、返回和通信必须验证。
- 识别高风险能力：扫码、Google Maps、图片裁剪、SortableJS 拖拽、html2canvas 截图、视频、设备二进制 WebSocket、iOS 多 WebView 返回和结果回传。
- 参考 uni-app 官方资料确认：App 逻辑层不能直接依赖浏览器 `window/document`；DOM 库需考虑 `renderjs`，WebSocket 可接收 String/ArrayBuffer，上传可使用 `uni.uploadFile` 的进度和取消能力，普通 uni-app App 仍可按需使用 `plus.*`。

Failures and how to do differently:

- 本轮没有运行构建、测试、真机或模拟器验证，因此兼容性判断属于源码和文档层面的评估，不能称为已验证迁移方案。
- 不应把旧 Vue 文件逐行转换为 uni-app 页面；应优先复用接口契约、数据转换和业务规则，并重新设计页面、导航、平台能力和生命周期。
- 不应等普通页面全部迁移后才验证复杂能力；应先做高风险样板，尤其是登录、扫码、设备详情、WebSocket、裁剪上传和生产测试。

Reusable knowledge:

- 请求层必须保留现有契约：`Client: global_app_operate`、`Authorization`、`country`、`Accept-TimeZone`、动态服务区 `currentServer`，以及 token 刷新和失效处理。
- 当前普通用户与品牌商有不同的底部 Tab 列表，并按后端权限过滤；uni-app 的 `pages.json`/底部导航设计必须保留角色差异。uni-app 原生 tabBar 通常限制为 2–5 项，因此动态权限展示需单独设计。
- 设备 WebSocket 在 `src/App.vue` 中同时处理 JSON 消息和二进制图片：去掉首字节后通过 Blob/FileReader 转图片，并持续发送状态上报；迁移时需明确 ArrayBuffer 解码、连接归属、退出清理、后台恢复和重复连接策略。
- 现有生产测试微应用边界和契约是：宿主 `src/views/common/productionTest.vue`、部署入口 `/productionTest/`、应用名 `hawkeye-southeast`、上下文 `token/expire/locale/vmCode`、默认路由 `/deveiceDetail`。

References:

- `AGENTS.md`：明确要求回复使用中文，并记录 Vue 2、Web/mobile、HBuilder/HTML5+ 架构。
- `src/utils/fetch.js`：请求头、动态 baseURL、token 刷新和统一错误处理。
- `src/store/modules/user.js`：后端菜单动态生成路由、角色和默认首页逻辑。
- `src/views/common/productionTest.vue`：micro-app 子应用入口和认证/语言/设备参数传递。
- `src/App.vue`：生产测试 WebSocket、二进制图片解析和定时状态上报。
- `src/native/router.js`、`src/native/bridge.js`：iOS 多 WebView 导航、参数注入、结果回传和 `evalJS` 通信。

## Task 2: 对比改造前后的优劣

Outcome: success

Preference signals:

- 用户在完成迁移评估后继续追问“对比下改造前后的优劣点”，说明后续架构建议应同时给出收益、代价、适用目标和不适合迁移的场景，而不只给出技术方案。

Key steps:

- 对比结论：改造后主要收益是三端维护、导航和平台能力边界更统一；主要代价是迁移量大、复杂能力需重做、短期回归风险高。
- 明确性能和稳定性不能因改用 uni-app 自动提升，必须通过真实设备和业务链路实测。
- 建议采用“新建 uni-app Vue 3 工程、按业务模块逐步迁移、旧 H5 过渡运行”的方式，而非一次性替换。
- 建议第一条完整样板链路为“登录 → 设备列表 → 扫码 → 设备详情”，同时验证裁剪上传、设备 WebSocket 和生产测试接入。

Reusable knowledge:

- 改造前优势：现有业务稳定、团队熟悉、HTML/浏览器能力直接、复杂功能已有修复积累、短期需求交付更快。
- 改造后潜在优势：统一 Android/iOS/H5 页面和平台适配、减少自建 iOS WebView 导航、集中封装扫码/定位/上传/存储、升级 Vue 3 和长期开发规范。
- 改造后主要代价：Vant/Cube UI、Vue Router、keep-alive、动态权限路由、组件模板和生命周期均需适配；地图、裁剪、拖拽、截图、视频、扫码和二进制 WebSocket 不能简单复制。
- 如果目标只是解决某个卡顿、闪退或扫码问题，整体迁移投入可能过大；如果目标是长期维护 Android+iOS+H5 并降低平台分歧，则分阶段迁移更有价值。

Failures and how to do differently:

- 不要承诺“迁移后性能一定更好”或“完全消除平台兼容问题”；应以样板链路的启动、交互、性能和真机回归结果决定是否扩大迁移范围。
- 不要把 uni-app 与 uni-app x 混为一谈。本轮建议的是普通 uni-app Vue 3；uni-app x 需要单独评估 Vue/uvue、原生渲染和插件兼容性。

References:

- 用户确认范围：`Android + iOS，保留 H5`。
- 建议迁移顺序：范围盘点 → 高风险样板 → 公共基础层 → 分批业务迁移 → 发布与灰度。
- 未完成验证项：完整服务端菜单清单、首期业务范围、签名/推送/升级存储、真机兼容性和性能对比。
