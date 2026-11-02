# GitLab MR 只读 API

依据：[GitLab Merge requests API](https://docs.gitlab.com/api/merge_requests/)。以下路径相对于目标实例 `/api/v4`；实例版本有差异时核对其官方帮助。没有在本次整理中执行联网认证或接口调用。

## 必要读取

| 目的 | GET 路径 |
|---|---|
| MR 元数据 | `/projects/:id/merge_requests/:merge_request_iid` |
| 分页文件差异 | `/projects/:id/merge_requests/:merge_request_iid/diffs` |
| 原始 diff，按实例能力使用 | `/projects/:id/merge_requests/:merge_request_iid/raw_diffs` |

`:id` 为项目数字 ID 或按要求编码的完整路径；iid 为当前项目内 MR 编号。跨项目 MR 要区分源/目标项目，取文件时使用其实际所属项目和 head SHA。

使用现有安全连接携带认证，不能把 PAT 放 URL、命令示例输出或临时文件。不要复制响应中的无关私人资料。

## 完整性

- 遵守分页链接/响应头并读取后续页，不只请求第一页。
- `diff_refs` 和 `changes_count` 可能异步准备，空值不表示没有改动；`changes_count` 也可能为 `1000+`。
- `collapsed` 表示 diff 被折叠，需要通过实例支持的方式补取。
- `too_large` 表示超出限制；相关 diff 可能无法由该接口取得。原始 diff 也不应被当成绕过所有限制的保证。
- 当前 diffs 接口受实例 diff limits 限制。必要时结合获准的指定 SHA 文件或已有 Git 对象，仍无法覆盖时列明缺失。
- 旧 `/changes` 已被官方标记弃用，不作为新流程的默认接口；兼容旧实例时明确限制。

选择当前已具备的 JSON 工具解析，不为解析强制安装 Node 或 Python。认证、分页、完整性和 SHA 必须可追溯，输出可以分块，不能因此丢掉未读部分。
