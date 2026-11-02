# CLAUDE.md

本文件为 Claude Code (claude.ai/code) 在此代码库中工作时提供指导。

以后所有回复都用中文模式

## 项目概述

这是一个智能售货机运营管理平台（Hawkeye/简单购运营中心）的微信小程序。该应用服务于不同用户角色，包括运营商、平台管理方和品牌商。

## 开发命令

### 依赖管理
```bash
npm install
```

### 构建/部署
这是一个微信小程序。开发在微信开发者工具中进行。安装 npm 包后：
1. 在微信开发者工具中，点击"工具" > "构建 npm"
2. 这会从 `node_modules` 创建 `miniprogram_npm` 目录

### 环境配置
应用使用 `utils/env/` 中定义的环境特定配置。切换环境：
1. 编辑 `utils/env/env.js` 设置环境字符串：
   - `'develop'` 开发环境（加载 `config.dev.js`）
   - `'test-1'` 测试环境（加载 `config.t1.js`）
   - `'production'` 生产环境（加载 `config.prod.js`）
2. 每个配置文件导出 API 主机的 `baseHost`

**注意**：README 中也提到了 `utils/config.dev.js` 用于本地覆盖，但实际配置加载通过 `utils/env/env.js` 进行。

## 架构

### 目录结构

```
├── api/                    # 按功能模块组织的 API 函数
├── components/             # 可复用组件
├── pages/                  # 主页面
├── packageBrand/           # 品牌商分包
├── packageOperator/        # 运营商分包（最大的模块）
├── packagePlatform/        # 平台管理方分包
├── packageCharts/          # 图表/分析分包
├── packageOperatorDevice/  # 设备管理分包
├── packageOperatorOrder/   # 订单管理分包
├── packageOperatorInventory/ # 库存管理分包
├── store/                  # MobX 状态管理
├── utils/                  # 工具函数
└── utils/env/              # 环境配置
```

### 核心架构模式

#### API 层 (`api/`)
所有 API 调用按功能模块组织。遵循以下 API 方法命名规范：
- `模块 + 控制器 + 方法`（例如 `iotVmModelDetail`）
- 如果路径有参数：`模块 + 控制器 + 方法 + 参数名称`（例如 `iotVmModelDetailId`）
- 如果存在重复：添加请求类型（例如 `iotVmModelDetailGet`）

示例：
```javascript
import fetch from '../../utils/fetch.js'

export function iotVmModelDetail(id) {
  return fetch({
    url: `/api/iot/vmModel/detail/${id}`,
    method: 'get'
  })
}
```

#### HTTP 客户端 (`utils/fetch.js`)
集中式的 fetch 封装，具有以下特性：
- 自动添加认证头
- 通过 `fetchErrorMessage.js` 处理错误消息
- 支持简单和对象式参数传递
- Token 存储在 `config.tokenKey` 指定的 storage key 下

使用方式：
```javascript
// 简单风格
fetch.get('/api/endpoint', data)
fetch.post('/api/endpoint', data, contentType)

// 对象风格
fetch.get({
  url: '/api/endpoint',
  data: bodyData,
  params: queryParams
})
```

#### 状态管理
使用 `mobx-miniprogram` 进行响应式状态管理。主 store 在 `store/operatorConfig.js` 中，用于运营商特定的全局状态（推荐类型等）。

组件可以通过 `miniprogram-computed` 使用计算属性和监听器：
```javascript
import { ComponentWithComputed } from 'miniprogram-computed';

ComponentWithComputed({
  data: { a: 1, b: 1 },
  computed: {
    sum(data) { return data.a + data.b }
  },
  watch: {
    'a, b': function(a, b) {
      this.setData({ total: a + b })
    }
  }
})
```

#### WebSocket 连接 (`utils/websocket.js`)
通过 WebSocket 进行实时设备通信。关键模式：
- 设备状态监控
- 2 秒超时的重连逻辑
- 10 秒心跳通过 `statusReport()`
- 消息超时时自动重连
- WebSocket 属性附加到 `app.globalData` 以保持向后兼容

#### 导航和布局
- 自定义 tab bar 在 `custom-tab-bar/`
- 自定义导航栏组件位于 `/components/CustomNavBar/CustomNavBar`
- 系统信息（navBarHeight、cachetHeight、bottomLift）存储在 `app.globalData` 中

#### 认证流程
1. `app.onLaunch()` 检查路径是否需要登录
2. `app.registered()` 调用 `wx.login()` 并通过 `/api/system/auth/staff/login` 交换 code 获取 token
3. Token 存储在 `config.tokenKey` 下
4. `app.initUserData()` 获取用户信息和权限（`elements` 映射）
5. 通过 `app.changeCurrentGroup(groupId)` 切换群组/角色

认证的关键错误码：
- `8406`：未找到用户
- `8403`：账户未激活
- `8404`：用户未绑定任何群组

#### 多角色架构
三个主要用户类别（存储在 `userInfo.orgCategory` 中）：
1. **平台方 (1)**：顶级管理员，管理运营商
2. **运营商 (3)**：管理设备、库存、订单
3. **品牌商 (2)**：管理品牌特定内容

UI 根据用户的 `orgCategory` 和 `roleCodes` 进行适配。

#### 分包页面路由
应用使用微信分包进行代码分割：
- 主包：核心页面（首页、登录、我的等）
- 分包：按需加载的功能特定模块
- 在 `app.json` 中定义常见转换的预加载规则

### 配置

#### 环境配置 (`utils/env/`)
- `config.dev.js`、`config.prod.js`、`config.t1.js`、`config.staging01.js`、`config.dev01.js`、`config.dev02.js`
- `utils/config.js` 中的主配置加载适当的环境文件
- `loadOrgDict()` 动态加载组织特定的字典

#### App 全局数据 (`app.globalData`)
关键属性：
- `userInfo`：当前用户信息、群组、orgId、orgCategory
- `elements`：UI 元素可见性的权限映射
- `navBarHeight`、`cachetHeight`、`bottomLift`：系统布局尺寸
- `scene`：微信小程序场景值

#### 常量
- `utils/constants.js`：员工状态常量（INACTIVE、ACTIVE、DISABLED）
- `utils/fetchErrorMessage.js`：按模块组织的全面错误码 → 消息映射（auth、user、device、payee 等）

### 第三方库
- **@vant/weapp**：UI 组件库
- **mobx-miniprogram**：状态管理
- **mobx-miniprogram-bindings**：组件的 MobX 绑定
- **miniprogram-computed**：计算属性和监听器
- **min-ready**：异步初始化的就绪队列
- **moment**：日期/时间工具
- **weapp-qrcode**：二维码生成

### 关键工具函数 (`utils/util.js`)
- `validateFields()`：使用规则的表单验证（required、minLength、pattern、自定义验证器）
- `treeTolist()`：将树形数据展开为列表
- `getMsg()`：从错误码查找错误消息
- `getVmCodeFromUrl()`：从各种 QR/url 格式解析设备代码
- `param()`、`param2Obj()`、`toQueryString()`：URL 参数处理
- `sortTree()`：按 sort 字段对树进行排序
- `diffApiCtrlValue()`：带超时的设备操作状态轮询

### 开发注意事项

#### 添加新的 API 方法
在适当的 `api/` 子目录中创建，遵循命名规范。使用来自 `utils/fetch.js` 的集中式 `fetch`。

#### 创建页面
分包中的页面遵循 `packageName/pages/pageName/pageName` 模式。添加新页面时更新 `app.json`。

#### 环境切换
编辑 `utils/env/env.js` 更改导出字符串。确保存在对应的 `config.{env}.js`。

#### 自定义组件
- 全局组件在 `app.json` 的 `usingComponents` 中注册
- 页面特定组件在其各自的目录中
- 使用 `miniprogram-computed` 的 `ComponentWithComputed` 支持计算/监听器

#### 设备特定功能
- 设备通信使用带自动重连的 WebSocket
- 设备代码支持多种格式（二维码、URL、条形码）
- 设备操作通过 `diffApiCtrlValue()` 轮询状态
