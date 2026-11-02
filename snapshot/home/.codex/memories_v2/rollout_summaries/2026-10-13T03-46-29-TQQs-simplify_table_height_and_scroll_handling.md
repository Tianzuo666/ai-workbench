thread_id: 01a1335e-71d1-7e02-8263-3a5aad6b090c
updated_at: 2026-10-22T09:43:55+00:00
rollout_path: C:\Users\zuoshi\.codex\archived_sessions\rollout-2026-10-16T13-55-04-01a1335e-71d1-7e02-8263-3a5aad6b090c_01a14347-3ae5-7f82-b790-8ab7b3a90b97.jsonl
cwd: \\?\C:\Users\zuoshi\Desktop\phantom-ui-ops
git_branch: opt-JUSTSO-11575

在 phantom-ui（分支 fix-JUSTSO-11519）中，用户先审查并比较 Ops 与主站表格滚动方案，随后要求保留业务完整性但简化实现：删除 tableAdaptiveHeight.js 及其动态滚动缓存/恢复逻辑，改为各页面独立计算表格高度；使用表格实际 getBoundingClientRect().top、分页高度、60px 安全距离和 Math.floor，最小高度按页面设置（订单 300px，其余主要页面 400px），并通过 tableKey++ 在搜索、重置、分页、Tab 切换时重建表格回顶，详情返回依赖现有 keep-alive。用户明确不希望复杂公共 mixin/统一方案，单行 tableKey++ 不额外封装方法，只有父组件需要调用时保留 resetTable。已修改设备、订单、商品库首页及详情商品/层级表格，删除旧 mixin；期间删除了新增的 v-horizontal-ai-scroll（本地组件库实际注册名不匹配），保留 table.scss 固定列样式。用户后来要求在 phantom-ui-ops（分支 opt-JUSTSO-11575）的 src/views/baseSetManager/user/index.vue 按同规则处理：主表改用 :max-height="tableHeight"，mounted 后按实际表格位置和分页高度计算，公式为 Math.max(Math.floor(window.innerHeight - tableTop - paginationHeight - 60), 400)+'px'；搜索、组织筛选、分页直接 tableKey++，删除无效的 formQuery.page=1。该 Ops 文件本已有用户对列宽/操作按钮的未提交改动，修改时保留。未运行构建、测试或 lint；phantom-ui 的 group-keep-alive.js 有既存修改，未主动清理。
