thread_id: 01a138fd-e342-79d3-a88a-dbfb22bbe739
updated_at: 2026-10-17T02:46:59+00:00
rollout_path: C:\Users\zuoshi\.codex\archived_sessions\rollout-2026-10-14T13-58-45-01a138fd-e342-79d3-a88a-dbfb22bbe739.jsonl
cwd: \\?\C:\Users\zuoshi\Desktop\phantom-ui-ops
git_branch: release-261021

# JUSTSO-11529 四仓 token key 隔离实施、错误分支/提交修复及 star-lord 部署脚本排查

Rollout context: 工作目录主要为 `C:\Users\zuoshi\Desktop\phantom-ui-ops`，涉及四个独立仓库：`phantom-ui`、`phantom-ui-ops`、`phantom-ui-iot-en`、`star-lord`。用户要求中文、最小改动；明确不处理 `localStorage.clear()`，且构建/测试/部署等有副作用操作需谨慎确认。

## Task 1: JUSTSO-11529 四仓 localStorage token key 隔离

Outcome: success

Preference signals:
- 用户补充“`phantom-ui-iot-en` 这个也放桌面了，纳入考量范围，本次不考虑 `localStorage.clear()`” -> 多仓需求要先完整盘点相关仓库，并严格遵守明确排除项。
- 用户先要求只读分析，之后明确“ok 切分支 开始改动” -> 先审计和确认范围，得到明确批准后再编辑。

Key steps:
- 读取 DevOps 需求 JUSTSO-11529，确认四个项目的 key 映射：`phantom-ui`=`p-Authorization`，Ops=`po-Authorization`，iot-en=`pie-Authorization`，star-lord=`sl-Authorization`。
- 静态审计确认主要 token 读写集中在各仓库 `src/utils/auth.js` 的 `TokenKey/getToken/setToken/removeToken`。
- 在四仓库需求分支 `opt-JUSTSO-11529` 实施：修改四个 `TokenKey`；删除 Ops 与 iot-en 登出流程中重复的 `common.localDelKey('Authorization')`。
- 保留 HTTP 请求头名 `Authorization`，未改微应用传参，也未处理 `localStorage.clear()`。
- Ops 的 `deviceOperation.vue` 仍通过 `getToken()` 和 `microApp.setGlobalData({ token })` 传 token，`vmCode` 仍通过 `microApp.setData()`。

Reusable knowledge:
- 当前四仓 key 映射和关键文件路径已验证；历史浏览器中的旧 `Authorization` 不会自动消失，但不会被当前 `getToken()` 使用。清理后重新登录不会重新生成旧 key。
- iot-en 注册了 Qiankun 子应用 `aliDevice`、`crowdsource`；`aliDevice` 源码不在桌面，仍需部署后联调确认其是否直接依赖旧 key。

References:
- `phantom-ui/src/utils/auth.js`: `const TokenKey = 'p-Authorization'`
- `phantom-ui-ops/src/utils/auth.js`: `const TokenKey = 'po-Authorization'`
- `phantom-ui-iot-en/src/utils/auth.js`: `const TokenKey = 'pie-Authorization'`
- `star-lord/src/utils/auth.js`: `const TokenKey = 'sl-Authorization'`
- HTTP header 静态复核仍为 `config.headers.Authorization = getToken()`。

## Task 2: 需求分支切换及错误 release 提交修复

Outcome: success

Preference signals:
- 用户指出“忘了分支怎么切了？”并要求处理错误 commit -> 需求分支应遵循既有规则，而不是使用默认 `codex/` 命名。
- 既有规则：一般需求从最新需求迭代分支切出，命名 `opt-<需求编号>`；本次四仓最终均为 `opt-JUSTSO-11529`。

Key steps:
- 初始错误地从当前分支创建 `codex/JUSTSO-11529-localstorage-key`，随后核对规则并修正。
- 将改动 stash，刷新远端基线后重建正确分支：`phantom-ui`/Ops 基于最新 `origin/release-261021`，iot-en 基于 `origin/master`，star-lord 基于 `origin/feature_add_project`；恢复改动无冲突。
- 删除四个无独有提交的错误 `codex/...` 本地分支。
- 错误地让需求分支跟踪 `origin/release-261021`，导致提交 `831d821ff` 被普通 push 推到了 PC 运营仓库的 release 分支。随后解除四仓需求分支的错误 upstream。
- 经用户“执行”授权后，先推送 `origin/opt-JUSTSO-11529` 保存提交，再用精确 `--force-with-lease` 将 `origin/release-261021` 从 `831d821ff` 回退到父提交 `abe78806d`，并 fetch 验证远端状态。

Failures and how to do differently:
- 创建需求分支时不能使用 `git switch -c branch origin/base` 后直接依赖其 upstream；这会让普通 `git push` 误推到基线。创建后应立即检查 `git branch -vv`，需求分支必须跟踪自己的 `origin/opt-...`，或先 unset upstream，首次推送明确使用 `git push -u origin opt-...`。
- 改写共享 release 历史前必须先保存需求分支、核对远端当前 SHA、使用 `--force-with-lease=<ref>:<expected-sha>`，并在操作后重新 fetch 验证。

References:
- 正确分支：`opt-JUSTSO-11529`
- 错误提交：`831d821ff3f1ed50560bed1af3efc45da6b617ad`
- 回退目标：`abe78806dd9fe5b44ad0f0597903f023901b7ad1`
- 使用的修复命令核心：`git push --force-with-lease=refs/heads/release-261021:831d821ff origin abe78806d...:refs/heads/release-261021`
- 最终验证：`origin/release-261021=abe78806d`，`origin/opt-JUSTSO-11529=831d821ff`，PC 运营仓库工作区干净。

## Task 3: star-lord `scp100.bat` 部署脚本说明与中止

Outcome: partial

Preference signals:
- 用户询问“看看这个怎么用”，助手先只读解释，没有直接执行；部署脚本涉及构建和上传，应先说明副作用并等待明确授权。
- 用户遇到 SSH 主机指纹提示后说“现在无法输入了”，随后明确“停止” -> 遇到交互式终端卡住时应立即停止，不要代替用户确认服务器指纹。

Reusable knowledge:
- `C:\Users\zuoshi\Desktop\star-lord\scp100.bat` 当前有效命令为：`call npm run build`，随后使用 `~\.ssh\id_rsa_100` 将 `./dist/*` 上传到 `root@test.ainfinit.com:/root/service/Hiron`。注释中的 `web01.ainfinit.com` 和旧测试路径不执行。
- `package.json` 的 `build` 是 `vue-cli-service build`；本地已确认 OpenSSH `scp.exe` 和私钥存在，但当时没有 `dist`。
- 脚本不主动切换目录，必须从 `star-lord` 项目根目录运行；也没有检查构建失败，可能继续上传旧 dist。SCP 不会删除服务器遗留文件。
- 首次连接 `test.ainfinit.com` 会等待 ED25519 指纹确认；无法确认时不要输入 `yes`。本轮用户要求停止，未继续操作。

References:
- `scp100.bat` 有效行：`call npm run build`、`set key=~\.ssh\id_rsa_100`、`scp -i %key% -r ./dist/* root@test.ainfinit.com:/root/service/Hiron`
- 主机指纹提示：`SHA256:hN+3UjNtjpN8wpxWMRmf7lpSzF0IiQL2zsTEHTuK8Ok`
- 用户最终指令：“停止”；应按 `Ctrl+C` 取消等待中的 SSH/SCP。
