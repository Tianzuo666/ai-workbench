thread_id: 01a0ec6a-67ec-7172-a06f-ebf15ebe4771
updated_at: 2026-10-08T02:02:10+00:00
rollout_path: C:\Users\zuoshi\.codex\archived_sessions\rollout-2026-09-29T17-06-31-01a0ec6a-67ec-7172-a06f-ebf15ebe4771.jsonl
cwd: \\?\C:\Users\zuoshi\Desktop\hawkeye-southeast-asia
git_branch: master

# 售卖分析接口梳理并修复设备排行显示问题

Rollout context: 工作目录为 `C:\Users\zuoshi\Desktop\hawkeye-southeast-asia`，Vue 2 海外 App 项目。用户先要求只整理售卖分析相关接口并输出为项目根目录 Markdown，随后反馈自定义日期范围下设备排行接口有数据但页面不显示。

## Task 1: 梳理售卖分析接口并输出 Markdown

Outcome: success

Preference signals:

- 用户明确纠正“**不改代码 只整理涉及的接口**”，因此类似需求应先做静态梳理，不修改业务代码。
- 用户要求“**在首页输出为md文件**”，最终在项目根目录生成 `售卖分析接口整理.md`。

Key steps:

- 确认首页顶部“更多数据”跳转 `/salesAnalysis`。
- 梳理首页概览、售卖分析主页、排行详情、导出及设备页面相关接口。
- 补充发现设备列表/详情调用的 `/api/analysis/orderDevice/findDeviceSales`，以及当前注释未启用的 `/api/analysis/order/post/salesVolume`。
- 文档写入并验证文件存在，大小约 7359 字节。

Reusable knowledge:

- 首页顶部销售额/订单量使用 `POST /api/analysis/order/post/total`，今日/昨日为 `domain=hour`，本月/上月为 `domain=day`。
- 售卖分析主要接口包括 `/api/analysis/order/post/report`、`/api/analysis/orderDevice/post/total`、`/api/analysis/orderGoods/total`、部门/点位排行接口、品类排行及详情趋势接口。
- 前端使用本地时区日边界毫秒时间戳，并通过 `Accept-TimeZone` 请求头传递浏览器时区；多日查询的 `domain=day` 是时区问题重点。
- 导出统一调用 `POST /api/task/taskRecord/submit?exportType=...`，复用查询时间参数。

References:

- `售卖分析接口整理.md`
- `src/views/home/operatorHome.vue`
- `src/views/salesAnalysis/index.vue`
- `src/views/salesAnalysis/saleAnalyseDetail/index.vue`
- `src/views/salesAnalysis/components/timeSelect/index.vue`
- `src/api/salesAnalysis/index.js`
- `src/api/dashboard/index.js`
- `src/api/statistics/index.js`

## Task 2: 修复自定义日期设备排行不显示

Outcome: partial

Preference signals:

- 用户描述的是具体复现路径：“点击本月，再选择一个日期范围后，查看设备排行，结果接口有数据 页面不显示”，未来应优先沿该状态链检查父页面数据组装，而非先改表格组件。
- 用户已有 `src/main.js` 的 VConsole 修改，代理明确要求不要触碰现有工作区改动；类似任务应保留用户已有改动。

Key steps:

- 定位到 `src/views/salesAnalysis/index.vue` 的 `machineGetData`：此前只有设备名称接口成功后才赋值 `rankTableData`，名称接口失败或 ID 不匹配会导致页面空白。
- 修改为排行接口返回后立即写入表格数据，再异步调用 `getMachineNames` 补全名称；兼容数字/字符串 ID，并在名称缺失时保留 `deviceName`、`goodName` 或 `--`。
- `tableList/index.vue` 本身在收到非空 `data` 后可正常渲染，因此未修改。
- `git diff --check` 通过；未运行构建、lint 或测试，需在海外 App 手动验证完整路径。

Failures and how to do differently:

- 修复尚未得到用户现场确认，属于未完全验证；不能宣称已在真实 App 验证成功。
- 设备名称补全请求仍可能异步返回较旧列表，后续若出现分页/快速切换串数据，应进一步绑定请求上下文或在查询变化时取消/忽略旧响应。

References:

- `src/views/salesAnalysis/index.vue:312` 附近的 `machineGetData`。
- 原问题链路：`machineItem` 返回 `res.data.rows` → `getMachineNames` → `rankTableData`。
- 现有改动保留：`src/main.js` 启用 VConsole。
