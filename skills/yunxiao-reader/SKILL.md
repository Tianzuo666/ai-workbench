---
name: yunxiao-reader
description: 通过阿里云效 CLI 只读搜索、查看和概述 Projex 需求工作项，包括字段、正文、内嵌图片与附件；不创建、修改、删除或上传任何云效数据。
---

# 云效需求只读

只通过官方 `aliyun devops` CLI 读取云效 Projex 需求。默认不使用浏览器，不执行任何会改变云效数据的命令。

## 连接

使用当前 Windows 用户的环境变量：

- `ALIBABA_CLOUD_YUNXIAO_ACCESS_TOKEN`：当前用户自己的 PAT。
- `ALIBABA_CLOUD_YUNXIAO_ORGANIZATION_ID`：中心版组织 ID；未知时可通过组织列表只读发现。

不得输出、记录、截图或要求用户在聊天中发送 PAT。若用户环境变量尚未进入当前进程，只将用户级变量加载到本次进程，不显示其值。

确认 `aliyun` 和 `aliyun devops version` 可用。缺少组件时说明缺失项，不自动安装。组织 ID 未配置时：

1. 使用 `aliyun devops base-list-organizations` 获取当前 PAT 加入的组织。
2. 只有一个组织时在本次进程中使用其 ID；有多个时让用户确认。
3. 不主动把组织 ID 持久化到系统环境变量。

首次连接用 `base-get-user-by-token` 做只读认证验证。出现 `401 InvalidToken` 时按令牌无效或过期处理，不误判为项目权限不足。

## 读取需求

- 已知展示编号或内部 ID 时，优先使用 `projex-get-workitem --id <编号或ID>`。
- 仅有关键词时，先确定项目，再使用 `projex-search-workitems --category Req --space-id <项目ID>` 搜索；不要使用不存在的 `projex-list-workitems`。
- 读取并核对标题、编号、项目、迭代、状态、优先级、负责人、完整正文及正文格式。
- 富文本正文同时解析 `htmlValue` 或 `jsonMLValue`，不要只根据截断文本概述。
- 使用 `projex-list-workitem-attachments --id <内部ID>` 检查独立附件。
- 从正文提取每个 `fileIdentifier`，使用 `projex-get-workitem-file --workitem-id <内部ID> --id <文件ID>` 获取内嵌图片信息。
- 需要理解原型时，将返回的临时文件地址下载到系统临时目录并查看，完成后删除临时文件；不要在回复中暴露临时签名地址。
- 图片、附件或字段无法读取时明确说明限制，不能声称已完整核对。

## 概述要求

用户要求概述、评审或提炼改动时，仅总结本期真实新增、修改、删除和约束：

- 区分需求正文、原型图和推断。
- 指出正文与原型、不同端或不同页面之间的口径冲突。
- 不补写需求未明确的接口、字段、权限或异常处理。
- 给出需求详情链接，并说明图片和附件是否已核对。

## 只读边界

禁止执行任何创建、更新、删除、状态流转、评论、上传附件、注册成员或其他写入命令。即使用户要求写入，也要说明此 Skill 仅支持读取并停止，不调用其他写入流程。
