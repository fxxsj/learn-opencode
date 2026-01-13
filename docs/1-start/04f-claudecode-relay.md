---
title: 第三方中转（Claude Code 兼容）
subtitle: baseURL（.../v1）+ API Key（二选一）
course: OpenCode 中文实战课
stage: 第一阶段
lesson: "1.4f"
duration: 15 分钟
practice: 5 分钟
level: 新手
description: 使用第三方中转/网关连接 Anthropic 兼容接口，在 OpenCode 中使用 Claude 模型。
tags:
  - 模型
  - Claude
  - Anthropic
  - 中转
  - baseURL
prerequisite:
  - 1.2 安装
---

# 第三方中转（Claude Code 兼容）

这一页只做一件事：把“宣称兼容 Claude Code”的第三方中转，接到 OpenCode 里。

## 学完你能做什么

你将完成 3 件事：

1. 配一个独立 provider（例如 `claudecode-relay`），把 `baseURL` 指到中转的 `.../v1`
2. 配置 API Key（**二选一**：写在 `opencode.json` 或存到 `auth.json`）
3. 在 `/models` 里选择模型并发一句话验证

---

## 🎒 开始前的准备

- [ ] 完成了 [1.2 安装](./02-install)，能运行 `opencode`
- [ ] 你已经从中转服务拿到了 `baseURL` 和 `API Key`

从中转服务拿到两样东西：

1. **baseURL**：中转给你的接口地址
2. **API Key**：通常形如 `k-...`

baseURL 的填写规则（常见两种给法）：

1) 对方给到 `/v1` 就原样填：例如 `https://url.com/v1`
2) 只给域名就补 `/v1`：例如 `https://url.com` → `https://url.com/v1`

> 提示：很多 Claude/Anthropic 兼容中转的消息接口路径会包含 `.../v1/messages`，可用于你排查 baseURL 是否少了 `/v1`。

---

## 跟我做

### 第 1 步：在 opencode.json 配置一个独立 provider

编辑 `~/.config/opencode/opencode.json`，添加一个自定义 provider（这里用 `claudecode-relay` 作为 provider ID）：

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "claudecode-relay": {
      "npm": "@ai-sdk/anthropic",
      "options": {
        "baseURL": "https://url.com/v1"
      },
      "models": {
        "claude-opus-4-5-20251101": {
          "name": "中转站的 opus 4.5",
          "limit": {
            "context": 200000,
            "output": 64000
          },
          "options": {
            "thinking": {
              "type": "enabled",
              "budgetTokens": 16000
            }
          }
        }
      }
    }
  }
}
```

说明：

- `npm` 用来告诉 OpenCode：这个 provider 要按哪种"协议/SDK 驱动"发请求。Claude/Anthropic 兼容中转一般要用 `@ai-sdk/anthropic`；如果你删掉这行，OpenCode 可能会按默认的 OpenAI-compatible 方式去理解这个 provider，从而请求失败。
- 你也可以直接改 `provider.anthropic` 来走中转；那种写法本质上同样是在走 Anthropic 这套协议，但不建议（原因见文末"补充说明"）。
- 配置的 key（如 `claude-opus-4-5-20251101`）**既是**你在 OpenCode 里看到的"模型 ID"，**也是**发给中转商 API 使用的模型名
- `models.<key>.name` 是显示名称，可以随便写成中文（如"中转站的 opus 4.5"）
- `limit.context` 和 `limit.output` 定义模型的上下文窗口和最大输出长度。**如果不配置，默认值为 0，会导致自动压缩功能失效**。推荐值见下表。

| 模型 | context | output |
|-----|---------|--------|
| claude-opus-4-5 | 200000 | 64000 |
| claude-sonnet-4-5 | 200000 | 64000 |

> 💡 想深入了解上下文压缩机制和 `limit` 参数的作用？请参阅 [5.20 上下文压缩](/5-advanced/20-compaction)。

---

### 第 2 步：配置 API Key（**二选一**）

你只需要选下面一种方式配置 Key：

#### 方案 1：把 Key 写在 `opencode.json`（最简单）

##### 方式 A：直接写 Key（最快）

```jsonc
{
  "provider": {
    "claudecode-relay": {
      "options": {
        "baseURL": "https://url.com/v1",
        "apiKey": "你的API Key"
      }
    }
  }
}
```

直接把中转商给你的 Key 粘贴进去就行。

::: warning 安全提示
这样 Key 会明文保存在配置文件里。如果出于安全考虑，建议用方式 B。
:::

#### 方式 B：用环境变量（更安全）

如果你不想把 Key 明文写在配置文件里，可以用环境变量：

```jsonc
{
  "provider": {
    "claudecode-relay": {
      "options": {
        "baseURL": "https://url.com/v1",
        "apiKey": "{env:CLAUDECODE_RELAY_API_KEY}"
      }
    }
  }
}
```

然后在你的 shell 配置文件（`~/.bashrc` 或 `~/.zshrc`）里添加：

```bash
export CLAUDECODE_RELAY_API_KEY="你的API Key"
```

保存后运行 `source ~/.bashrc`（或重启终端）使环境变量生效。

#### 方案 2：把 Key 存到 OpenCode 认证（`auth.json`）

运行：

```bash
opencode auth login
```

在交互界面里：

1. 选择 `Other`
2. 输入 provider ID：`claudecode-relay`
3. 粘贴你的 API Key

它会把 Key 写入 OpenCode 的认证文件 `auth.json`。你可以用下面命令查看“实际写到了哪个路径”（命令输出里会显示路径）：

```bash
opencode auth list
```

::: warning 重要：不要两边都写
为了避免你“以为换了 Key 但没生效”，建议只保留一种方式。

- 如果你选择了方案 1（`opencode.json` 里有 `options.apiKey`），就不要再做 `auth login`。
- 如果你选择了方案 2（存到 `auth.json`），就不要在 `opencode.json` 里再写 `options.apiKey`。

原因：当 `opencode.json` 里存在 `options.apiKey` 时，会优先使用它。
:::

---

### 第 3 步：选择模型并验证

启动 OpenCode：

```bash
opencode
```

输入：

```text
/models
```

选择 `claudecode-relay/claude-opus-4-5-20251101`，然后发送一句话验证：

```text
你好，请简单介绍一下你自己
```

---

## 检查点 ✅

- [ ] `/models` 里能看到 `claudecode-relay/...` 并成功选择
- [ ] 发送消息能收到 AI 回复

---

## 踩坑提醒

| 现象 | 原因 | 解决 |
|-----|------|------|
| 404 / Not Found | baseURL 写错了 | 优先检查 `baseURL` 是否应该是 `.../v1`，并确认它能拼出 `.../v1/messages` |
| 401 / Unauthorized | Key 无效/没权限 | 重新生成 Key，或检查套餐/权限 |
| 选了模型就报错 | 模型名不支持 | 检查配置的 key（如 `claude-opus-4-5-20251101`）是否和中转商给的模型名完全一致 |

---

## 同时配置多个中转商

OpenCode 支持同时配置多个中转商，你可以在 `provider` 下添加多个独立的 provider：

```jsonc
{
  "provider": {
    "relay-a": {
      "npm": "@ai-sdk/anthropic",
      "options": {
        "baseURL": "https://relay-a.com/v1",
        "apiKey": "你的 A 中转商 Key"
      },
      "models": {
        "claude-opus-4-5-20251101": {
          "name": "A 中转的 opus 4.5",
          "limit": {
            "context": 200000,
            "output": 64000
          }
        }
      }
    },
    "relay-b": {
      "npm": "@ai-sdk/anthropic",
      "options": {
        "baseURL": "https://relay-b.com/v1",
        "apiKey": "你的 B 中转商 Key"
      },
      "models": {
        "claude-sonnet-4-5-20250514": {
          "name": "B 中转的 sonnet 4.5",
          "limit": {
            "context": 200000,
            "output": 64000
          }
        }
      }
    }
  }
}
```

配置后在 `/models` 里可以看到：
- `relay-a/claude-opus-4-5-20251101`
- `relay-b/claude-sonnet-4-5-20250514`

**使用场景**：
- 不同中转商价格/速度不同，按需切换
- 一个中转商挂了，快速切到另一个
- 测试对比不同中转商的效果

---

## 补充说明：为什么不建议直接改 `provider.anthropic`

你当然可以把中转写到 `provider.anthropic`，但不建议这么做：

- 会把你原本的 `anthropic` 配置也一起“换成中转”（不利于后续切回官方或对比排查）
- 更容易在 `opencode auth login` 时把 Key 存到错误的 providerID（例如误存成 `anthropic`），排查会变复杂

用一个独立 providerID（例如本章的 `claudecode-relay`）能把“中转配置”与“官方 anthropic 配置”隔离开。

---

## 下一步

- 回到 [1.4 总览](./04-connect) 选择下一条路线，或进入 [2.1 界面与基础操作](../2-daily/01-interface)

::: tip 遇到问题？
中转配置卡住了？[加入社群](/community)，和 500+ 同路人一起交流，实时答疑。
:::

