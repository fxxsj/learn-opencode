---
title: 5.11 企业版功能
subtitle: 团队和企业特性
course: OpenCode 中文实战课
stage: 第五阶段
lesson: "5.11"
duration: 15 分钟
level: 进阶
description: 了解 OpenCode 企业版的部署选项和数据安全保障，支持团队协作。
tags:
  - 企业
  - 团队
prerequisite:
  - 5.1 配置全解
---

# 5.11 企业版功能

> 💡 **一句话总结**：了解 OpenCode 企业版的部署选项和数据安全保障。

## 📝 课程笔记

本课核心知识点整理：

<img src="/images/5-advanced/enterprise-notes.mini.jpeg"
     alt="5.11 企业版功能学霸笔记"
     data-zoom-src="/images/5-advanced/enterprise-notes.jpeg" />

---

## 学完你能做什么

- 了解企业版和开源版的区别
- 在企业试用阶段配置数据安全选项
- 了解正式部署选项

---

## 你现在的困境

- 个人用 OpenCode 很爽，想在团队推广
- 担心代码数据安全问题
- 不知道企业版有什么额外功能

---

## 什么时候用这一招

- 当你需要：确保代码和数据不离开企业基础设施
- 而且需要：中央化配置管理和 SSO 集成

---

## 企业版 vs 开源版

| 功能 | 开源版 | 企业版 |
|-----|-------|-------|
| 核心功能 | ✅ | ✅ |
| 本地处理（不存储代码） | ✅ | ✅ |
| 中央配置管理 | ❌ | ✅ |
| SSO 集成 | ❌ | ✅ |
| 内部 AI 网关集成 | ❌ | ✅ |
| 优先支持 | ❌ | ✅ |

> **来源**：[enterprise.mdx](https://opencode.ai/docs/enterprise)

---

## 开始试用

<AdInArticle />

OpenCode 是开源的，不存储任何代码或上下文数据，团队可以直接开始试用。

### 数据处理

**OpenCode 不存储你的代码或上下文数据。** 所有处理都在本地进行，或通过直接 API 调用发送到你选择的 AI 提供商。

这意味着只要你使用信任的提供商，或者使用内部 AI 网关，就可以安全地使用 OpenCode。

### 禁用分享功能

唯一需要注意的是可选的 `/share` 功能。如果用户启用分享，对话数据会被发送到 opencode.ai 托管的服务。

**企业试用建议禁用此功能：**

```jsonc
// opencode.json
{
  "$schema": "https://opencode.ai/config.json",
  "share": "disabled"
}
```

`share` 配置选项：

| 值 | 说明 |
|---|-----|
| `"manual"` | 手动分享（默认） |
| `"auto"` | 自动分享新会话 |
| `"disabled"` | 完全禁用分享 |

> **来源**：config.ts:790-795

### 代码所有权

**你拥有 OpenCode 生成的所有代码。** 没有任何许可限制或所有权声明。

---

## 正式部署

完成试用后，访问 [OpenCode 官网](https://opencode.ai) 讨论定价和实施方案。

### 中央配置

企业版可以为整个组织设置统一的中央配置。

中央配置可以：

- 与组织的 SSO 提供商集成
- 确保所有用户只能访问内部 AI 网关

### SSO 集成

通过中央配置，OpenCode 可以与组织的 SSO 提供商集成进行身份认证。

这使得 OpenCode 可以通过现有的身份管理系统获取内部 AI 网关的凭据。

### 内部 AI 网关

中央配置还可以让 OpenCode 只使用内部 AI 网关。

你可以禁用所有其他 AI 提供商，确保所有请求都通过组织批准的基础设施。

### 自托管

> ⚠️ **此功能在开发路线图中**

虽然建议禁用分享页面来确保数据不离开组织，但企业版也可以帮助在你的基础设施上自托管分享页面。

如有需要，请访问 [OpenCode 官网](https://opencode.ai) 联系。

---

## 定价说明

企业版采用按席位计费模式。

如果你有自己的 LLM 网关，不收取 token 使用费。

具体定价请访问 [OpenCode 官网](https://opencode.ai) 获取针对组织需求的报价。

---

## 企业环境配置

### enterprise.url 配置

企业版唯一的专属配置项：

```jsonc
// opencode.json
{
  "$schema": "https://opencode.ai/config.json",
  "enterprise": {
    "url": "https://your-company.opencode.internal"
  }
}
```

此 URL 用于企业版的分享服务等功能。

> **来源**：config.ts:922-926

### 私有 NPM 注册表

OpenCode 通过 Bun 原生支持 `.npmrc` 文件来使用私有 npm 注册表。

如果你的组织使用 JFrog Artifactory、Nexus 等私有注册表，确保开发者在运行 OpenCode 前已完成认证。

**登录私有注册表：**

```bash
npm login --registry=https://your-company.jfrog.io/api/npm/npm-virtual/
```

这会在 `~/.npmrc` 中创建认证信息，OpenCode 会自动使用它。

**手动配置 `.npmrc`：**

```bash
# ~/.npmrc
registry=https://your-company.jfrog.io/api/npm/npm-virtual/
//your-company.jfrog.io/api/npm/npm-virtual/:_authToken=${NPM_AUTH_TOKEN}
```

> ⚠️ **重要**：开发者必须先登录私有注册表，再运行 OpenCode。

---

## FAQ

<details>
<summary>什么是 OpenCode 企业版？</summary>

OpenCode 企业版适用于希望确保代码和数据永不离开内部基础设施的组织。它通过中央配置与 SSO 和内部 AI 网关集成来实现这一点。

</details>

<details>
<summary>如何开始使用企业版？</summary>

直接让团队开始内部试用即可。OpenCode 默认不存储代码或上下文数据，可以轻松开始。

然后访问 [OpenCode 官网](https://opencode.ai) 讨论定价和实施方案。

</details>

<details>
<summary>企业版如何定价？</summary>

我们提供按席位的企业定价。如果你有自己的 LLM 网关，不收取 token 使用费。

具体详情请访问 [OpenCode 官网](https://opencode.ai) 获取针对组织需求的定制报价。

</details>

<details>
<summary>企业版数据安全吗？</summary>

是的。OpenCode 不存储你的代码或上下文数据。所有处理都在本地进行，或通过直接 API 调用发送到你的 AI 提供商。

通过中央配置和 SSO 集成，你的数据安全地保留在组织基础设施内。

</details>

<details>
<summary>可以使用私有 NPM 注册表吗？</summary>

可以。OpenCode 通过 Bun 原生支持 `.npmrc` 文件。如果你的组织使用 JFrog Artifactory、Nexus 等私有注册表，确保开发者在运行 OpenCode 前已完成认证。

详见上方"私有 NPM 注册表"章节。

</details>

---

## 踩坑提醒

| 现象 | 原因 | 解决 |
|-----|-----|-----|
| 试用时分享功能仍可用 | 默认是 `"manual"` | 在 `opencode.json` 设置 `"share": "disabled"` |
| npm 包安装失败 | 未登录私有注册表 | 先执行 `npm login` 登录私有注册表 |
| 找不到企业版配置项 | 配置由中央配置管理 | 联系企业管理员获取配置 |

---

## 本课小结

你了解了：

1. 企业版的核心功能：中央配置、SSO、内部 AI 网关
2. **关键信息**：OpenCode 不存储代码或上下文数据
3. 如何在试用阶段禁用分享功能
4. 企业环境的配置选项

---

## 下一课预告

> 下一课我们把“企业环境最容易卡的那一步”补齐：怎么把公司的统一认证接进 OpenCode，做到自动拿 Token、自动下发默认配置。
>
> 下一课：**[5.11a 企业认证集成](./11a-enterprise-auth)**。
