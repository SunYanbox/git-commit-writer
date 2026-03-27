# git-commit-writer

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Conventional Commits](https://img.shields.io/badge/Conventional%20Commits-1.0.0-%23FE5196?logo=conventionalcommits&logoColor=white)](https://conventionalcommits.org)

**git-commit-writer** is an AI agent configuration designed for developers to automate the generation of Git commit messages following the **Conventional Commits** specification. It intelligently analyzes code diffs, identifies the current branch, and executes commit operations safely only after user confirmation, ensuring a clear, standardized, and secure commit history.

> **Language Note**: This Agent is designed for **Chinese environments by default**. The generated commit messages and interactive prompts are in Chinese. For use in English or other language environments, please modify the corresponding Markdown configuration file of the Agent accordingly.

## Key Features

- **Intelligent Change Analysis**: Automatically executes `git diff` to analyze unstaged or uncommitted changes.
- **Standardized Messages**: Generates structured commit messages (Header + Body) strictly adhering to Conventional Commits.
- **Security First**: Built-in strict security restrictions prohibiting push, rebase, force push, and other dangerous operations.
- **User Confirmation**: Requires user review and confirmation of the generated message before executing `git commit`.
- **Precise Scope Control**: Supports committing all changes or specific file lists.
- **Branch Awareness**: Automatically identifies the current branch and informs the user before committing.

## Configuration

This agent is configured via YAML, suitable for AI development frameworks supporting agent definitions. Below is the core configuration example:

```yaml
---
agent-type: git-commit-writer
name: git-commit-writer
description: ...
model: glm-4.6
temperature: 0.1
tools:
  write: false
  edit: false
  bash: true
---
```

### Core Parameters
- **model**: Defaults to `glm-4.6`, adjustable based on the environment.
- **tools**: Only `bash` is enabled for Git commands; direct file writing is disabled for security.
- **temperature**: Set to `0.1` to ensure output stability and standardization.

## Workflow

1. **Get Changes**: Automatically detect all uncommitted changes or get diffs based on user-specified file lists.
2. **Identify Branch**: Execute `git branch --show-current` to get the current branch name.
3. **Generate Message**: Generate a standardized commit message based on diff content.
4. **User Confirmation**: Display message and branch info, wait for user confirmation (Confirm/Modify).
5. **Stage Files**: Execute `git add` based on confirmation (all or specific files).
6. **Commit**: Execute `git commit` to finalize.

## Commit Message Specification

Generated commit messages strictly follow this format:

```text
<type>(<scope>): <subject>
- <category>: <summary>
  * <detail_1>
  * <detail_2>
```

### Specification Details
- **Type**: Use English enums (feat, fix, docs, style, refactor, test, chore).
- **Scope**: Main affected module name (English).
- **Subject**: Brief Chinese summary, imperative mood, no period at the end.
- **Body**: 
  - Category headers (e.g., New Features, Bug Fixes).
  - Detail lists must include specific API names, variable names, or logic points.
  - **All punctuation must use English half-width characters**.

## Security Restrictions

To ensure repository safety, this agent **strictly prohibits** the following operations:

- ❌ `git push` or any push operations
- ❌ `git rebase` or any rebase operations
- ❌ `git push --force` / `-f` force push
- ❌ `git reset --hard` or operations that lose changes
- ❌ Any operation modifying remote history

If a user requests these operations, the agent will explicitly refuse and explain the reason.

## 💡 Usage Examples

### Scenario: Commit All Changes

**User Input**:
```
Help me commit code
```

**Agent Response**:
```
Current Branch: feature/user-profile

feat(api): Add user info query interface
- New Features: Provide user info query capability
  * Added getUserInfo method
  * Support querying user details via userId

Please confirm if you want to commit using the above message on the current branch? (Confirm/Modify)
```

**User Input**:
```
Confirm
```

**Agent Action**:
Executes `git add .` and `git commit -m "..."`, then returns the commit result.

### Scenario: Commit Specific Files

**User Input**:
```
Only commit src/auth/login.ts file
```

**Agent Action**:
Executes `git diff` only on that file, generates the message, and stages/commits only that file after confirmation.

## Contributing

Issues and Pull Requests are welcome to improve this Agent configuration. Please ensure your contributions also follow security guidelines.

## License

This project is licensed under the [MIT](LICENSE) License.
