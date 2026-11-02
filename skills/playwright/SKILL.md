---
name: playwright
description: 使用 Playwright CLI 操作真实浏览器，完成导航、交互、数据提取或界面问题复现。用于明确的浏览器任务，不默认生成或运行项目测试。
---

# Playwright CLI 浏览器工作流

优先使用当前环境已经可用的浏览器连接或已安装 CLI；用户明确指定 Playwright CLI 时使用本流程。不把普通页面查看扩大成项目测试、启动开发服务器或编写测试工程。

## 环境与授权

- 先确定目标 URL、已有服务、任务范围和当前 shell。没有服务时说明缺失，不自动启动开发服务器。
- 已有全局 `playwright-cli` 时直接使用。包装脚本 `scripts/playwright_cli.sh` 需要 Bash 与 npx；它通过 npx --yes 启动包，缺少缓存时可能下载依赖，使用前需要已有对应授权。
- 不把 npx 存在等同于 CLI、浏览器或依赖已安装。缺少环境时报告缺项，不自动安装 Node、npm 包或浏览器。
- Bash 中将 `PWCLI` 指向实际安装的技能目录下脚本；不改写 HOME 或 CODEX_HOME。Windows PowerShell 可使用已安装的 `playwright-cli`，不直接执行 Bash 语法。
- 示例命令只说明用法，不构成执行许可。提交表单、上传、支付、发消息或其他外部写入必须在用户明确授权范围内。

## 操作循环

1. 打开获准的页面，取得 snapshot。
2. 用最新 snapshot 的元素 ref 交互，不猜 e1/e12 等标识。
3. 导航、弹窗、切页或 DOM 明显改变后重新 snapshot；旧 ref 失效时先刷新状态。
4. 根据任务需要记录截图或 trace，避免采集无关敏感信息。

已安装 CLI 的示意用法：

```bash
playwright-cli open https://example.com --headed
playwright-cli snapshot
# 从实际 snapshot 选择 ref，再执行获准的交互
playwright-cli screenshot
```

## 资料与产物

- 命令例子见 [cli.md](references/cli.md)，场景例子见 [workflows.md](references/workflows.md)，按需读取。参数以实际安装版本为准。
- 优先明确 CLI 操作；只在确有必要时使用 eval/run-code，不用它们绕过未知页面状态。
- 在本次独立的仓库外临时工作目录运行 CLI，或使用用户指定的产物目录，避免把默认生成的截图/trace 放进业务仓库。用户明确要求项目内交付时才使用相应位置。
- 输入秘密时不在聊天、截图和日志中复述原值。不导出认证状态，除非任务明确需要且位置已确认。
- 报告实际页面、已做步骤、观察结果、产物位置与未覆盖部分。浏览器能打开不代表项目测试或构建通过。

包装脚本和第三方资源保留原样；来源、修改说明与许可证见 NOTICE.txt、LICENSE.txt。
