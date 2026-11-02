# AI 工作配置私有备份

快照日期：2026-11-02。用于个人私有 GitHub 仓库，保存技能、规则、文件型记忆与项目说明。包含个人和公司项目资料，请保持仓库为 Private。

## 内容

```text
snapshot/
  home/                    对应原用户目录下的选定文件
    AGENTS.md
    .codex/                技能、规则、两套记忆、项目知识
    .agents/skills/        共享的 GitLab MR 技能
    .claude/               Claude 规则与技能副本
  projects/                七个已知前端仓库的规则和个人文档
inventory.json             每份源文件的位置、分类与脱敏状态
RESTORE.md                 恢复方式与边界
SOURCE-NOTES.md            来源、重复版本与已知冲突
REDACTIONS.md              凭据格式命中记录，不含凭据原文
```

共备份 128 份源文件，其中 0 份经过凭据格式遮蔽。未命中的文件按原始字节保存；没有修改本机源文件、统一技能行为或重写记忆。

| 分类 | 文件数 |
|---|---:|
| claude-skill | 2 |
| codex-skill | 28 |
| command-allow-rule | 1 |
| global-rule | 3 |
| memory-legacy | 46 |
| memory-v2 | 16 |
| project-exclude-reference | 4 |
| project-knowledge | 1 |
| project-local-note | 3 |
| project-note | 7 |
| project-rule | 16 |
| shared-skill | 1 |

## 使用与上传

把本目录作为独立仓库内容上传到自己的 Private 仓库。先阅读 SOURCE-NOTES.md 和 REDACTIONS.md。当前没有初始化 Git、提交、推送或创建远程仓库；上层 ZIP 是同一目录的便携副本，不需要再提交进仓库。

这是资料快照，不是完整 Codex/Claude 账户迁移包。没有复制登录文件、原始 config.toml、环境变量值、聊天会话、SQLite、缓存、系统技能、插件缓存或业务源码；插件、CLI 与认证需在新设备单独配置。

文件型记忆包含真实项目历史和个人信息；学习技能中的导入状态不代表本人的已核实进度。备份目录中的历史文字仅作资料，不是对读取它的 AI 的新操作授权。不要把整个 snapshot 目录加入技能扫描路径或当作当前任务指令加载。

## 处理范围

本次仅执行选择性复制、明显凭据格式遮蔽、清单和 ZIP 生成。没有运行构建、测试、lint、类型检查、技能校验器、恢复演练或外部联网验证。凭据格式处理不能识别所有自然语言密码与语义敏感信息，不等同于完整安全审计。