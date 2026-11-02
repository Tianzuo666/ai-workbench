# 单文件学习状态

此文件定义字段，不存储任何人的实际学习状态。真实内容写入用户明确指定的记忆文件的“学习状态”节；不要创建多份 progress、tracks、learning-log 和 session-handoff 正文。

## 核心字段

| 字段 | 内容 |
|---|---|
| revision | 用户确认后的状态写入递增；首次初始化前为 0 |
| status | uninitialized、active、paused、completed |
| learner_profile | 已确认目标、相关基础、时间与项目；不使用示例评分 |
| tracks | 长期轨道的目标、状态、关系与路线摘要；可为空 |
| active_track | 唯一 active 轨道；尚未初始化时为 none |
| current_phase/current_task | 已接受路线的阶段与当前小任务 |
| last_confirmed_checkpoint | 有证据并经用户确认的最近检查点 |
| task_queue | 近期任务及成功条件，不展开数百项计划 |
| evidence | 精炼的确认日期、任务、可观察结果和产物位置 |
| latest_handoff | 最新确认的交接与下一步，不存聊天原文 |
| confirmed_decisions | 路线、笔记位置、必要导师行为约定 |
| mentor_backlog | 用户接受记录的提议及 proposed/trial/adopted/rejected/deferred 状态 |

按实际需要增加字段，保留已有用户组织方式，不为了匹配模板整篇重写记忆。日期、命令结果和路径只记录实际证据。权限不属于状态字段。

## 初次使用

先区分当前已确认资料与导入示例。没有可信进度时，不创建虚构的已完成任务；询问目标、基础、时间和已有项目，提出最小初始化方案。用户明确确认后才将 status 设为 active 并建立轨道。

## 更新

只读相关节；展示目标变更，获确认后重读 revision。状态未变化时更新确认范围、增加 revision，保持轨道、当前任务和交接一致。与用户当下明确决定不符时先澄清，不用文件的新日期替代真实证据。

正式进度和学习笔记各自独立。大型源码/笔记仍保存到用户指定产物位置，记忆只保留必要链接和状态。
