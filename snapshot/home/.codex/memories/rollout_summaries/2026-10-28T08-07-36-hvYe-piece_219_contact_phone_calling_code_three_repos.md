thread_id: 01a1818c-e26a-7370-b2c2-e1df9d052daa
updated_at: 2026-10-30T10:28:57+00:00
rollout_path: \\?\C:\Users\zuoshi\.codex\sessions\2026\10\31\rollout-2026-10-31T15-42-45-01a1818c-e26a-7370-b2c2-e1df9d052daa_01a190e9-3594-7311-8f88-af3a2879d3bb.jsonl
cwd: \\?\C:\Users\zuoshi\Desktop\phantom-ui-ops-global
git_branch: opt-PIECE-219

# PIECE-219 联系方式国际化改动扩展到三个前端仓库

Rollout context: 用户围绕组织联系方式支持邮箱、国际手机号和 `phoneCallingCode` 展开实现，涉及 `phantom-ui-ops-global`、`phantom-ui-global` 和 `hawkeye-southeast-asia`。用户明确要求中国不显示邮箱/区号，区号字段置空由后端补充；非中国按国家字典 `countryCode.attributes.phoneCode` 提供邮箱/手机号选择及区号校验。用户还要求复杂逻辑必须有必要中文注释，新页面/组件要有标准注释头。

## Task 1: phantom-ui-ops-global 运维端组织联系方式

Outcome: partial

Preference signals:
- 用户确认“中国时不能选邮箱和区号，区号置空就行，后端会自动补充” -> 中国只显示手机号，前端校验临时使用 `86`，提交 `phoneCallingCode: ''`。
- 用户要求“手机号根据区号进行格式校验” -> 不能只用数字正则，应结合区号、号码有效性及号码类型校验。
- 用户要求“写方法和复杂逻辑时要带上必须的注释” -> 新增校验、国家分流、回显和提交逻辑应带简洁中文设计意图注释。

Key steps:
- 修改 `src/views/baseSetManager/dept/form/tenant.vue`：非中国显示邮箱/手机号单选；手机号显示区号选择器；邮箱仍存 `headPhone`。
- 从 `parentObj.countryCodeList` 的 `attributes.phoneCode` 生成区号选项，展示 `+86`，保存不带 `+` 的字符串值。
- 使用 `libphonenumber-js/max`，按区号拼接国际号码并校验 `isValid()` 与 `MOBILE`/`FIXED_LINE_OR_MOBILE` 类型；区号变化会重新校验。
- 编辑回显时根据 `headPhone` 是否包含 `@` 判断类型；邮箱或中国场景清空区号。
- `package.json` 增加 `libphonenumber-js: 1.13.7`，依赖安装成功。

Failures and how to do differently:
- 未运行构建、lint、测试、接口联调或页面验证，不能宣称运行时验收完成。
- 安装时旧锁文件触发 npm 元数据补全并调整了 97 个本地依赖，出现 Node/废弃依赖警告；后续应审查工作区差异，避免把无关锁文件变化带入提交。

Reusable knowledge:
- 保存入口仍为 `POST /api/system/dept/save`，通过 `getPostFrom()` 传递 `phoneCallingCode`。
- 中国区号由后端补充，前端不得把临时校验用的 `86` 回填到表单。

References:
- `C:\Users\zuoshi\Desktop\phantom-ui-ops-global\src\views\baseSetManager\dept\form\tenant.vue`
- `C:\Users\zuoshi\Desktop\phantom-ui-ops-global\package.json`
- 安装命令：`npm install libphonenumber-js@1.13.7 --save-exact --ignore-scripts --no-audit --no-fund`

## Task 2: phantom-ui-global 运营端组织联系方式

Outcome: partial

Preference signals:
- 用户补充“邮箱/手机的选择项要根据国家是否不为中国判断” -> 运营端单选组也必须仅在已确定国家且不为 `CN` 时显示。
- 用户要求邮箱切换清除旧区号、国家为中国时后端自动补区号 -> 提交中国或邮箱时传空区号。

Key steps:
- 修改 `src/views/system/operatorDept/components/BasicInfoForm.vue`，增加国家分流、联系方式类型切换、区号选择、历史回显和手机号/邮箱校验。
- 运营端通过 `getOptions([{ key: 'countryCode', array: this.countryCodeList }])` 加载字典，读取 `attributes.phoneCode`。
- 根组织编辑仍走 `PUT /api/system/dept/update`，其他组织走 `/api/system/dept/save`；两者提交均携带新字段约定。
- 修改 `src/i18n/en/modules/orgInfo.js` 增加手机号、区号和区号校验英文文案。
- 依赖 `libphonenumber-js@1.13.7` 已安装成功。

Failures and how to do differently:
- 未运行构建、lint、测试或页面验证；当前仅完成静态差异检查。
- 需要重点手动验证中国/非中国切换、上级国家异步回显、邮箱切换清空区号和保存后再次打开。

Reusable knowledge:
- `countryCode` 表示组织国家；`phoneCallingCode` 表示手机号区号；`headPhone` 保存手机号或邮箱。
- 运营端通过 `contactCountryCode` 处理新建下级组织使用上级国家的场景，避免表单复用沿用上一次区号。

References:
- `C:\Users\zuoshi\Desktop\phantom-ui-global\src\views\system\operatorDept\components\BasicInfoForm.vue`
- `C:\Users\zuoshi\Desktop\phantom-ui-global\src\i18n\en\modules\orgInfo.js`

## Task 3: hawkeye-southeast-asia 移动/Web 组织编辑页

Outcome: partial

Preference signals:
- 用户说“这个也处理下”并指定 `src/views/organizationManage/deptEdit.vue` -> 将同一联系方式规则扩展到该独立仓库，但不能假设其与两个 PC 仓库共享实现。
- 用户要求必要注释 -> 新增 `validateContact`、类型切换、区号回显和提交分流均补了中文注释。

Key steps:
- 修改 `src/views/organizationManage/deptEdit.vue`：使用 Cube UI 单选组和 `CountryMultiSelect`，非中国支持邮箱/手机号与区号；中国仅手机号且提交空区号。
- `phoneCodeOptions` 从国家字典项 `attributes.phoneCode` 生成，兼容属性为 JSON 字符串或对象。
- 使用 `libphonenumber-js/max` 按区号校验完整号码；区号变化重新校验；编辑详情根据历史 `headPhone` 回显类型和区号。
- 修复 Cube 单选组回显会触发 `input` 的问题：只有类型真正变化时才清空输入和区号，并增加多语言标签换行样式。
- 更新英文、越南、泰文、日文、韩文 `dept` 文案；`package.json` 增加依赖并安装成功。

Failures and how to do differently:
- 未运行构建、lint、测试、浏览器或真机验证；移动端 Cube UI 交互和多语言布局仍需实际验证。
- 依赖安装出现 `stylus-loader` 要求 Node >=18、当前 Node 16 的警告，但 npm 安装成功。

Reusable knowledge:
- 该仓库字典由 `src/api/type.js#getOptions` 加载，选项的 `attributes` 可能保留原始 JSON 字符串，因此页面侧需要安全解析。
- 保存接口为 `POST /api/system/dept/save`。

References:
- `C:\Users\zuoshi\Desktop\hawkeye-southeast-asia\src\views\organizationManage\deptEdit.vue`
- `C:\Users\zuoshi\Desktop\hawkeye-southeast-asia\src\api\type.js`
- `C:\Users\zuoshi\Desktop\hawkeye-southeast-asia\src\views\warehouse\components\CountryMultiSelect.vue`

## Task 4: 用户编码注释偏好

Outcome: success

Preference signals:
- 用户明确说“写方法和复杂逻辑时要带上必须的注释，新加的页面或组件要加上注释头” -> 后续代码修改默认补充简洁中文用途/设计意图注释；新增页面或组件遵循项目注释头格式，不伪造作者和日期。

Key steps:
- 三个目标表单已有组件注释头，未新增页面或组件。
- 已为联系方式校验、国家分流、区号回显、类型切换和提交清除逻辑补充注释。

## 安全与环境注意

- 一次宽范围搜索意外输出了疑似凭据，助手未复述其内容，但已建议用户或安全同事确认是否需要轮换。未来应避免宽范围读取敏感配置、地图 key、代理文件或环境变量。
