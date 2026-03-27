# git-commit-writer

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Conventional Commits](https://img.shields.io/badge/Conventional%20Commits-1.0.0-%23FE5196?logo=conventionalcommits&logoColor=white)](https://conventionalcommits.org)

**git-commit-writer** 是一个专为开发者设计的 AI 代理配置，旨在自动化生成符合 **Conventional Commits** 规范的 Git 提交消息。它通过智能分析代码差异、识别当前分支，并在用户确认后安全地执行提交操作，从而确保提交历史清晰、规范且安全。

## 主要特性

- **智能变更分析**：自动执行 `git diff` 分析未暂存或未提交的代码变更。
- **规范提交消息**：严格遵循 Conventional Commits 标准生成结构化提交信息（Header + Body）。
- **安全优先**：内置严格的安全限制，禁止推送、变基、强制推送等危险操作。
- **用户确认机制**：生成提交消息后必须经用户审核确认，方可执行 `git commit`。
- **精准范围控制**：支持全量提交或指定文件列表提交。
- **分支感知**：自动识别当前所在分支并在提交前告知用户。

## 配置说明

本代理基于 YAML 格式配置，适用于支持 Agent 定义的 AI 开发框架。以下是核心配置示例：

```yaml
---
agent-type: git-commit-writer
name: git-commit-writer
description: ......
model: glm-4.6
temperature: 0.1
tools:
  write: false
  edit: false
  bash: true
---
```

### 核心参数
- **model**: 默认使用 `glm-4.6`，可根据实际环境调整。
- **tools**: 仅启用 `bash` 工具以执行 Git 命令，禁用直接文件写入以确保安全。
- **temperature**: 设置为 `0.1` 以保证输出的稳定性和规范性。

## 工作流程

1. **获取变更**：自动检测所有未提交变更，或根据用户指定的文件列表获取差异。
2. **识别分支**：执行 `git branch --show-current` 获取当前分支名。
3. **生成消息**：基于 diff 内容生成符合规范的提交消息。
4. **用户确认**：展示消息和分支信息，等待用户确认（确认/修改）。
5. **暂存文件**：根据确认结果，执行 `git add` 操作（全量或指定文件）。
6. **执行提交**：执行 `git commit` 完成提交。

## 提交消息规范

生成的提交消息严格遵循以下格式：

```text
<type>(<scope>): <subject>
- <category>: <summary>
  * <detail_1>
  * <detail_2>
```

### 规范细节
- **Type**: 使用英文枚举 (feat, fix, docs, style, refactor, test, chore)。
- **Scope**: 受影响的主要模块名称（英文）。
- **Subject**: 中文简短概括，祈使句，无句号。
- **Body**: 
  - 分类标题（如：新增功能、修复问题）。
  - 细节列表需包含具体的 API 名、变量名或逻辑点。
  - **所有标点符号必须使用英文半角符号**。

## 安全限制

为了保障仓库安全，本代理**严禁**执行以下操作：

- ❌ `git push` 或任何推送操作
- ❌ `git rebase` 或任何变基操作
- ❌ `git push --force` / `-f` 强制推送
- ❌ `git reset --hard` 等丢失变更的操作
- ❌ 任何修改远程历史的操作

若用户请求上述操作，代理将明确拒绝并说明原因。

## 使用示例

### 场景：提交所有变更

**用户输入**:
```
帮我提交代码
```

**代理响应**:
```
当前分支: feature/user-profile

feat(api): 新增用户信息查询接口
- 新增功能：提供用户信息查询能力
  * 新增 getUserInfo 方法
  * 支持通过 userId 查询用户详情

请确认是否使用上述消息在当前分支上进行提交？（确认/修改）
```

**用户输入**:
```
确认
```

**代理操作**:
执行 `git add .` 及 `git commit -m "..."`，并返回提交结果。

### 场景：提交指定文件

**用户输入**:
```
只提交 src/auth/login.ts 文件
```

**代理操作**:
仅对该文件执行 `git diff`，生成消息，并在确认后将该文件单独暂存并提交。

## 贡献

欢迎提交 Issue 和 Pull Request 来改进此 Agent 配置。请确保您的贡献同样遵循安全规范。

## 许可证

本项目采用 [MIT](LICENSE) 许可证。
