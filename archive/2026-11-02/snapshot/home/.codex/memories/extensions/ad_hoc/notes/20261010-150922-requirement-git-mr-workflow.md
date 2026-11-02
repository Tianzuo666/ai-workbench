# 一般需求的分支、提交与 MR 规范

- 适用范围：所有项目的一般需求；涉及多个独立仓库时，每个仓库分别执行同一套流程。
- 开始前先确认需求所属迭代分支，并拉取更新；需求分支必须从最新的迭代分支切出。
- 需求分支命名：`opt-<需求编号>`。例如 JUSTSO-11419 使用 `opt-JUSTSO-11419`，来源迭代分支为 `release-261021`。
- Commit message 格式：`opt-<需求编号> <需求标题简述>`。例如：`opt-JUSTSO-11419 组织树增加搜索按钮`。
- 提交后将同名需求分支推送到对应仓库的远程。
- MR 的源分支为需求分支，目标分支为该需求对应的迭代分支；多个仓库分别创建各自的 MR。
- MR 标题与 Commit message 保持一致。
- MR Description 使用从需求页面复制的完整文本，格式为：`需求 URL 《需求完整标题》`。例如：`https://devops.aliyun.com/projex/req/JUSTSO-11419# 《开门柜-平台方&运营商-组织详情左侧组织树增加搜索按钮》`。只让 URL 本身成为链接，后面的需求标题保持普通文本，不要把整段包装成 Markdown 链接。
- MR Assignee 选择聂显达，Reviewer 选择李立。
- Merge options 两项都勾选：合并后删除源分支、Squash commits。
