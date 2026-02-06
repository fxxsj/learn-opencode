---
title: B5 专属开发 Agent
subtitle: 创建 Code Reviewer、Security Auditor
course: OpenCode 中文实战课
stage: 第四阶段
lesson: "B5"
duration: 25 分钟
practice: 30 分钟
level: 进阶
description: 创建专属 Code Reviewer 和 Security Auditor Agent，自动化代码审查和安全检查。
tags:
  - Agent
  - Code Review
  - 安全审计
prerequisite:
  - B1 开发日常
  - 3.2 认识 Agent
---

# B5 专属开发 Agent

> 💡 **一句话总结**：创建 Code Reviewer、Security Auditor、Test Writer 等专属开发 Agent。

## 📝 课程笔记

本课核心知识点整理：

<img src="/images/4-scenarios/coder-agents-notes.mini.jpeg"
     alt="B5 专属开发 Agent学霸笔记"
     data-zoom-src="/images/4-scenarios/coder-agents-notes.jpeg" />

---

## 学完你能做什么

- 创建 Code Reviewer Agent
- 创建 Security Auditor Agent
- 创建 Test Writer Agent
- 组合多个 Agent 形成开发工作流

---

## 你现在的困境

- 代码审查、安全检查、测试生成都要自己做
- 每次都要告诉 AI 用什么标准审查
- 想让多个"专家"协作，但不知道怎么实现

---

## 什么时候用这一招

- 当你需要：用专业化 Agent 提升开发质量
- 而且不想：每次都重复输入审查标准

---

## 🎒 开始前的准备

> 确保你已经完成以下事项：

- [ ] 完成了 [B1 开发日常](./coder-daily)
- [ ] 完成了 [3.2 认识 Agent](../3-workflow/02-agents)

---

## 核心思路

### 开发 Agent 矩阵

| Agent | 职责 | 使用场景 |
|-------|------|---------|
| Code Reviewer | 代码审查 | PR 审核、自检 |
| Security Auditor | 安全审计 | 上线前检查 |
| Test Writer | 测试生成 | 补充测试覆盖 |
| Doc Writer | 文档生成 | 注释、README |

---

## 跟我做

### 第 1 步：创建 Code Reviewer Agent

**为什么**  
专业的代码审查 Agent 能发现更多问题。

```bash
mkdir -p ~/.config/opencode/agent
```

> Agent 配置文件放置位置：
> - 全局：`~/.config/opencode/agent/`
> - 项目级：`.opencode/agent/`
>
> Agent 的调用名默认来自文件名：例如 `code-reviewer.md` 对应 `@code-reviewer`。

创建配置：

```
帮我创建一个 Code Reviewer Agent，保存到 ~/.config/opencode/agent/code-reviewer.md：

---
description: 严格的代码审查专家
mode: subagent
model: anthropic/claude-opus-4-5-thinking
temperature: 0.3
tools:
  write: false
  edit: false
  bash: false
---


# Code Reviewer Agent

你是一位经验丰富的高级工程师，专门负责代码审查。

## 审查清单

### 代码质量
- [ ] 函数职责单一
- [ ] 命名清晰准确
- [ ] 无重复代码
- [ ] 适当的注释

### 潜在问题
- [ ] 边界条件处理
- [ ] 错误处理完整
- [ ] 无内存泄漏风险
- [ ] 无竞态条件

### 可维护性
- [ ] 代码易于理解
- [ ] 可测试性好
- [ ] 符合项目规范

## 输出格式

对于每个问题，按以下格式输出：
- **位置**：文件名:行号
- **问题**：问题描述
- **严重程度**：高 / 中 / 低
- **建议**：修复建议
```

来源（Agent Markdown 字段与 tools 写法示例）：
- `opencode/packages/web/src/content/docs/agents.mdx:163`
- `opencode/packages/web/src/content/docs/agents.mdx:167`
- `opencode/packages/web/src/content/docs/agents.mdx:169`

### 第 2 步：创建 Security Auditor Agent
<AdInArticle />

**为什么**  
安全审计需要专门的视角。

```
帮我创建一个 Security Auditor Agent，保存到 ~/.config/opencode/agent/security-auditor.md：

---
description: 安全漏洞猎人
mode: subagent
model: anthropic/claude-opus-4-5-thinking
temperature: 0.2
tools:
  write: false
  edit: false
  bash: false
---

# Security Auditor Agent

你是一位安全专家，专门发现代码中的安全隐患。

## 检查项目

### 输入验证
- SQL 注入
- XSS 攻击
- 命令注入
- 路径遍历

### 认证授权
- 身份验证绕过
- 权限提升
- 会话管理

### 敏感数据
- 硬编码密钥
- 敏感信息泄露
- 不安全的存储

### 依赖安全
- 已知漏洞依赖
- 过时的包版本

## 输出格式

对于每个安全问题：
- **漏洞类型**：OWASP 分类
- **位置**：文件名:行号
- **风险等级**：Critical/High/Medium/Low
- **描述**：漏洞描述
- **修复建议**：如何修复
- **参考**：相关 CWE/CVE
```

### 第 3 步：创建 Test Writer Agent

**为什么**  
专门的测试 Agent 能生成更全面的测试。

```
帮我创建一个 Test Writer Agent，保存到 ~/.config/opencode/agent/test-writer.md：

---
description: 测试用例专家
mode: subagent
model: anthropic/claude-opus-4-5-thinking
temperature: 0.4
tools:
  write: false
  edit: false
  bash: false
---

# Test Writer Agent

你是一位测试专家，擅长设计和编写测试用例。

## 测试策略

1. **单元测试**：隔离测试每个函数
2. **集成测试**：测试模块间交互
3. **边界测试**：测试边界条件
4. **异常测试**：测试错误处理

## 测试覆盖

每个函数必须覆盖：
- 正常输入
- 边界值（最大、最小、临界）
- 非法输入（null、undefined、错误类型）
- 异常情况（网络错误、超时）

## 输出格式

使用项目的测试框架，生成可直接运行的测试代码。
```

### 第 4 步：使用专属 Agent

**为什么**  
调用专属 Agent 完成任务。

重启 OpenCode：

```bash
opencode
```

调用 Code Reviewer：

```
@code-reviewer @src/services/auth.ts 请审查这个认证模块
```

调用 Security Auditor：

```
@security-auditor @src/controllers/ 对这个目录进行安全审计
```

调用 Test Writer：

```
@test-writer @src/utils/validate.ts 为这个文件生成完整的测试用例
```

### 第 5 步：组合工作流

**为什么**  
多个 Agent 协作更高效。

创建一个综合审查命令 `.opencode/command/全面审查.md`：

```
---
description: 综合代码审查
---

请依次执行：
1. @code-reviewer 审查代码质量
2. @security-auditor 检查安全隐患
3. @test-writer 分析测试覆盖率

目标文件：$ARGUMENTS

最后汇总所有问题，按优先级排序。
```

来源（自定义命令目录与参数占位符）：
- `opencode/packages/web/src/content/docs/commands.mdx:20`
- `opencode/packages/web/src/content/docs/commands.mdx:113`
- `opencode/packages/opencode/src/config/config.ts:191`
- `opencode/packages/opencode/src/command/index.ts:49`

使用：

```
/全面审查 src/services/payment.ts
```

---

## 检查点 ✅

> 全部通过才能继续

- [ ] 创建了 Code Reviewer Agent
- [ ] 创建了 Security Auditor Agent
- [ ] 创建了 Test Writer Agent
- [ ] 能用 @agent名 调用

---

## 踩坑提醒

| 现象 | 原因 | 解决 |
|-----|-----|-----|
| @agent名 没反应 | 调用名和文件名不一致（或拼写错误） | 确认文件名（不含 .md）就是调用名，例如 `code-reviewer.md` → `@code-reviewer` |
| Agent 配置不生效 | 配置文件不在 agent 目录 | 放到 `~/.config/opencode/agent/`（全局）或 `.opencode/agent/`（项目级） |
| 多 Agent 结果不一致 | 各自独立执行 | 用命令串联，统一汇总 |

---

## 本课小结

你学会了：

1. 创建 Code Reviewer Agent
2. 创建 Security Auditor Agent
3. 创建 Test Writer Agent
4. 组合多个 Agent 形成工作流

---

## 下一课预告

> 下一课我们学习 **[B6 内网/离线部署](./coder-intranet)**。
>
> 你会学到：
> - 在企业内网环境运行 OpenCode
> - 禁用所有外网请求
> - 配置内部 AI 网关
