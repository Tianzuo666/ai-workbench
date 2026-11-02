thread_id: 01a11ab2-feec-7e21-9984-84f53782ee60
updated_at: 2026-10-08T09:02:56+00:00
rollout_path: C:\Users\zuoshi\.codex\archived_sessions\rollout-2026-10-08T16-48-25-01a11ab2-feec-7e21-9984-84f53782ee60.jsonl
cwd: \\?\C:\Users\zuoshi\Desktop\hawkeye-southeast-asia
git_branch: master

# 梳理 H5 状态码映射 OSS/R2 加载与 CDN 缓存行为

Rollout context: 用户要求分析 `C:\Users\zuoshi\Desktop\ant\src\main.js` 中状态码映射从 OSS 获取的所有触发场景，并进一步追问普通 H5、国内 PC 微应用、时间戳参数、CDN、`CF-Cache-Status` 和缓存 TTL。全程主要进行代码阅读与资料查询，未修改代码或运行构建测试。

## Task 1: 状态码映射加载触发链路

Outcome: success

Preference signals:

- 用户连续追问“普通 H5 是否加载”“国内 PC 是否加载”“`t=${timestamp}` 是否导致没走 CDN”以及“那怎么做”，说明类似问题应同时覆盖运行模式、实际请求 URL、缓存效果和可执行改法，而不只回答入口调用位置。

Key steps:

- 检查 `main.js`、`main.micro.js`、`statusCodeOSS.js`、`LangSwitch.vue`、Vuex `statusCode` 模块和 webpack entry 配置。
- 确认普通构建入口由 `build/webpack.base.conf.js` 根据 `BUILD_TARGET` 决定：普通模式使用 `src/main.js`，微应用模式使用 `src/main.micro.js`。
- 确认普通 H5 启动时在创建 Vue 实例前无条件调用 `preloadStatusCodeMap(getLocale())`，因此刷新/重新打开应用都会请求一次，与登录、Token、路由或是否进入业务页面无关。
- 确认微应用入口启动时也无条件调用 `preloadStatusCodeMap(i18n.locale)`；主应用后续传入 `data.locale` 时，`main.micro.js` 还会再次调用。
- 确认 `LangSwitch.vue` 切换语言成功后调用 `preloadStatusCodeMap(newLocale)`，但全项目搜索只发现组件定义，未发现其被引用，因此当前实际生效性未确认。
- 确认 Vuex action `statusCode/loadStatusCodeMap` 会重新请求，但源码中未找到实际 dispatch。

Reusable knowledge:

- 请求 URL 为 `https://r2-aus-phantom.shopaicloud.com/static/ant/{locale}.json?t={timestamp}`；支持 `zh/en/th/vi/ja/ko`，非法语言回退为 `zh`。
- `preloadStatusCodeMap` 会在当前语言匹配时先清空旧映射；异步返回后仅当当前语言仍匹配时才写入 Vuex，避免旧语言响应覆盖新语言。
- `fetchStatusCodeMapFromOSS` 使用 Axios 10 秒超时；失败返回 `null` 并记录错误。Vuex 状态码映射由 `statusCode/SET_STATUS_MAP` 统一格式化数组或对象。
- 普通 H5 和国内 PC 微应用都会加载；决定因素是构建入口，不是国内/海外或是否登录。

Failures and how to do differently:

- 一次搜索使用了 Windows 不支持的通配路径 `webpack*.js`，产生 OS error 123；后续改为明确文件路径后成功。未来 PowerShell 检索应使用具体路径或 `-Filter`。

References:

- `C:\Users\zuoshi\Desktop\ant\src\main.js:245`：`preloadStatusCodeMap(getLocale())`
- `C:\Users\zuoshi\Desktop\ant\src\main.micro.js:95,151`：语言数据变化和微应用启动时加载
- `C:\Users\zuoshi\Desktop\ant\src\utils\statusCodeOSS.js`
- `C:\Users\zuoshi\Desktop\ant\src\views\common\LangSwitch.vue:62`
- `C:\Users\zuoshi\Desktop\ant\build\webpack.base.conf.js:29-30`

## Task 2: 时间戳、CDN 缓存和 TTL 解释

Outcome: partial

Preference signals:

- 用户明确指出“问题是 `t=${timestamp}` 增加了这个导致没走 CDN”，说明类似缓存问题应区分“请求经过 CDN”与“命中 CDN 缓存”，并直接围绕用户指出的 URL 变化分析。
- 用户要求解释 CDN、`cf-cache-status` 和缓存时间，说明需要用项目实际 URL 和 Network 响应头举例，而不是只给抽象定义。

Key steps:

- 说明每次变化的 `?t=...` 会形成不同缓存键；即使请求经过 Cloudflare，也几乎无法复用前一个 URL 的缓存，通常表现为持续 `MISS`。
- 说明 `CF-Cache-Status` 常见含义：`HIT` 为命中缓存，`MISS` 为可缓存但当前节点没有缓存，`DYNAMIC` 为请求阶段不可缓存，`BYPASS` 为响应阶段被禁止缓存，另有 `EXPIRED`、`REVALIDATED`、`STALE`、`UPDATING` 等状态。
- 说明缓存 TTL 需要区分 Cloudflare 边缘缓存和浏览器缓存；Cloudflare Cache Rule 的 Edge Cache TTL 可能覆盖源站的 CDN 缓存控制，浏览器缓存可由 `Cache-Control` 或 Browser Cache TTL 控制。
- 资料查询确认 Cloudflare 默认缓存键包含完整 URL及查询字符串，因此时间戳确实会造成缓存键不断变化。

Reusable knowledge:

- 当前代码同时发送请求头 `Cache-Control: no-cache, no-store, must-revalidate`、`Pragma: no-cache`、`Expires: 0`。这些是请求头，不能简单等同于源站响应头；真正的 Cloudflare 缓存结果还应检查响应头和 Cache Rules。
- 更稳妥的缓存方案是固定 URL，例如 `.../${currentLocale}.json`，并由 Cloudflare Cache Rule/源站响应头设置 Edge TTL；状态文件更新时通过 purge 或仅在版本变化时使用稳定版本参数。
- 建议验证：连续请求同一固定 URL，在浏览器 DevTools Network 查看响应头，期望第一次可能为 `CF-Cache-Status: MISS`，后续在 TTL 内为 `HIT`，并观察 `Age` 是否增长。测试时注意 DevTools 的 Disable cache 会影响结果。
- 项目 `buildVersion` 当前用于静态资源发布路径（`config/index.js`），没有注入前端 JS，不能直接假设可用作状态码 URL 版本参数。

Failures and how to do differently:

- 本次没有实际修改 `statusCodeOSS.js`、没有验证真实 CDN 响应，也没有确认 Cloudflare Cache Rule，因此“删除时间戳后一定 HIT”只能作为方案，不能当作已验证结果。
- 后续应先查看真实响应头（尤其是响应侧 `Cache-Control`、`CDN-Cache-Control`、`Cloudflare-CDN-Cache-Control`、`CF-Cache-Status`），再决定只改前端 URL、改源站响应头，还是配置 Cloudflare Cache Rule。

References:

- 当前代码片段：`const timestamp = new Date().getTime()` 与 ``${currentLocale}.json?t=${timestamp}``
- Cloudflare 文档：`https://developers.cloudflare.com/cache/concepts/cache-responses/`
- Cloudflare 文档：`https://developers.cloudflare.com/cache/concepts/cdn-cache-control/`
- Cloudflare 文档：`https://developers.cloudflare.com/cache/how-to/cache-keys/`
- 建议目标 URL：`https://r2-aus-phantom.shopaicloud.com/static/ant/{locale}.json`
