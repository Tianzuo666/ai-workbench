thread_id: 01a13e50-2950-73f0-a245-515d05a43132
updated_at: 2026-10-19T08:14:58+00:00
rollout_path: C:\Users\zuoshi\.codex\archived_sessions\rollout-2026-10-15T14-46-43-01a13e50-2950-73f0-a245-515d05a43132.jsonl
cwd: \\?\C:\Users\zuoshi\Desktop\hawkeye-southeast-asia
git_branch: fix-img-click

# JUSTSO-11535 跨 App 与海外 PC 的限时促销按设备选择售卖规划商品

Rollout context: 工作涉及两个独立 Vue 项目：`C:\Users\zuoshi\Desktop\hawkeye-southeast-asia`（App）和 `C:\Users\zuoshi\Desktop\phantom-ui-global`（海外 PC）。需求是未选设备时阻止选商品，按单/多设备查询售卖规划商品，支持搜索、分页、去重、跨页选择和编辑回显。项目规则要求先只读核对，未经批准不运行构建、测试、lint 或格式化。

## Task 1: App 限时促销商品选择改造

Outcome: partial

Preference signals:

- 用户要求“现改app项目 没问题就开始”，并要求先理解需求再改；类似任务应先核对需求页、现有页面职责和未提交改动。
- 用户随后追问为何改为 `/salesPlanHasAddGoods`、该页面是否有其他使用方，说明用户重视路由选择的依据和影响范围；以后涉及复用/切换页面应主动解释历史职责、当前引用和动态路由不确定性。

Key steps:

- 核对阿里云需求 JUSTSO-11535：无适用设备时提示“请先选择设备”；单设备查询该设备售卖规划；多设备查询去重合集。
- 在 `opt-JUSTSO-11535` 分支修改 App：新增 `pageVmGoodsByVmIds` API；创建页传 `vmIds`、设备变化时清空商品；商品选择页映射 `goodsId/goodsName/imgUrl/price/totalCapacity` 并用 `goodsId` 保持跨页选择和回显。
- 将入口从 `/salesPlanAddGoods` 恢复为 `/salesPlanHasAddGoods`。静态证据显示后者是专用的售卖规划商品选择页，前者是通用商品库页；历史上限时促销曾使用前者，支持多设备时因缺少多设备接口才临时切换到后者。
- `rg` 检查显示当前源码中 `salesPlanHasAddGoods` 的业务引用只有自身和限时促销创建页；动态后端路由是否仍注册该路径未实际运行验证。

Failures and how to do differently:

- 未运行构建、测试、lint 或浏览器/真机联调，因此不能宣称运行时完成。后续需手测未选设备、单设备、多设备去重、搜索/分页、跨页选择、编辑/复制回显及更换设备清空商品。
- App 的请求拦截器固定 `Client: global_app_operate`，与接口文档示例 `Client: web` 不同；本轮保留 App 既有认证约定，联调时需确认后端是否接受。
- `/salesPlanHasAddGoods` 的静态引用不足以证明动态路由可用；若运行环境 404，应保留通用入口并以 `scene=promotion` 做条件分流，而不是继续全局改通用页面。

Reusable knowledge:

- App 关键文件：`src/api/operation/market.js`、`src/views/markCampaign/createPromotion.vue`、`src/views/salesPlan/salesPlanHasAddGoods.vue`。
- App 全局请求头由 `src/utils/fetch.js` 设置：`Client: global_app_operate`、`Authorization`、`country`、`Accept-TimeZone`。

References:

- API：`POST /api/operation/saleManage/pageVmGoodsByVmIds`
- App 路由：`path: '/salesPlanHasAddGoods'`
- 历史/当前引用检查：`rg -n -S "salesPlanHasAddGoods|SalesPlanHasAddGoods" .`

## Task 2: phantom-ui-global 海外 PC 限时促销改造

Outcome: partial

Preference signals:

- 用户要求“phantom-ui-global 这个海外PC项目也处理下”，并接受先核对项目规则和工作区；以后必须区分 `phantom-ui-global` 与平级 Ops 仓库，避免跨仓照搬。
- 用户强调“只有限时促销吗”，说明需要明确组件实际调用方，而不是只看组件内部保留的历史兼容逻辑。

Key steps:

- 确认 `phantom-ui-global` 当前为 `master`，有 `AGENTS.override.md`，规则禁止自行建分支、提交、推送及运行构建/测试/lint。
- 修改 `src/api/pointsMall/index.js` 新增 `pageVmGoodsByVmIds`。
- 修改 `salesDetail.vue`：取消设备单选限制，传 `selectedVmIds`，设备集合变化时调用 `clearSelectedGoods()`。
- 修改 `handleIndividual.vue`：仅当 `isLimitPromotion=true` 时隐藏商品库筛选并调用新接口；其他调用路径继续使用 `getGoodsPageList`。增加未选设备提示、`vmIds` 属性、商品字段映射和跨页选中保持。
- 补充英文文案 `Please select a device first`。
- 当前源码引用检查显示 `handleIndividual.vue` 唯一现行入口是 `salesDetail.vue`，且固定传入 `isLimitPromotion=true`；历史上曾被已删除的 `operateDetail.vue`（购物发券）使用，当前分支无实际入口。

Failures and how to do differently:

- 未运行构建、测试、lint 或浏览器联调，结果仍需运行时验证。
- PC 请求拦截器固定 `Client: web`，不能照搬 App 的 `global_app_operate`。
- 组件内部仍保留 `isBuySendCoupon`、`showDiscount` 等历史逻辑，但当前没有其他页面引用；修改时应以当前 `rg` 结果为准，不要因遗留代码扩大范围。

Reusable knowledge:

- PC 关键文件：`src/views/marketManager/ActivityManager/salesDetail.vue`、`src/views/marketManager/ActivityManager/components/handleIndividual.vue`、`src/api/pointsMall/index.js`、`src/i18n/en/en.js`。
- 当前 PC 请求头在 `src/utils/fetch.js` 中设置 `Client: web`。

References:

- 当前组件引用：`salesDetail.vue:63`、异步注册 `salesDetail.vue:97`。
- 新接口调用：`handleIndividual.vue` 的 `pageVmGoodsByVmIds` 分支。
- 引用核对命令：`rg -n -S -g '!node_modules' -g '!dist' "handleIndividual|HandleIndividual|SelectPromotion" .`
