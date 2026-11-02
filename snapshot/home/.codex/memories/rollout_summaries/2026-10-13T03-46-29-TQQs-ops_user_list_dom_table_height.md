thread_id: 01a1335e-71d1-7e02-8263-3a5aad6b090c
updated_at: 2026-10-22T09:43:55+00:00
rollout_path: C:\Users\zuoshi\.codex\archived_sessions\rollout-2026-10-16T13-55-04-01a1335e-71d1-7e02-8263-3a5aad6b090c_01a14347-3ae5-7f82-b790-8ab7b3a90b97.jsonl
cwd: \\?\C:\Users\zuoshi\Desktop\phantom-ui-ops
git_branch: opt-JUSTSO-11575

# 用户管理列表页按实际 DOM 位置计算表格高度

Rollout context: 工作目录为 `C:\Users\zuoshi\Desktop\phantom-ui-ops`，目标文件为 `src/views/baseSetManager/user/index.vue`。用户要求沿用此前确认的页面级高度方案，避免公共 mixin，并保留已有未提交的列宽和操作按钮布局修改。

## Task 1: 用户管理列表页高度与分页滚动调整

Outcome: success

Preference signals:

- 用户要求“按这个规则”处理，随后明确使用 `max-height`、`Math.floor()` 取整、60px 安全距离 -> 类似列表页高度计算应优先使用真实 DOM 位置，不使用固定顶部偏移，并遵循用户指定的精确公式。
- 用户要求给 `tableTop` 和分页高度添加中文用途型注释 -> 保留简短、解释设计意图的注释。
- 用户指出 `this.formQuery.page = 1` 没有实际用途并要求精简 -> 只保留当前页面请求真正使用的状态字段，避免照搬全局 mixin 的无关逻辑。
- 用户偏好单行状态变更直接写 `this.tableKey++`，不额外封装方法；只有需要父组件调用时才保留方法接口。

Key steps:

- 保留用户已有的手机号列宽、微信关联列宽、操作列宽及操作按钮 grid 布局改动。
- 主表增加 `:max-height="tableHeight"`，使用已有 `ref="tableDom"`。
- 在 `mounted` + `$nextTick` 中调用 `calculateTableHeight()`。
- 高度公式调整为：`Math.max(Math.floor(window.innerHeight - tableTop - paginationHeight - 60), 400) + 'px'`。
- 查询时递增已有全局 mixin 提供的 `tableKey`，分页改为调用 `handlePagination()`，其中递增 `tableKey` 后执行 `getList()`。
- 删除用户管理页无实际用途的 `this.formQuery.page = 1`。

Failures and how to do differently:

- 首次补丁因文件已存在上一轮修改而匹配失败；后续先读取当前文件和 diff，再只补缺失内容，避免覆盖用户改动。
- 未运行构建、测试或 lint；结果仅完成静态代码修改。

Reusable knowledge:

- `src/mixin/index.js` 已提供 `tableKey`、`listQuery`、`formQuery` 和分页相关通用状态；用户管理页的 `getList()` 实际只使用 `listQuery`。
- 用户管理页主表 ref 是 `tableDom`，分页组件使用 `.pagination-container`，分页回调可改为页面自己的 `handlePagination`。
- 目标实现的稳定公式是：`Math.max(Math.floor(window.innerHeight - tableTop - paginationHeight - 60), 400) + 'px'`。

References:

- 文件：`C:\Users\zuoshi\Desktop\phantom-ui-ops\src\views\baseSetManager\user\index.vue`
- 表格：`:max-height="tableHeight"`
- 计算方法：`calculateTableHeight()`
- 关键代码：`const tableTop = table.$el.getBoundingClientRect().top`；`const paginationHeight = pagination ? pagination.getBoundingClientRect().height : 0`
- 查询/分页：`this.tableKey++`、`handlePagination()`
- 验证状态：仅检查 diff、固定字符串检索和 git status，未执行构建/测试/lint。
