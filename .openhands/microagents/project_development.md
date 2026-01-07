---
name: project_development
type: knowledge
version: 1.0.0
agent: CodeActAgent
triggers: []
---

# FastGPT 项目开发指南

本微代理帮助开发者了解 FastGPT 项目并在现有基础上进行迭代开发改进。

## 项目概述

FastGPT 是一个基于 LLM 大语言模型的知识库问答系统，提供开箱即用的数据处理、模型调用等能力。同时可以通过 Flow 可视化进行工作流编排，从而实现复杂的问答场景。

## 技术栈

- **前端框架**: Next.js + TypeScript
- **UI 组件库**: Chakra UI
- **数据库**: MongoDB + PostgreSQL (PG Vector 插件) / Milvus
- **包管理**: pnpm (monorepo 架构)
- **国际化**: next-i18next

## 项目结构

```
FastGPT/
├── projects/
│   ├── app/          # 主应用项目
│   └── sandbox/      # 沙箱项目
├── packages/
│   ├── global/       # 全局共享类型和工具
│   ├── plugins/      # 插件系统
│   ├── service/      # 后端服务层
│   ├── templates/    # 模板系统
│   └── web/          # 前端共享组件和工具
├── python/           # Python 相关服务
│   ├── bge-rerank/   # 重排序模型
│   ├── ocr/          # OCR 服务
│   └── pdf-marker/   # PDF 处理
├── docSite/          # 文档站点
├── files/            # 配置文件
│   ├── docker/       # Docker 配置
│   └── helm/         # Kubernetes Helm 配置
└── scripts/          # 构建和工具脚本
```

## 开发环境设置

### 前置要求

- Node.js >= 18.16.0
- pnpm >= 9.0.0
- MongoDB
- PostgreSQL (带 PG Vector 插件) 或 Milvus

### 安装依赖

```bash
# 给脚本执行权限
chmod -R +x ./scripts/

# 安装所有依赖
pnpm i

# 如果 Node 版本 >= 20
NODE_OPTIONS=--no-node-snapshot pnpm i
```

### 启动开发服务器

```bash
# 方式一：直接进入项目目录
cd projects/app
pnpm dev

# 方式二：使用 Make 命令
make dev name=app
```

## 核心功能模块

### 1. 应用编排能力

- 对话工作流、插件工作流
- 工具调用
- Code sandbox
- 循环调用
- 用户选择
- 表单输入

### 2. 知识库能力

- 多库复用、混用
- chunk 记录修改和删除
- 支持多种文件格式导入 (txt, md, html, pdf, docx, pptx, csv, xlsx)
- 混合检索 & 重排
- API 知识库

### 3. OpenAPI 接口

- completions 接口 (chat 模式对齐 GPT 接口)
- 知识库 CRUD
- 对话 CRUD

## 开发指南

### 添加新功能

1. 确定功能所属模块 (app, packages/service, packages/web 等)
2. 遵循现有代码风格和目录结构
3. 添加必要的类型定义到 `packages/global`
4. 如需后端服务，在 `packages/service` 中实现
5. 前端组件放置在 `packages/web` 或 `projects/app/src/components`

### 国际化 (i18n)

```typescript
// 在页面中使用
import { useTranslation } from 'next-i18next';

const { t } = useTranslation();
// 使用: t('namespace:key')

// 在静态文件中使用
import { i18nT } from '@fastgpt/web/i18n/utils';
const text = i18nT('app:template.simple_robot');
```

### 构建 Docker 镜像

```bash
# 不使用代理
make build name=app image=registry.cn-hangzhou.aliyuncs.com/fastgpt/fastgpt:v4.8.1

# 使用代理
make build name=app image=registry.cn-hangzhou.aliyuncs.com/fastgpt/fastgpt:v4.8.1 proxy=taobao
```

## 代码规范

- 使用 TypeScript 进行类型安全开发
- 遵循 Prettier 代码格式化配置
- 翻译键使用小写字母和下划线，如 `common.close`
- 组件使用函数式组件和 React Hooks

## 测试

项目使用 Jest 进行测试，配置文件位于 `projects/app/jest.config.js`。

## 常见开发任务

### 添加新的工作流节点

1. 在 `packages/global/core` 中定义节点类型
2. 在 `packages/service/core` 中实现节点逻辑
3. 在 `projects/app/src/components` 中创建节点 UI 组件

### 扩展知识库功能

1. 文件解析器位于 `packages/service/core`
2. 向量存储接口在 `packages/service/core`
3. 检索逻辑在 `packages/service/core`

### 添加新的 API 端点

1. 在 `projects/app/src/pages/api` 中创建 API 路由
2. 在 `packages/service` 中实现业务逻辑
3. 更新 OpenAPI 文档

## 资源链接

- [项目文档](https://doc.tryfastgpt.ai/docs/intro/)
- [本地开发指南](https://doc.tryfastgpt.ai/docs/development/intro/)
- [部署指南](https://doc.tryfastgpt.ai/docs/development/sealos/)
- [OpenAPI 文档](https://doc.tryfastgpt.ai/docs/development/openapi/)
