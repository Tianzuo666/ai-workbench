v1

## User Profile

The user works on Windows Vue front-end projects, especially Phantom UI variants and Hawkeye Southeast Asia, and normally communicates in Chinese. They also maintain a personal Android app and seek occasional consumer-device or health information.

## User preferences

- For code investigations, the user has repeatedly asked for a read-only diagnosis first, then explicit confirmation before edits; do not infer that permission from an investigation request.
- For Yunxiao work items, use the read-only `yunxiao-reader`/Aliyun DevOps CLI rather than a browser, and never expose a PAT.
- For performance/self-review prose, stay near the supplied length and use direct paragraphs rather than a long numbered structure.

## General Tips

- Preserve existing worktree changes; distinguish implementation from build, test, lint, integration, and manual verification.
- Do not reproduce any suspected credential that might appear in tool output.
- When giving current prices or other changing online information, distinguish a current page/store price from an official or historical promotional price.
- Imported learning-plan checkpoints are not confirmed user progress. For health follow-ups, obtain current symptoms and clinician guidance rather than treating historical information as current.

## What's in Memory

### Phantom UI cross-repository contact information

#### 2026-10-28

- rollout_summaries/2026-10-28T08-07-36-hvYe-piece_219_contact_phone_calling_code_multi_repo.md — PIECE-219 implementation for international phone/email organization editing across three repositories; retrieve when validating, completing, or reconciling those uncommitted changes. thread_id=01a1818c-e26a-7370-b2c2-e1df9d052daa
  - Scope: `phantom-ui-ops-global` `tenant.vue`, `phantom-ui-global` `BasicInfoForm.vue`, and Hawkeye `deptEdit.vue`; `libphonenumber-js@1.13.7` was declared/installed in all three.
  - Contract: persist `phoneCallingCode` without `+`; email remains in `headPhone` and clears the code; derive codes from `countryCode.attributes.phoneCode`. China permits only phone, hides its code, submits an empty code for backend completion, and validates temporarily with `86`.
  - Status: digit-only validation uses `libphonenumber-js/max` and accepts MOBILE/FIXED_LINE_OR_MOBILE. No validation was run; worktrees were uncommitted and already dirty. Ops-global installation also changed 97 locked dependencies.
  - Task convention: add necessary comments to methods/complex logic and a header comment to new pages/components.

### Phantom UI Ops

#### 2026-10-28

- rollout_summaries/2026-10-28T06-42-58-LBU0-phantom_ui_ops_batch_hierarchy_migration_and_dept_path_displ.md — PIECE-208 requirement review and the implemented, unvalidated JUSTSO-11627 batch hierarchy migration. thread_id=01a1813f-66b0-7632-ae24-f920ac6b98dc
  - Project: `C:\Users\zuoshi\Desktop\phantom-ui-ops`; JUSTSO-11627 pointers: `src/api/baseSetManager/dept/index.js`, `src/views/baseSetManager/dept/index.vue`, `src/views/baseSetManager/dept/components/BatchDeptMove.vue`.
  - Status: APIs, `dept:showBatchMove` entry, paged operator selection, template download, first-sheet 1,000-row precheck, and upload/confirmation/error/retry flows were implemented. No validation. Backend owns content validation/rollback; clarify blank rows, empty files, `.xls`, normalization, and prototype/document differences.
  - Git: the reported local branch result was commit `24195e9d` while remote was `daa0b23d`; an exact `--force-with-lease` push was suggested but not confirmed.
  - PIECE-208 requires visible department paths to start at the account-visible node for PC order/device/inventory and PC/App/H5 exports; this scope was reviewed only, not implemented here.

- rollout_summaries/2026-10-28T09-01-47-Ggkh-fix_vmtags_preset_pagination_display.md — Implemented mini-program preset pagination display fix; use when validating paged selection behavior. thread_id=01a181be-7dd5-7c90-88f7-ed4a5526b7d9
  - Project: `C:\Users\zuoshi\Desktop\phantom-ui-ops`, branch `opt-JUSTSO-11700`; `src/views/machineManager/vmTags/presets.vue` uses `ai-select-loadmore`, ID/name display options, and pagination/name synchronization. No build/test; check non-first-page edit/copy, search, load-more, and switching.

#### 2026-10-26

- rollout_summaries/2026-10-26T07-53-43-iPsm-justso_11595_device_detail_scan_code_preview.md — Implemented JUSTSO-11595 device-detail scan-sticker preview/download UI; use to validate or extend its uncommitted implementation. thread_id=01a17733-7400-7361-83dc-f74f612610fa
  - Project: `C:\Users\zuoshi\Desktop\phantom-ui-ops`, branch `opt-JUSTSO-11595`; pointers: `src/api/machineManager/vmTags/index.js`, `src/views/marketManager/machine/base/basicinfo.vue`, and untracked `src/views/marketManager/machine/base/components/scanCodeTemplatePreview.vue`.
  - API: `GET /api/tool/scanCodeRecord/preview?scanCode=...`; supports dual-door split, carousel, canvas preview, 3× PNG download, and enlargement. No validation; check compilation, CORS, data variants, device changes, and UI.

#### 2026-10-13

- rollout_summaries/2026-10-13T03-46-29-TQQs-simplify_table_height_and_scroll_handling.md — Page-local table-height/reset simplification, including the Ops user page; retrieve if revisiting table scrolling. thread_id=01a1335e-71d1-7e02-8263-3a5aad6b090c
  - Pointer: `C:\Users\zuoshi\Desktop\phantom-ui-ops\src\views\baseSetManager\user\index.vue` on `opt-JUSTSO-11575`; avoid a shared mixin/complex cache restore. Keep `resetTable` only when a parent invokes it. No validation run.

### Phantom UI Global department-path display

#### 2026-10-28

- rollout_summaries/2026-10-28T06-42-58-LBU0-phantom_ui_ops_batch_hierarchy_migration_and_dept_path_displ.md — Three path-display calls were implemented in global, followed by an unfinished audit for omissions. thread_id=01a1813f-66b0-7632-ae24-f920ac6b98dc
  - Project: `C:\Users\zuoshi\Desktop\phantom-ui-global`; modified `DataCubeManager/salesAnalysis/components/detailDialog.vue`, `marketManager/ActivityManager/components/activityTable.vue`, and `layout/components/navbar/OrgInfo.vue` to call `deptPathDisplay(..., $store.state.user.userData)`. Unvalidated.
  - Audit candidates include device/point analysis, `marketManager/machineOperator/index.vue`, device monitoring, and pending orders; confirm page purpose before changing selectors, edit forms, permissions, or identity switching.

#### 2026-10-27

- rollout_summaries/2026-10-27T10-26-15-rDLM-global_dept_path_display_filter.md — Shared department-path filter implementation in ops-global; retrieve for its specific data-scope behavior. thread_id=01a17ce5-76ee-7790-b879-c595dbb24ce6
  - Project reported as `phantom-ui-global`; branch `fix-lable-img`; pointers: `src/filters/index.js`, `OrderInfo.vue`, `machineOperator/index.vue`, `inventoryControl/index.vue`. For `dataScope === 2`, display begins at the logged-in department; otherwise it remains full. Uncommitted and unvalidated.

### Hawkeye Southeast Asia

#### 2026-10-22

- rollout_summaries/2026-10-22T05-54-17-3ufK-uniapp_refactor_analysis_and_before_after_comparison.md — Read-only migration assessment for Android/iOS/H5, explicitly excluding WeChat mini-program. thread_id=01a1622c-ade0-7612-89c8-079f49269c60
  - Project: `C:\Users\zuoshi\Desktop\hawkeye-southeast-asia`, branch `fix-img-click`; no code/build/test changes. Validate a Vue 3 uni-app vertical slice (login → device list → scan → device details) before broad migration; dynamic routes and native capabilities are major risks.

### Daily Journal Android app

#### 2026-10-21

- rollout_summaries/2026-10-21T03-16-03-Iphd-daily_journal_vue_capacitor_sqlite_query_fix.md — Phase-one offline journal and an unverified Capacitor SQLite query fix. thread_id=01a15c75-73bf-7830-9aef-64965dfd34cf
  - Project: `D:\workSpace\daily-journal`; pointers: `src/data/database.ts`, `tests/database.test.ts`. Password protection is deferred to phase two. The fix has not been checked, rebuilt, or device-tested; the installed old APK lacks it.

### Older Memory Topics

#### Phantom UI content rendering

- rollout_summaries/2026-10-22T02-28-35-oQWy-phantom_ui_handle_record_text_rendering.md — Changed warehouse handle-record content from `v-html` to safe text interpolation with preserved line breaks; unvalidated. thread_id=01a16170-5c11-7a43-aa02-39d011f7c37e

#### GKD custom rules

- rollout_summaries/2026-10-16T03-05-13-217s-gkd_custom_rules_zhihu_weather_honor_android15.md — Honor 90 Pro/Android 15 GKD setup and proposed Zhihu/Weather local JSON5 rules, neither confirmed working. thread_id=01a142ab-bc20-7443-9fd5-4e6770c35ec2

#### Yunxiao skills and project research

- rollout_summaries/2026-10-05T12-27-48-sDiX-skills_yunxiao_reader_peach_project_research.md — Read-only Yunxiao skill workflow, JUSTSO-11494 research, and unstarted Peach portable setup. thread_id=01a10c08-d6d4-7961-b33e-67c554f52753

#### Learning-mentor consultation

- rollout_summaries/2026-10-22T10-29-23-iB1j-learning_mentor_plan_progress_imported_state_warning.md — Imported AI-agent track state is unverified and must not be treated as the user's actual progress. thread_id=01a16328-8b6d-7bb1-91f8-a21337c02d45

#### Health context

- rollout_summaries/2026-09-07T07-30-27-C8uE-health_weight_loss_afib_and_acute_sore_throat.md — Historical obesity/metabolic concerns and reported atrial fibrillation with reduced EF, plus later acute throat/ear/head pain; obtain current clinical status if revisited. thread_id=01a07ac6-8b4c-7352-84ba-766a9b8d3b5c

#### Technical, consumer, and health history

- rollout_summaries/2026-08-21T06-47-18-t25T-user_task_history_technical_consumer_health_2026_10.md — Earlier troubleshooting, consumer-device context, writing preferences, and unresolved Hawkeye device-ranking/CDN-cache investigations. thread_id=01a02312-f0c7-78d2-918f-1674443ab25d
