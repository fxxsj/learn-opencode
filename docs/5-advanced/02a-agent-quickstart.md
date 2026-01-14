---
title: 5.2a Agent 快速入门
subtitle: 理解并创建你的第一个 Agent
course: OpenCode 中文实战课
stage: 第五阶段
lesson: "5.2a"
duration: 15 分钟
practice: 15 分钟
level: 进阶
description: 理解 Agent 的本质，创建你的第一个自定义 Agent，扩展 OpenCode 的能力。
tags:
  - Agent
  - 自定义
prerequisite:
  - 3.2 认识 Agent
---

# 5.2a Agent 快速入门

> 理解 Agent 的本质，创建你的第一个自定义 Agent。

## 📝 课程笔记

本课核心知识点整理：

<img src="/images/5-advanced/02a-agent-quickstart-notes.mini.jpeg" alt="Agent快速入门学霸笔记" data-zoom-src="/images/5-advanced/02a-agent-quickstart-notes.jpeg" />

---

## 学完你能做什么

- 理解 Agent 是什么、为什么需要它
- 区分 Primary Agent 和 Subagent
- 用 Markdown 创建自定义 Agent
- 用 JSON 创建自定义 Agent
- 切换和使用 Agent

---

## Agent 是什么

### Anthropic 的定义

根据 Anthropic 在《Building Effective Agents》中的定义：

| 类型 | 说明 | 特点 |
|------|------|------|
| **Workflow** | LLM 和工具通过预定义代码路径编排 | 步骤固定、可预测 |
| **Agent** | LLM 动态指导自己的流程和工具使用 | 自主决策、灵活应对 |

### OpenCode 中的 Agent

OpenCode 的 Agent 是**可配置的 AI 人格**，你可以定义：

- **身份**：它是谁、擅长什么
- **能力**：可以使用哪些工具
- **行为**：如何处理任务、有什么限制

### 为什么需要自定义 Agent

| 场景 | 不用 Agent | 用 Agent |
|------|-----------|---------|
| 代码审查 | 每次说"你是代码审查专家..." | 直接 `@reviewer` |
| 文档写作 | 每次说"用技术文档风格..." | 直接 `@docs-writer` |
| 安全审计 | 每次说"检查安全漏洞..." | 直接 `@security-auditor` |

---

## Agent 类型

OpenCode 有两类 Agent：

| 类型 | 说明 | 调用方式 |
|------|------|----------|
| **Primary** | 主 Agent，你直接交互的对象 | Tab 键切换 |
| **Subagent** | 子 Agent，被主 Agent 调用执行专项任务 | `@agent名` 或自动调用 |
| **All** | 混合模式，既可以作为主 Agent，也可以被调用 | Tab 切换或 @ 调用 |

### 它们如何协作

```
用户 ←→ Primary Agent (build/plan)
              ↓
         Task Tool (创建独立 Session)
              ↓
         Subagent (explore/general/你的自定义 Agent)
              ↓
         返回结果给 Primary
```

### 子代理的运行机制（重要）

理解子代理的运行机制对于设计高效 Agent 至关重要：

1. **Session 隔离（无历史记忆）**
   子代理运行在一个**全新的、独立的 Session** 中。这意味着：
   - **看不到主 Agent 的对话历史**：它不知道你之前和主 Agent 聊了什么。
   - **上下文仅包含 Prompt**：它的世界里只有你传给它的任务描述（Prompt）。
   - **必须提供完整上下文**：调用时必须把任务所需的所有信息都写在 prompt 里。

2. **All 模式的双重身份**
   当 `mode: "all"` 的 Agent：
   - **被 Tab 切换时**：它是主 Agent，拥有完整历史记忆。
   - **被 @ 调用时**：它是子 Agent，受到 Session 隔离限制，看不到调用者的历史。

---

## 内置 Agent 详解

<AdInArticle />

### 用户可见的 Agent

| 名称 | 模式 | 默认权限 | 说明 |
|------|------|---------|------|
| `build` | primary | 全部允许 | 默认开发 Agent，所有工具可用 |
| `plan` | primary | edit: deny（仅 `.opencode/plan/*.md` 允许） | 只读规划，不修改代码 |
| `general` | subagent | todoread/todowrite: deny | 通用研究，多步任务 |
| `explore` | subagent | 仅允许 grep/glob/list/bash/read/webfetch/websearch/codesearch | 快速代码探索 |

> **关于 Plan Agent**：它不是"需要确认才能编辑"，而是**默认禁止编辑**，只有 `.opencode/plan/*.md` 目录下的文件允许写入。这是为了让你在规划阶段专注思考，不被代码修改分心。
> 
> 来源：`agent.ts:69-83`

### 隐藏的内置 Agent

这些 Agent 你不会直接使用，但了解它们有助于理解系统：

| 名称 | 用途 | 说明 |
|------|------|------|
| `title` | 生成会话标题 | 使用 small_model |
| `summary` | 生成会话摘要 | 用于压缩 |
| `compaction` | 压缩上下文 | 当上下文过长时自动触发 |

> 来源：`agent.ts:122-166`

---

## 配置位置

| 位置 | 作用范围 | 优先级 |
|-----|---------|-------|
| `.opencode/agent/*.md` | 当前项目 | 高 |
| `~/.config/opencode/agent/*.md` | 全局所有项目 | 中 |
| `opencode.json` 的 `agent` 字段 | 取决于文件位置 | 与 Markdown 合并 |

> **文件名即 Agent 名称**：`docs-writer.md` 创建名为 `docs-writer` 的 Agent。

---

## 创建第一个 Agent（Markdown 方式）

### 基本结构

```markdown
---
description: 简短描述这个 Agent 做什么
mode: subagent
---

这里是系统提示词（System Prompt）。

告诉 Agent 它是谁、擅长什么、如何工作。
```

### 完整示例：文档写作 Agent

创建文件 `.opencode/agent/docs-writer.md`：

```markdown
---
description: |
  技术文档写作专家，擅长 API 文档、README、用户手册。
  适用场景：写新项目文档、更新现有文档、解释代码功能。
  不适用：代码审查、Bug 修复、功能实现。
mode: subagent
temperature: 0.3
---

# 角色

你是技术文档专家，擅长将复杂概念解释得通俗易懂。你的文档被评价为"看完就会用"。

# 文档规范

- 使用 Markdown 格式
- 代码示例必须可运行
- 包含输入/输出说明
- 中文优先，专业术语保留英文

# 文档结构

1. 概述（一句话说明是什么）
2. 快速开始（30 秒能跑起来）
3. 详细 API（完整参数说明）
4. 示例代码（覆盖常见场景）
5. 常见问题（预判用户疑惑）

# 工作原则

- 先理解代码，再写文档
- 不确定的地方要验证
- 保持风格一致

# 约束条件

- ✅ 快速开始的代码必须可直接复制运行
- ✅ 参数说明要包含类型和默认值
- ❌ 避免假设用户已有背景知识

# 错误处理

- 如果代码功能不明确，先询问或查阅相关源码
- 如果缺少上下文，列出需要补充的信息
- 如果遇到不熟悉的框架，声明并建议其他 Agent
```

### Frontmatter 字段速查表

| 字段 | 类型 | 必填 | 说明 |
|-----|------|-----|------|
| `description` | string | 建议填 | Agent 简介，影响自动选择 |
| `mode` | `primary` \| `subagent` \| `all` | 否 | 默认 `all` |
| `model` | string | 否 | 格式：`provider/model` |
| `temperature` | number | 否 | 0-1，控制创造性 |
| `top_p` | number | 否 | 0-1，核采样参数 |
| `steps` | number | 否 | 最大迭代步数 |
| `hidden` | boolean | 否 | 从 @ 菜单隐藏（仅 subagent） |
| `color` | string | 否 | 十六进制颜色，如 `#FF5733` |
| `permission` | object | 否 | 权限配置，详见 [5.2c](./02c-agent-permissions) |

> `maxSteps` 已废弃，请使用 `steps`。
> 
> 来源：`config.ts:484`

---

## 创建第一个 Agent（JSON 方式）

在 `opencode.json` 中配置：

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "agent": {
    "code-reviewer": {
      "description": "代码审查专家，专注安全、性能、可维护性。适用于 PR 审查、代码健康检查。",
      "mode": "subagent",
      "model": "anthropic/claude-sonnet-4-20250514",
      "temperature": 0.2,
      "steps": 30,
      "color": "#4CAF50",
      "prompt": "你是代码审查专家。\n\n## 检查要点\n- 安全漏洞（SQL注入、XSS、硬编码密钥）\n- 性能问题（时间复杂度、资源泄漏）\n- 代码风格（命名、结构、可读性）\n- 可维护性（耦合度、测试覆盖）\n\n## 输出格式\n- 🔴 严重问题（必须修复，说明风险和修复方案）\n- 🟡 建议改进（推荐修复，说明原因）\n- 🟢 优点（值得保持）\n\n## 约束条件\n- ✅ 问题要具体到行号\n- ✅ 每个问题都要有修复建议\n- ❌ 避免只批评不给方案"
    }
  }
}
```

### 使用外部 Prompt 文件

当 prompt 很长时，可以引用外部文件：

```jsonc
{
  "agent": {
    "code-reviewer": {
      "description": "代码审查专家",
      "mode": "subagent",
      "prompt": "{file:./prompts/code-reviewer.txt}"
    }
  }
}
```

> 路径相对于配置文件所在目录。

---

## 使用 Agent

### 切换 Primary Agent

按 **Tab** 键在 primary agent 之间切换（如 build ↔ plan）。

也可以使用快捷键 `<Leader>+a` 打开 Agent 列表选择。

### 调用 Subagent

**方式 1：手动 @ 提及**

```
@docs-writer 帮我写一个 README
```

**方式 2：自动调用**

主 Agent 会根据任务描述和 subagent 的 `description` 自动选择合适的 subagent。

> 这就是为什么 `description` 很重要——它决定了 Agent 何时被自动选中。

### 会话导航

当 subagent 创建子会话时：

| 快捷键 | 作用 |
|--------|------|
| `<Leader>+→` | 向前切换（父 → 子1 → 子2 → 父） |
| `<Leader>+←` | 向后切换 |
| `<Leader>+↑` | 返回父会话 |

---

## 禁用 Agent

在 `opencode.json` 中禁用不需要的内置 Agent：

```jsonc
{
  "agent": {
    "explore": {
      "disable": true
    },
    "general": {
      "disable": true
    }
  }
}
```

---

## 设置默认 Agent

启动 OpenCode 时默认使用哪个 primary agent：

```jsonc
{
  "default_agent": "plan"  // 默认使用 plan agent
}
```

> 如果不设置，默认是 `build`。
> 
> 来源：`config.ts:817-821`

---

## 踩坑提醒

| 现象 | 原因 | 解决 |
|-----|-----|-----|
| Agent 没出现 | 文件位置不对 | 确认在 `agent/` 目录下，不是 `agents/` |
| Agent 不遵守指令 | prompt 太长或太模糊 | 精简核心规则，结构化 |
| mode 不对 | 用了 `plan` 或 `build` | 应为 `primary` / `subagent` / `all` |
| description 报必填错误 | 版本问题 | 实际是可选的，建议还是填写 |
| maxSteps 不生效 | 已废弃 | 使用 `steps` 替代 |
| color 格式错误 | 不是十六进制 | 使用 `#RRGGBB` 格式 |
| 嵌套目录 Agent 名称 | 不知道怎么调用 | 名称包含路径：`folder/agent-name` |

---

## 本课小结

你学会了：

1. **Agent 本质**：可配置的 AI 人格
2. **两种类型**：Primary（主）和 Subagent（子）
3. **七个内置 Agent**：build、plan、general、explore + 3 个隐藏
4. **两种配置方式**：Markdown 和 JSON
5. **使用方式**：Tab 切换、@ 提及、自动调用

---

## 下一课预告

> 学会了创建 Agent，但如何设计一个**好用**的 Agent？下一课我们将学习 Agent 设计模式，包括业界最佳实践和实战案例。

**下一课**：[5.2b Agent 设计模式](./02b-agent-patterns)

::: tip 遇到问题？
Agent 配置卡住了？[加入社群](/community)，和 500+ 同路人一起交流，实时答疑。
:::
