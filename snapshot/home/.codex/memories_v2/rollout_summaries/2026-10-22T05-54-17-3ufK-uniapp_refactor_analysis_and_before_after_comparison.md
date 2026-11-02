thread_id: 01a1622c-ade0-7612-89c8-079f49269c60
updated_at: 2026-10-22T10:21:21+00:00
rollout_path: \\?\C:\Users\zuoshi\.codex\sessions\2026\10\22\rollout-2026-10-22T13-54-17-01a1622c-ade0-7612-89c8-079f49269c60.jsonl
cwd: \\?\C:\Users\zuoshi\Desktop\hawkeye-southeast-asia
git_branch: fix-img-click

在 C:\Users\zuoshi\Desktop\hawkeye-southeast-asia、分支 fix-img-click 上完成了只读分析，未修改代码、构建或测试。用户确认 uni-app 改造范围为 Android+iOS，保留 H5，暂不合并微信小程序。现状为 Vue 2.6/Vue CLI、Vant/Cube、Vuex/动态权限路由、HTML5+（大量 plus.*）、自建 iOS WebView 导航，以及通过 micro-app 接入独立生产测试 H5。源码约 222 个 Vue 文件、41 个 API JS 文件；普通接口、业务规则和静态资源可较多复用，但页面模板、生命周期、导航、缓存和组件需迁移。重点风险包括后端菜单动态路由需改为页面预注册+权限映射，扫码/定位/文件/推送/下载等原生能力，Google 地图、图片裁剪、拖拽、截图导出、视频，设备 WebSocket 二进制图片协议，以及生产测试子应用的认证、语言、设备参数和部署边界。请求层需保留 Client: global_app_operate、Authorization、country、Accept-TimeZone 和动态服务区；生产测试契约涉及 /productionTest/、token、expire、locale、vmCode，历史资料还记录 ant 仓库的 BUILD_TARGET=micro、src/main.micro.js、src/router/micro.js 和默认路由 /deveiceDetail。建议新建 uni-app Vue 3 工程，业务分批迁移，普通页面优先使用跨端组件，特殊能力按 uni.*、plus.*、renderjs、原生插件或 web-view 分层处理；先做“登录→设备列表→扫码→设备详情”样板，并同时验证裁剪上传、WebSocket、生产测试接入，再决定批量迁移。对比结论：改造后长期可统一三端开发、导航和平台能力边界，但短期迁移量、回归风险和发布链路成本显著增加；性能不会因换框架自动提升，需实测。
