---
name: scaffold
description: "当用户提及 '搭建项目脚手架'、'初始化项目'、'帮我搭个项目'、'scaffold the project'、'set up the project'、'project setup' 等类似表述时使用"
---

# 项目脚手架搭建

## 核心原则

这个 skill 的目标是：把一个技术选型决策落地为可运行的项目骨架，让开发者能立即开始写业务代码。

只做初始化，不做业务功能。搭建完成后，项目应该处于一个"随时可以开工"的状态。

## 工作流程

### Phase 1: 理解技术栈

首先确定用户的技术选型。按优先级依次尝试：

1. **项目内文档**：查找 `docs/tech-stack.md`、`TECH_STACK.md`、`docs/architecture.md`、`README.md` 等文件，提取技术栈信息
2. **已有代码**：如果项目已有部分代码，分析 `package.json`、`go.mod`、`requirements.txt`、`Cargo.toml` 等依赖文件
3. **用户口述**：如果以上都没有，直接询问用户使用什么技术栈

如果信息不足，不要猜。直接问用户。

### Phase 2: 调研最佳实践

基于确定的技术栈，调研当前该技术栈的最佳实践。**必须先搜索，不要依赖记忆中的信息**——工具链、脚手架版本、推荐配置都在快速演进，过时的建议会让用户踩坑。

搜索工具降级链（按优先级）：
1. **WebSearch** — 首选，能获取最新的官方文档和社区推荐
2. **read skill** — 如果 WebSearch 不可用，用 read skill 抓取官方文档页面
3. **web-access skill** — 如果以上都不可用，用 web-access skill 访问网页

搜索内容：
- 该框架/语言当前推荐的项目结构
- 推荐的构建工具（对比当前主流选项）
- 推荐的代码规范和格式化工具
- 推荐的测试框架
- 项目初始化命令（官方脚手架优先）
- 该技术栈的已知坑和注意事项

只读取与用户技术栈相关的参考资料文件（如果有的话），不要读取所有参考资料。

### Phase 3: 提出搭建方案

**在执行任何操作之前**，必须向用户展示搭建方案并获得同意。方案应包含：

1. **项目名称和路径**：在哪里创建什么名字的项目
2. **目录结构**：展示关键目录和文件
3. **技术选型摘要**：框架、构建工具、包管理器、语言版本
4. **初始化方式**：使用官方脚手架还是手动搭建，以及为什么
5. **开发工具链**：代码规范、格式化、Git hooks 等
6. **CI/CD 方案**：平台（GitHub Actions / GitLab CI 等）、包含哪些检查
7. **验证计划**：搭建完成后如何确认一切正常（dev server 启动、lint 通过、CI 配置有效等）

格式示例：

```
## 搭建方案

**项目**: my-app
**路径**: /Users/x/projects/my-app
**技术栈**: React 19 + TypeScript + Vite

**目录结构**:
├── src/
│   ├── components/
│   ├── pages/
│   ├── hooks/
│   ├── utils/
│   └── main.tsx
├── public/
├── tests/
├── .github/workflows/ci.yml
├── package.json
├── tsconfig.json
├── vite.config.ts
├── .eslintrc.cjs
└── .prettierrc

**初始化方式**: `npm create vite@latest` 官方脚手架

**开发工具链**:
- ESLint (flat config) + Prettier
- Husky + lint-staged (pre-commit hooks)

**CI/CD**: GitHub Actions
- lint check
- type check
- build

**验证**: dev server 启动 → lint 通过 → build 成功 → CI 配置文件有效
```

用户确认后才进入 Phase 4。

### Phase 4: 执行搭建

按顺序执行：

#### 4.1 创建项目
- 优先使用官方脚手架（如 `create-vite`、`create-next-app`、`cargo init`、`go mod init`）
- 如果官方脚手架不符合项目需求，手动搭建
- 确保使用正确的语言/运行时版本

#### 4.2 配置开发工具链
- 代码规范（Linter）：使用 Phase 2 搜索到的当前推荐方案
- 格式化（Formatter）：与 linter 配合，使用搜索到的推荐方案
- Git hooks：根据技术栈选择合适的方案（如 Husky + lint-staged、pre-commit 等）

#### 4.3 配置 CI/CD
- 根据用户指定的平台创建 CI 配置
- CI 流水线至少包含：lint、type check（如适用）、build
- 如果用户指定 GitHub Actions，创建 `.github/workflows/ci.yml`
- 如果用户指定 GitLab CI，创建 `.gitlab-ci.yml`

#### 4.4 基础文件
- `.gitignore`：确保覆盖该语言/框架的常见忽略项
- `README.md`：包含项目简介、启动命令、开发命令、CI 说明
- 环境变量示例文件（如 `.env.example`）

### Phase 5: 验证

搭建完成后，逐项验证：

1. **依赖安装**：确认 `npm install` / `pip install` / `cargo build` 等成功
2. **开发服务器**：启动 dev server，确认能正常访问
3. **代码检查**：运行 linter，确认无报错
4. **构建**：运行 build 命令，确认产物正常
5. **CI 配置**：确认 CI 配置文件语法正确（如有 linter 可用）

如果任何步骤失败，立即修复，不要跳过。

验证全部通过后，向用户报告结果。

## 注意事项

- **不要做过度工程**：脚手架只是起点，不要添加用户没要求的复杂配置
- **不要做业务功能**：只搭骨架，不写业务代码
- **尊重已有代码**：如果项目已有部分文件，不要覆盖，而是在现有基础上补充缺失部分
- **包管理器一致性**：如果项目已有 lock 文件，继续使用同一个包管理器
- **语言和注释**：用中文与用户沟通，但代码注释、commit message 等保持英文

