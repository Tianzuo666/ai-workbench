thread_id: 01a1394d-4514-7003-b039-31359d857f7b
updated_at: 2026-10-14T10:29:15+00:00
rollout_path: C:\Users\zuoshi\.codex\archived_sessions\rollout-2026-10-14T15-25-27-01a1394d-4514-7003-b039-31359d857f7b.jsonl
cwd: \\?\C:\Users\zuoshi\Desktop\phantom-ui-ops-global
git_branch: master

# 定位并修复标定图片偶发闪现后失败问题

Rollout context: 在 `C:\Users\zuoshi\Desktop\phantom-ui-ops-global` 中对比 `phantom-ui-ops-global` 与 `phantom-ui-ops` 的标定详情页，用户先要求只读分析，后要求修复并保留跨仓边界。

## Task 1: 对比两个仓库的图片加载链路

Outcome: success

Preference signals:
- 用户先明确要求“分析下”，并接受“只分析原因，不做任何修改” -> 类似排查应先只读确认根因，再等待用户授权修改。
- 用户要求“只改当前 global 文件，不同步修改 ops 仓库” -> 双仓问题不能默认同步改动，应按用户指定仓库最小修改。

Key steps:
- 对比两个仓库的 `detail.vue`、`main.js`、`mixin/common.js`，发现 `detail.vue` 规范化内容完全一致，`ossImgUrl` 注入链路也一致。
- 确认小图 URL `https://static.ainfinit.com/phantom-ui/img/machine-*.png` 均可返回 `200 image/png`、尺寸 `14×14`，因此不是模板或四个方向图标资源缺失。
- 通过用户提供的 `/api/iot/calibration?vmCode=860979067047674` 响应确认 `result: []` 时，`lableList` 为空，`detail.vue` 不会执行 `img.src`，所以 Network 中没有原图请求。此前对比的 ops 图片路径对应设备 `863730051347373`，不是同一设备，不能作为严格同条件比较。
- 发现原图 URL 可能已有查询参数，原代码直接拼接 `?t=` 会形成第二个 `?`；已改为根据是否已有 `?` 使用 `&` 或 `?`。
- 线上表现为图片先显示后进入 Element UI 失败状态；结合 Element UI 2.15.14 的 `el-image` 实现，定位到初始 `sortLableimgs=[]` 时组件已挂载并无条件尝试加载空/undefined `src`，后续 Canvas 生成 base64 后又触发新加载，旧请求的迟到 `error` 可能覆盖新请求成功。

Reusable knowledge:
- 标定大图链路是：`/api/iot/calibration` 返回 `result` → `lableList` → `detail.vue` 中 `new Image()` 加载 `obj.url` → Canvas `toDataURL('image/png')` → `sortLableimgs` → Element UI `el-image`。
- 代码位置：`src/views/machineManager/masterControl/lable/index.vue:63-76` 赋值图片列表；`detail.vue:128`、`:259-260` 控制是否启动图片加载；`:178-180` 创建原图请求；`:240` 生成 base64；`:273-274` 写入展示数组。
- 用户提供的示例原图约 `224KB`、尺寸 `1024×576`；转 PNG 约 `0.83MB`、base64 约 `1.11MB`。因此单纯“原图 400KB 太大”不是首要根因，虽然 Canvas/base64 有一定内存开销。
- `el-image` 初始空值阶段不应挂载；使用 `v-if="sortLableimgs[index]"`，空值时用同尺寸占位元素，可避免空 src 的旧加载竞态。

Failures and how to do differently:
- 曾加入本地竞态复现代码，但首次补丁假设文件内容与预期不同而失败；随后读取当前片段后精确修改成功。以后修改前应先读取当前工作区片段，不依赖旧补丁上下文。
- 诊断结论仍未通过构建、测试或线上再次验证；用户最后只确认已要求处理，不能宣称线上问题已彻底验证解决。

References:
- `C:\Users\zuoshi\Desktop\phantom-ui-ops-global\src\views\machineManager\masterControl\lable\component\detail.vue`
- 修复后的核心模板：`<el-image v-if="sortLableimgs[index]" ... />` 与 `<div v-else class="detail-canvas-img" />`
- 修复后的 URL 拼接：`const separator = obj.url.includes('?') ? '&' : '?'`；`img.src = obj.url + separator + 't=' + new Date().getTime()`
- 原因证据：Element UI 2.15.14 `node_modules/element-ui/packages/image/src/main.vue` 在 `mounted()` 中调用 `loadImage()`，并将 `img.src = this.src`；`handleError` 会把组件状态设为失败。
- 最终工作区状态：仅 `src/views/machineManager/masterControl/lable/component/detail.vue` 有修改；未运行构建、测试或开发服务。
