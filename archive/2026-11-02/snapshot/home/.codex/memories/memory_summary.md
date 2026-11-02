v1

## User Profile

Chinese-speaking front-end developer working across independent Vue 2, mini-program, micro-frontend, overseas App/H5, and a personal Android offline accounting app. Uses Codex for evidence-backed diagnosis, narrow implementation, project-local knowledge, and concise Chinese business/general-support writing. Values explicit facts, inferences, unknowns, checkout boundaries, and honest validation status. `phantom-ui` is the 运营项目; `phantom-ui-ops` is the 运维项目. [ad-hoc note] The user is also building a personal Android offline accounting-and-records app.

## User preferences

- Start “分析下”, audit, bootstrap, or “不改代码” work read-only: report evidence, scope, risks, facts/inferences/unknowns, and a plan before edits or runtime commands.
- Do not run install/build/test/lint/typecheck/format/dev-server, commit/push/reset, or overwrite unrelated changes without explicit approval.
- For large/global/multi-module changes or unclear acceptance, state impact/approach and ask before editing. [ad-hoc note]
- Preserve independent multi-repo/release boundaries and explicitly deferred work; explain callers, conditional scope, and dynamic-route uncertainty before changing shared pages/routes.
- Prefer minimal page-local UI logic; add concise Chinese design-intent comments to changed methods/complex logic. New pages/components use the project-standard header without inventing author/date. [ad-hoc note]
- Keep non-deliverable reports, analysis, plans, and local Codex knowledge outside repositories; put easy-to-view reports/checklists on `C:\Users\zuoshi\Desktop` unless a specific location or versioned deliverable overrides it. [ad-hoc note]
- Ordinary requirements use `opt-<需求编号>` from the current iteration branch; commit/MR title is `opt-<需求编号> <需求标题简述>` and the MR targets that iteration branch. [ad-hoc note]
- When an interface shape is unknown, honor “先空着，写了之后我请求下给你发”: add only the agreed entry/UI and wait for real payloads. Preserve explicit page boundaries such as “只做设备详情页”.
- Follow corrected conventions: use `v-hasPermi`, OSS rather than a checked-in binary template, clear blocked-upload feedback, and exact confirmed field contracts such as `phoneCallingCode`.
- For Skills, distinguish “介绍下” from explicit installation; use a specified read-only skill/CLI rather than browser exploration, and keep Yunxiao read-only without separate authorization.
- For short Chinese questions, lead with the conclusion; for medical, appliance/gas, SSH, or security issues include verification and stop/escalate triggers.

## General Tips

- `git diff --check` is static only; always report unrun build/test/manual verification. Re-read the current target before reapplying a patch to a restored worktree.
- In PowerShell use explicit paths and `rg -F`; split fragile fixed-string searches. Never store credentials; narrow searches to avoid sensitive configuration.
- Before push, verify upstream. For shared-branch correction preserve commits, verify remote SHA, use exact `--force-with-lease`, then fetch/recheck.
- Canvas/download changes need browser checks for CORS images, fonts, empty/error payloads, rapid request changes, and downloaded output.
- Use `skills/requirement-git-mr-workflow/SKILL.md` only after explicit side-effect authorization. [ad-hoc note]
- Recheck official docs for current OpenAI/Codex product or model questions. For Clash TUN loopback bypasses, inspect active mode/profile, prepend `localhost`/`127.0.0.1`/`::1` DIRECT rules in the global extension script, verify ordering, then request a real local URL.

## What's in Memory

### C:\Users\zuoshi\Documents\Codex\2026-10-30\g-p-t

#### 2026-10-30

- Clash Verge Rev localhost DIRECT routing: Clash Verge Rev, mihomo, TUN, localhost, 127.0.0.1, ::1, DIRECT, profiles\Script.js
  - desc: Persistent global extension-script bypass for local services routed through TUN.
  - learnings: Windows proxy exceptions are insufficient with active TUN; generated config/UI was verified, not a browser request.
- GPT-6.1 Sol model comparison: GPT-6.1 Sol, Astra, Terra, Luna, OpenAI Docs, latest-model
  - desc: Official-source model positioning and selection guidance.
  - learnings: Preserve requested model names and recheck current docs.

### C:\Users\zuoshi\Desktop\phantom-ui-ops

#### 2026-10-30

- JUSTSO-11595 device-detail scan-code template preview and QR UI: JUSTSO-11595, scanCodeRecordPreview, basicinfo.vue, scanCodeTemplatePreview.vue, Canvas, FileSaver.saveAs
  - desc: Device-detail-only template tab, double-door preview, 3x PNG, and QR/carousel UI.
  - learnings: Parse real JSON strings, use `scanCodePreviewRequestId`, and validate CORS/fonts/browser behavior before completion.
- JUSTSO-11627 batch hierarchy migration and rebase cleanup: BatchDeptMove.vue, checkBatchMove, batchMove, pageForSearch, dept:showBatchMove, rebase --onto, --force-with-lease
  - desc: Batch upload/check/execute UI, Excel rule inventory, and local branch-history rewrite.
  - learnings: Front end owns extension/first-sheet/≤1000 interaction; backend owns business validation. Confirm remote SHA before a lease push.

### C:\Users\zuoshi\Desktop\phantom-ui-ops-global / phantom-ui-global

#### 2026-10-30

- PIECE-219 三仓联系方式国际化: PIECE-219, phoneCallingCode, headPhone, countryCode.attributes.phoneCode, libphonenumber-js/max
  - desc: Email/international-phone contact forms across ops, global, and hawkeye checkouts.
  - learnings: China is phone-only and submits empty calling code; all three implementations need runtime validation.
- dataScope=2 部门路径过滤和 PIECE-208 排查: deptPathDisplay, Vue.filter, operDeptFullName, operFullName, GitLab MR44
  - desc: Shared full-path display filtering and the remaining direct-display inventory.
  - learnings: Filter only full path/tooltip/popover, not identity, controls, routes, or request parameters.

### C:\Users\zuoshi\Documents\Codex\2026-10-05\zui-j

#### 2026-10-30

- GitHub Skills, Peach, and DeepInfer research: HSSHSSHSSH/my_skills, allow_implicit_invocation, peach-mitao/peach, DeepInfer, DEEPINFER_ACCELERATOR
  - desc: Source-first Skill/install review and high-permission project/deployment assessment.
  - learnings: `$skill-name` differs from description matching; Peach is single-user and should not be public-facing.

### C:\Users\zuoshi\Documents\Codex\2026-08-21\mo-x

#### 2026-10-30

- MJP conversion, performance writing, and CDN cache: MJP, libopencore_amrnb, 绩效自评, statusCodeOSS.js, CF-Cache-Status
  - desc: MJP extraction/verification, Chinese performance writing, and a cache-header follow-up in hawkeye.
  - learnings: Preserve originals; removing a timestamp alone does not defeat client no-cache headers.

### C:\Users\zuoshi\Desktop\hawkeye-southeast-asia

#### 2026-10-29

- Service Exception error-code lookup: Service Exception, code 500, en.json, rg -n -i -C 3, 360zip$Temp
  - desc: Exact temporary extracted `en.json` lookup for that source/version.
  - learnings: Lines 531–532 map `Service Exception` to `500`; it is not a general response contract.

### C:\Users\zuoshi\Desktop\phantom-ui-ops

#### 2026-10-28

- 小程序预设分页反显: presets.vue, miniTemplateId, miniTemplateName, AiSelectLoadmore, displayOptions, getFormTempList
  - desc: Single-file fix for non-first-page preset names in edit/copy dialogs.
  - learnings: `displayOptions` injects historical values; remote method returns `{ rows, total }`; browser checks remain.

### Older Memory Topics

#### C:\Users\zuoshi\Desktop\phantom-ui-ops

- Tenant contacts, action layout, and common UI fixes: tenant.vue, headPhone, GoodsCombine.vue, submitLoading, user-list, OrgSelector.vue, pageFullName
  - desc: International contact validation, Grid action buttons, duplicate-submit loading, table height, and the 28MB tree-to-lazy-select refactor; cwd-sensitive.
- JUSTSO-11529 token isolation and repo bootstrap: TokenKey, opt-JUSTSO-11529, scp100.bat, microApp, AGENTS.override.md
  - desc: Four-repo authorization-key isolation, branch recovery, deployment-script diagnosis, and ops/运营 micro-app boundaries.

#### C:\Users\zuoshi\Desktop\hawkeye-southeast-asia / phantom-ui-global

- WebSocket, customer point creation, migration assessment, and promotion/image UI: goodsRecord, 101 Switching Protocols, JUSTSO-11494, uni-app, JUSTSO-11535, fourCorner.vue
  - desc: Long-connection diagnosis, customer locking, source-level uni-app assessment, device-scoped promotion goods, and image-failure interaction; runtime checks mostly pending.

#### C:\Users\zuoshi\Desktop\phantom-ui / hawkeye

- Safe record rendering and goods-image gestures: handleRecord.vue, v-html, white-space: pre-wrap, JUSTSO-11357, gesture.wxs, swiper
  - desc: Safe text-only operation records plus isolated mini-program/Web multi-image preview behavior.

#### C:\Users\zuoshi\Desktop\phantom-ui-ops-global / phantom-ui-global

- Calibration race and local Codex knowledge: calibration, el-image, Canvas, router.addRoutes, Node-16
  - desc: Element UI image-loading race plus checkout-local runtime and documentation boundaries.

#### D:\workSpace\daily-journal

- Android offline accounting App: CapacitorSQLite, Query: Must provide an Array of Strings, values: [], executeSet, android:sync
  - desc: Product scope, SQLite first-launch fix, and safe local handoff; do not overwrite custom plugins with `cap add android`.

#### C:\Users\zuoshi\Desktop\hawkeye / ant / subproject-store / ironman

- Architecture, cache/ranking, and audio diagnosis: BUILD_TARGET=micro, statusCodeOSS, CF-Cache-Status, machineGetData, openLock, handelVoiceBroadcast
  - desc: Multi-repo boundaries, Cloudflare cache evidence, sales-ranking mapping, Vue bootstrap audit, and phone-vs-device audio chains.

#### C:\Users\zuoshi\Documents\Codex\2026-10-16\g-i-t

- GKD snapshot rule: GKD, JSON5, ShortContainerHostActivity, `text$=" 的广告"`, actionCd: 3000
  - desc: Snapshot-derived Zhihu ad-rule workflow; verify package/activity before presenting as applicable.

#### C:\Users\zuoshi\Documents\Codex\2026-09-07\zuo

- Health management and acute-symptom triage: atrial-fibrillation, anticoagulation, sore-throat, earache, antibiotics
  - desc: Report-first exercise safety, cardiology prompts, and emergency boundaries; not a treatment plan.

#### Codex workflow

- Multi-repo organization and requirement delivery: frontend-project-bootstrap, AGENTS.md, change-plan.md, requirement-git-mr-workflow
  - desc: Independent release units, local-only knowledge, and the normal `opt-<需求编号>` commit/push/MR procedure. [ad-hoc note]
- Learning-mentor imported-state verification: learning-mentor, progress.md, AI-029, resume, imported-state
  - desc: Read-only learning-roadmap/status aggregation; confirm user, project, and write authorization before resuming or persisting imported state.
- Chinese business writing, product support, and FreeClip 2: 绩效自评, ChatGPT, Codex, FreeClip 2, Windows Bluetooth
  - desc: Source-faithful Chinese writing, current-product-doc checks, and phone-plus-PC headphone pairing without deleting the phone record.
