# 项目 AI 开发约束

## 小程序页面开发规范（强制）

- 新增小程序页面或组件时，必须在 WXML 文件顶部添加文件头注释，并按实际内容填写 `description`、`fileName`、`author` 和 `date`：

```xml
<!--
 * @description: 页面或组件说明
 * @fileName: 文件名
 * @author: userName
 * @date: YYYY-MM-DD
!-->
```

- 新增或修改小程序页面前，必须先完整阅读 `docs/miniprogram-ui-spec.md`。
- 开发前优先从规范文档中选择一个同类型的参考页面；文档未覆盖时再检索相邻业务模块。
- 规范用于选择公共能力，组件 API 以原组件源码为准，同类型参考页面用于确定视觉和交互，`packageDev/pages/uiSpec/uiSpec` 只用于参考通用状态流程。
- 优先复用项目已有的高频公共组件、全局样式、behavior 和公共方法；已有能力可以满足时不得重复实现。
- 列表页优先复用 `behaviors/pageLoading.js` 处理下拉刷新和触底分页，并处理首次加载与空数据状态。
- 表单提交必须处理校验、提交 loading 和防重复提交；请求无论成功或失败都必须恢复提交状态。
- 底部操作区优先使用项目现有的 `fixed-bottom`、`g-ft-btn-box` 和 `seize-seat-ft` 样式。
- 组件源码、事件、参数或推荐示例发生变化时，必须同步更新 `docs/miniprogram-ui-spec.md`。
- 规范文档与源码不一致时以源码为准，修正文档后再继续生成页面。
