# 来源与整理说明

整理日期：2026-11-02。本仓库用于个人维护，不声明所有技能均为原创，也未擅自给全部内容增加统一开源许可证。

## 文档设计参考

| 来源 | 采用的原则 |
|---|---|
| [OpenAI：Rethinking skills and prompts](https://developers.openai.com/blog/rethinking-skills-and-prompts-for-gpt-6-astra) | 精简长期指令，按任务读取资料，技能描述避免过宽，明确执行边界 |
| [OpenAI：Build skills](https://learn.chatgpt.com/docs/build-skills) | 每个技能使用 SKILL.md，名称和描述负责发现，参考资料按需加载 |
| [OpenAI：AGENTS.md](https://learn.chatgpt.com/docs/agent-configuration/agents-md) | 工具入口、作用范围及覆盖文件的加载方式 |
| [Anthropic：Claude Code memory](https://code.claude.com/docs/en/memory) | 长期指令与记忆分开，写具体且可判断的规则，减少重复与冲突 |
| [Anthropic：Context engineering](https://www.anthropic.com/research/effective-context-engineering-for-ai-agents) | 保留高价值上下文，按需检索，利用精炼笔记保持任务连续性 |
| [Diátaxis](https://diataxis.fr/) | 根据使用目的区分入口说明、规范参考、操作指南和解释 |

HANDBOOK.md、MEMORY.md 和目录布局是本仓库的维护约定，并非所有工具都会自动识别的标准。这里的参考建议不替代用户自己的验证授权要求。

## 技能来源

| 技能 | 已知来源 | 本次处理 |
|---|---|---|
| context-handoff | 安装记录指向 [HSSHSSHSSH/my_skills](https://github.com/HSSHSSHSSH/my_skills)；未锁定上游提交 | 保留连续性判断与交接边界，整理为中文 |
| find-skills | 同一导入仓库；其流程使用 [Vercel Skills CLI](https://github.com/vercel-labs/skills) | 收窄触发范围，区分查询与安装，移除默认全局安装参数 |
| frontend-project-bootstrap | 本地需求驱动的前端仓库整理技能 | 保留先分析后确认写入，以及验证授权要求 |
| learning-mentor | 上述导入仓库；包含原作者/导入学习状态 | 保留教学和命令协议，状态使用用户为学习任务指定的记忆文件；通用路线仅作参考 |
| playwright | 原 NOTICE 指向 [Microsoft playwright-cli](https://github.com/microsoft/playwright-cli)；本地已有包装脚本与参考资料 | 保留 LICENSE、NOTICE、脚本和资源，整理入口与路径说明；脚本未修改或执行 |
| readonly-code-review | 上述导入仓库 | 保留精确文件范围批准及当前消息 auto 修改边界，明确验证需另行授权 |
| yunxiao-reader | 从本地 ZIP 导入的云效技能后续精简改造；原 ZIP 上游未核实 | 保留官方 aliyun devops CLI 只读流程，不携带凭据 |
| gitlab-mr-review | 本地共享技能和两份 Claude 副本；最初作者与上游版本待核实 | 合并为单个只读流程，取消默认 checkout/pull、证书跳过和报告入业务仓库 |
| requirement-git-mr-workflow | 旧记忆目录中生成的个人工作流 | 整理为独立技能；按已授权阶段停止，团队人员配置由具体任务提供；将原显式调用限制保留为 openai.yaml 中的策略 |

上游当前网页与本地导入版本不一定相同。来源尚未核实时保留这一说明，不补造版本号、作者或授权结论。第三方 LICENSE 和 NOTICE 只适用于其说明的文件；未来公开分发前逐项核实。

## 外部流程依据

- GitLab MR：参考 [Merge requests API](https://docs.gitlab.com/api/merge_requests/)，使用分页 diff，处理 `collapsed`、`too_large` 和数据未准备完成的情况，不把部分差异说成完整审查。
- 云效 CLI：当前流程来自本地已使用过的官方 CLI 记录。本次未连接云效或重新验证各版本命令；使用时按实际 CLI 帮助和服务版本核实，不虚构命令。
- Playwright：CLI 文档和脚本来自原技能。本次未安装或联网验证 CLI；参数以实际已安装版本为准。

## 从备份迁移的内容

原备份保存在 [archive/2026-11-02](archive/2026-11-02/README.md)，包含 snapshot、旧 README、恢复说明、文件清单和脱敏记录。正式入口只使用根目录 AGENTS.md。

- 工作约束提炼至 HANDBOOK.md，去除重复文字和固定电脑路径。
- 九类技能各保留一份正式版本。旧副本保留在存档，不静默覆盖电脑上已安装的版本。
- MEMORY.md 从旧记忆中的用户纠正、可复用实现和失败经验提炼代码习惯与开发注意事项；不维护任务流水、仓库台账或个人生活记录。
- 原始学习画像、AI-028/AI-029 等导入检查点没有转换成本人的正式学习进度。
- 健康、产品调研和设备支持资料仅作历史背景，不写入通用工作约束。

本次只进行文档与技能内容整理及人工阅读，没有运行验证器或执行技能。CLI、浏览器、GitLab、云效以及不同 AI 工具的安装/发现行为仍待在获准的环境中确认。
