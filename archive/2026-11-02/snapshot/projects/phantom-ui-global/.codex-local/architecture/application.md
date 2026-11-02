# phantom-ui-global 架构速查

> 个人本地文档，不提交。依据 2026-10-10 的本地工作树整理。

## 产品职责

本仓库是运营商、商户使用的 B2B 新零售综合管理后台，覆盖设备、订单、库存、商品、补货、广告、用户和数据报表等业务。

平台方与生产商已由代码分流到新平台：分组选择排除 `orgCategory` 为 `1`、`4` 的项目，`App.vue` 也包含迁移提示。因此 README 中“面向平台方”的描述不能作为当前用户边界依据。

## 启动链路

1. `src/main.js` 注册 Element UI、公共组件、指令、过滤器和全局 mixin。
2. `initLanguage()` 根据缓存或浏览器语言初始化 `vue-i18n` 与 Element UI 语言。
3. Vue 挂载 Router、Vuex 与 i18n。
4. 路由守卫读取 token；首次进入时调用 `GetStaffInfo`。
5. 后端菜单经 `GenerateStaffRoutes` 转换并由 `router.addRoutes` 注册。
6. 状态码映射按当前 locale 从 OSS 预加载。

## 登录与权限

- 登录：`POST /api/system/auth/staff/login`。
- 用户分组：`GET /api/system/currentStaff/groups`。
- 菜单：`GET /api/system/currentStaff/menus`。
- 视图权限：`GET /api/system/currentStaff/views`。
- 分组切换：`POST /api/system/auth/staff/switchGroup`。
- 首次登录与密码重置另有邮件相关接口。
- 大部分业务路由来自后端组件路径；前端文件重命名必须检查菜单契约。

## 国际化

- 核心入口：`src/i18n/index.js`。
- 默认文案：`src/i18n/en/en.js`。
- 业务模块文案：`src/i18n/en/modules/`。
- 布局和登录页广泛使用 `$t()`。
- `switchLanguage` 同时切换 Vue I18n、Element UI、Moment，并触发状态码语言刷新。

修改用户可见文案时：

1. 检查是否已有相同翻译 key。
2. 通用文案放默认资源，领域文案放对应模块。
3. 不要只翻译页面正文而遗漏表头、校验、通知和确认框。

## 状态与页面

Vuex 主要模块：`app`、`user`、`permission`、`machineWarn`、`common`、`dataDictionary`、`statusCode`。

跨页面的登录、权限、布局、字典和状态码使用 Vuex；普通列表和表单数据继续由页面自行管理。

主要业务目录包括：

- `marketManager`：设备与市场运营。
- `orderManager`：订单、反馈和异常处理。
- `commodityManager`：商品、分类、品牌和价格策略。
- `inventoryControl`、`replenishment`、`replenishDispatch`：库存与补货。
- `purchaseSalesInventory`：进销存业务。
- `DataCubeManager`、`dashboard`：数据分析和看板。
- `baseSetManager`、`userManager`：平台配置、组织和用户。

## 构建与测试

- Node.js 16，npm 7+。
- 开发端口 7777。
- Webpack 4，支持 prod、test、sit、staging、test01、dev1～dev3 和 OSS 构建。
- Jest 与 Vue Test Utils 的用例位于 `test/unit/`。
- 即使测试已配置，也必须先获得用户明确批准才能运行 Jest、lint、构建或格式化。
- 本地代理来自被忽略的 `config/dev.conf.js`，不要读取、复制或输出其实际地址，除非用户明确要求。

## 与 ops-global 的边界

- 两仓没有直接构建或运行时依赖。
- 两仓共享大量 API、组件和历史实现，但同路径代码多数已经分化。
- 不要把商户仓的国际化、登录和组织逻辑直接覆盖到运维仓。
- 不要把运维仓的 Micro App 初始化复制到本仓，除非出现明确的产品需求。
- 跨仓移植前阅读 `phantom-ui-ops-global/.codex-local/architecture/repository-map.md`。

## 已知文档冲突

- `CLAUDE.md` 描述了 Qiankun 与 `convenience` 子应用，但当前 `package.json`、`src/main.js` 和 `AppMain.vue` 未发现对应实现。
- README 的平台方用户定位与当前分组过滤、迁移提示冲突。
- 以实际依赖、入口源码和登录分流代码为准。
