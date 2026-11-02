# 恢复说明

本包保留原目录关系，默认不自动恢复。先保留目标电脑现有文件，再按需要选择单项复制；不要整目录覆盖现有用户配置。

## 路径映射

| 备份位置 | 恢复目标 |
|---|---|
| snapshot/home/AGENTS.md | 新电脑用户目录下的 AGENTS.md；原件存档，不代表该位置必定全局生效 |
| snapshot/home/.codex/AGENTS.md | 当前 CODEX_HOME 下的 AGENTS.md；默认用户目录下的 .codex |
| snapshot/home/.codex/skills/ | 当前运行环境支持的用户技能目录；先查看新版本的技能加载说明 |
| snapshot/home/.agents/skills/ | 用户目录下的 .agents/skills |
| snapshot/home/.claude/CLAUDE.md | Claude 用户配置目录中的 CLAUDE.md |
| snapshot/home/.claude/skills/ | 原 Claude 技能文件的存档；启用前选择所需版本 |
| snapshot/home/.codex/project-notes/、project-knowledge/ | 用户自己的项目资料目录；更换电脑后修正引用路径 |
| snapshot/projects/<项目名>/ | 对应源码仓库的选定规则与个人文档，不含源码 |

## 按顺序恢复

1. 安装目标环境及需要的插件、Git、Node.js 或云效 CLI。备份没有携带这些依赖，也没有执行安装。
2. 比较目标环境现有的全局规则，再决定使用或合并哪份规则。保留“未经明确批准不得运行项目验证命令”的要求。
3. 选择技能。GitLab MR 技能有三个版本，不要全部同时启用；学习技能含未核实的导入状态，不要当作本人已完成的学习证据。
4. 单独恢复项目规则和资料，修正旧电脑绝对路径。AGENTS.override.md 在同目录优先于 AGENTS.md；AGENTS.personal.md 需要显式引用或配置，不能假设会被自动加载。
5. 文件型记忆仅作资料恢复。memories 和 memories_v2 分开保留，依据新版本的实际支持方式决定如何导入；本包不承诺复制这些文件就会恢复应用的内部记忆索引或聊天记录。
6. rules/default.rules 是本机命令放行记录，先审阅再选择性恢复，不默认导入。git-info-exclude.txt 只是原 Git 排除规则参考；将需要的条目合入目标仓库的 .git/info/exclude，不能把这个文件直接当作项目源码规则提交。
7. 在新设备重新配置认证和环境变量。不要将 PAT、API Key 或登录文件放进此仓库；被遮蔽的字符串只能从可信的原配置或凭据管理器重新配置。

本包未提供一键覆盖脚本，未修改真实记忆目录，也未运行恢复演练。