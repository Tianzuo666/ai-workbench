# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 语言偏好
所有回复今后都使用中文模式

## 项目概述

这是一个名为"海外运营程序"（Southeast Asia Operations Program）的Vue.js应用程序，是一个用于设备监控和管理操作的移动/Web应用程序。该应用程序支持Web和移动端两种环境，通过 `VUE_APP_MODEL` 环境变量进行条件编译。

## 架构与结构

该应用程序遵循标准的Vue.js + Vuex + Vue Router架构，包含以下关键组件：

- **Vue 2.6.x** 作为核心框架
- **Vuex** 用于具有模块化存储结构的状态管理
- **Vue Router** 用于导航
- **Vant UI** 和 **Cube UI** 用于移动端组件
- **Micro Apps** 集成用于微前端支持
- **国际化(i18n)** 支持多语言

主要目录：
- `/api` - API服务调用和定义
- `/components` - 可复用的Vue组件
- `/layout` - 布局组件
- `/mixins` - Vue混入
- `/plugins` - UI库和实用工具的插件定义
- `/router` - 路由配置
- `/store` - Vuex存储模块(user, globalData, productionTest等)
- `/utils` - 实用函数
- `/views` - 页面组件

## 环境配置

该项目支持两种环境：
- `web`: 设置 `VUE_APP_MODEL=web` 无基础API URL(使用nginx转发)
- `mobile`: 设置 `VUE_APP_MODEL=mobile` 直接API基础URL

## 主要特性

1. **设备监控**: 通过WebSocket连接实时监控设备
2. **多平台支持**: Web和移动端平台的不同行为
3. **状态管理**: 使用Vuex进行复杂的状态管理，包括用户数据、设备信息和全局状态的模块
4. **国际化**: 支持多语言和动态翻译
5. **离线处理**: 对连接设备进行复杂的在线/离线状态管理
6. **微前端集成**: 使用 @micro-zoe/micro-app 进行组件加载

## 状态管理

该应用程序使用多个模块的Vuex：
- `user.js` - 用户认证和资料数据
- `globalData.js` - 具有localStorage持久性的全局共享数据
- `productionTest.js` - 生产测试相关数据
- `keepalive.js` - 路由keep-alive配置
- `tabbar.js` - 标签栏导航状态

## 常见开发任务

### 构建和运行
```bash
# 安装依赖
npm install

# 开发(web)
npm run dev

# 开发(移动端)
npm run dev:mobile

# 生产构建(web)
npm run build

# 生产构建(移动端)
npm run build:mobile

# 不同环境的移动端构建
npm run build:mobile:prod  # 生产构建
npm run build:mobile:test  # 测试构建
```

### 代码质量
```bash
# 检查/修复文件
npm run lint
```

### 环境设置
- Node版本: v16.13.0 (按README所示)
- 两种主要模式: web和移动端(由VUE_APP_MODEL环境变量控制)
- 开发期间API请求的代理配置在 `dev.conf.js` 中

## API和代理配置

开发代理在 `dev.conf.js` 中配置:
- `/api` 路径代理到后端服务器
- `/oss` 路径代理到阿里云OSS用于文件上传

## 设备通信

该应用程序通过WebSockets实现复杂的设备通信:
- 处理设备连接状态(已连接、断开连接、错误)
- 处理设备状态报告和图像数据
- 实现自动重连逻辑
- 管理在线/离线状态指示器

## 特殊注意事项

1. **Vue 2 遗留**: 该项目使用Vue 2(非Vue 3)，因此应遵循Vue 2语法和模式
2. **移动端特定生命周期**: iOS与其他平台不同的生命周期处理
3. **Keep-alive 组件**: 使用Vue的keep-alive进行高效的组件缓存
4. **LocalStorage 集成**: Vuex与localStorage之间的自动同步，用于全局数据
5. **设备检测**: 用于平台特定行为的复杂浏览器/操作系统检测