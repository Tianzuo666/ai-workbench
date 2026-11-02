# AI Workbench

个人维护的 AI 工作约束手册、技能库和记忆。用于不同项目和 AI 编程工具，主要以中文维护。仓库含团队与项目资料，按个人私有仓库管理。

## 从哪里开始

| 文件 | 用途 | 读取时机 |
|---|---|---|
| [AGENTS.md](AGENTS.md) | 唯一工具入口，说明加载顺序 | 接入本仓库时 |
| [HANDBOOK.md](HANDBOOK.md) | 通用工作约束的唯一正文 | 开始工作时 |
| [skills/](skills/) | 九个场景技能，每个只维护一个正式版本 | 对应任务触发时 |
| [MEMORY.md](MEMORY.md) | 代码习惯、开发注意事项和踩坑经验 | 编写、修改或排查相关代码时 |
| [SOURCES.md](SOURCES.md) | 设计参考、技能来源和本次整理说明 | 追溯来源或维护时 |
| [archive/](archive/) | 整理前的原始快照 | 需要核对原文时 |

先阅读手册，再按任务选技能。记忆和历史资料按需查阅，不把整个仓库一次性放进上下文。

## 技能目录

| 技能 | 适用任务 | 示例 |
|---|---|---|
| [context-handoff](skills/context-handoff/SKILL.md) | 上下文连续性评估与任务交接 | “检查是否需要新开上下文” |
| [find-skills](skills/find-skills/SKILL.md) | 查找、介绍、按明确授权安装技能 | “找一个适合处理 PDF 的技能，先介绍” |
| [frontend-project-bootstrap](skills/frontend-project-bootstrap/SKILL.md) | 首次整理前端单仓或多仓关系与文档 | “先分析这几个仓库的关系” |
| [learning-mentor](skills/learning-mentor/SKILL.md) | 分步技术学习、复习与确认进度 | “先确认我的基础，再规划学习路线” |
| [playwright](skills/playwright/SKILL.md) | 用 CLI 操作浏览器、提取数据或复现界面问题 | “打开指定页面，检查表单交互” |
| [readonly-code-review](skills/readonly-code-review/SKILL.md) | 逐文件批准范围的只读代码审查 | “先列出准备读取的文件，批准后审查” |
| [yunxiao-reader](skills/yunxiao-reader/SKILL.md) | 通过云效 CLI 只读查看需求、图片和附件 | “读取这个需求，概述改动和冲突” |
| [gitlab-mr-review](skills/gitlab-mr-review/SKILL.md) | 只读获取 GitLab MR 及完整差异并审查 | “审查这个 MR 链接，不修改工作区” |
| [requirement-git-mr-workflow](skills/requirement-git-mr-workflow/SKILL.md) | 按已授权阶段处理需求分支、提交、推送和 MR | “按团队约定准备需求分支，暂不提交” |

示例只是意图表达，不自动授予全部操作权限。`auto:`、`verify:` 等前缀仅在明确采用它们的技能中使用。

## 接入 AI 工具

本仓库先维护内容，不自动修改电脑上的全局规则、安装路径或现有技能。

### 在当前仓库工作

让支持 AGENTS.md 的工具读取根目录入口。Claude Code 的支持取决于版本和设置；遇到不支持的环境时，先显式要求它读取 AGENTS.md。当前不额外维护 CLAUDE.md。

### 在其他项目使用手册

可以在工具的用户级指令或目标项目入口中写入“读取本仓库 AGENTS.md 的绝对路径”，也可以在对话中明确提供入口。引用路径需根据电脑实际位置调整。仅把仓库放在磁盘上，不代表任意项目会自动继承它。

目标项目自己的架构、兼容性、UI 和业务规则仍需按任务读取。不要把本仓库的项目历史当成其他项目的规范。

### 安装技能

`skills/` 是维护目录，不假定工具会自动扫描它。按目标工具支持的方式，把所需的完整技能目录复制或链接到其用户级/项目级技能位置。Codex 当前文档列出用户级 `.agents/skills/` 和项目级 `.agents/skills/`；已有环境也可能从配置的其他位置加载，安装前核实实际路径。

保持一个技能只有一个启用版本，尤其不要再同时启用存档中的 GitLab 三份副本。复制安装后，以本仓库为修改来源，明确同步所需版本；本次没有替换电脑上的已安装版本。

技能的 `SKILL.md` 说明流程，`agents/openai.yaml` 保留已有工具元数据。自动发现能力、认证、CLI 依赖与操作授权是独立事项。具体官方说明见 SOURCES.md。

## 如何维护

- 改工作原则：编辑 HANDBOOK.md。
- 改场景流程：编辑对应技能；细节多时放入该技能的 references。
- 记录或修正可复用的代码习惯、踩坑经验：明确确认后编辑 MEMORY.md，写清场景、做法和适用条件。
- 调整来源或第三方资源：更新 SOURCES.md，保留相应许可证与 NOTICE。
- archive 仅保留迁移原件，不作为正式技能或当前规则继续维护。

学习导师没有启用导入的个人画像或检查点；实际学习状态使用用户为学习任务指定的文件。本仓库 MEMORY.md 专门维护开发经验，不预置空白学习状态。

## 当前整理状态

本次完成内容重组、重复技能归并与入口统一。原始快照和原备份说明保留在 archive/2026-11-02。没有运行技能校验、构建、测试、lint、CLI 联网调用、浏览器流程或安装验证；没有提交、推送，也没有改动已安装技能。
