---
title: 5.21 思考深度配置
subtitle: 给大模型单独设定思考预算
course: OpenCode 中文实战课
stage: 第五阶段
lesson: "5.21"
duration: 18 分钟
practice: 12 分钟
level: 进阶
description: 学会用 opencode.json 为单个模型设定思考预算，并用 Ctrl+T 快捷键在不同深度间切换。
tags:
  - 配置
  - 模型
  - 思考
  - 快捷键
prerequisite:
  - 5.1 配置全解
---

# 5.21 思考深度配置

> 把“思考深度”当成挡位：想快就浅，想稳就深。

## 学完你能做什么

- 给某个模型单独设置思考预算（thinking budget）
- 理解“变体”机制如何控制思考深度
- 使用 <kbd>Ctrl</kbd>+<kbd>T</kbd> 在不同思考深度间切换

---

## 你现在的困境

- 同一个模型，有时想快、有时想深，但不知道怎么切
- 配置写进 `opencode.json` 后，不确定有没有生效
- 用中转站模型时，不确定是否还能控制思考深度

---

## 什么时候用这一招

- 当你需要：把“思考深度”做成可切换的档位
- 而且不想：每次换模型或改配置

---

## 🎒 开始前的准备

- [ ] 完成了 [5.1 配置全解](./01a-config-basics)
- [ ] 已能正常启动 OpenCode

---

## 核心思路

1. OpenCode 用 **模型变体（variants）** 保存不同思考深度
2. 变体是模型级配置，优先级高于默认值
3. <kbd>Ctrl</kbd>+<kbd>T</kbd> 会在变体之间循环切换

::: info ℹ️ 什么是“思考深度”？
它指的是模型的“可用思考预算”，例如 Anthropic 的 `thinking.budgetTokens`。
数值越大，模型可用于推理的 token 越多，但响应更慢、成本更高。
:::

---

## 跟我做

### 第 1 步：确认模型是否支持思考变体

**为什么**  不是所有模型都有变体，OpenCode 会先检查 `capabilities.reasoning`。

**怎么做**  选用支持 reasoning 的模型（如 Anthropic / Gemini 3 / OpenAI）。

**你应该看到**  模型列表中能出现 `high` / `max` 等变体。

---

### 第 2 步：在 opencode.json 里为单个模型设置思考预算

**为什么**  变体配置在 `provider.models.[modelID].variants` 下，可以覆盖默认值。

**怎么做**  按你使用的 Provider 填入对应字段：

**Anthropic 示例**（thinking.budgetTokens）

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "anthropic": {
      "models": {
        "claude-sonnet-4-5": {
          "variants": {
            "high": {
              "thinking": { "type": "enabled", "budgetTokens": 20000 }
            },
            "max": {
              "thinking": { "type": "enabled", "budgetTokens": 32000 }
            }
          }
        }
      }
    }
  }
}
```

**Gemini 3 示例**（thinkingConfig.thinkingBudget）

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "google": {
      "models": {
        "gemini-3-flash": {
          "variants": {
            "high": {
              "thinkingConfig": { "includeThoughts": true, "thinkingBudget": 16000 }
            },
            "max": {
              "thinkingConfig": { "includeThoughts": true, "thinkingBudget": 24576 }
            }
          }
        }
      }
    }
  }
}
```

**你应该看到**  重启后模型变体的数值生效。

---

### 第 3 步：用 Ctrl+T 在思考深度间切换

**为什么**  变体配置写好后，用快捷键快速切换更顺手。

**怎么做**  在对话输入框按 <kbd>Ctrl</kbd>+<kbd>T</kbd> 循环切换：

```
(无) → high → max → (无) → high → ...
```

**你应该看到**  状态栏显示当前变体名称（例如 `high`）。

---

### 第 4 步：自定义变体名字（可选）

**为什么**  变体名称不是固定的，你可以改成“深度思考/极速”等。

**怎么做**  在 `variants` 里使用自定义 key：

```jsonc
{
  "provider": {
    "anthropic": {
      "models": {
        "claude-sonnet-4-5": {
          "variants": {
            "极速": { "thinking": { "type": "enabled", "budgetTokens": 8000 } },
            "深度": { "thinking": { "type": "enabled", "budgetTokens": 32000 } }
          }
        }
      }
    }
  }
}
```

**你应该看到**  <kbd>Ctrl</kbd>+<kbd>T</kbd> 在“极速/深度”之间切换。

::: info ℹ️ 自定义变体是“增加”，不是“替换”
OpenCode 会把你在 `opencode.json` 里写的变体**合并**到默认变体中。
如果你只想保留自定义的变体，把默认的 `high`/`max` 显式禁用即可：

```jsonc
{
  "provider": {
    "anthropic": {
      "models": {
        "claude-sonnet-4-5": {
          "variants": {
            "high": { "disabled": true },
            "max": { "disabled": true },
            "极速": { "thinking": { "type": "enabled", "budgetTokens": 8000 } },
            "深度": { "thinking": { "type": "enabled", "budgetTokens": 32000 } }
          }
        }
      }
    }
  }
}
```
:::

**第三方中转站怎么配**

如果你的中转站是 `openai-compatible`，默认使用 `reasoningEffort`。示例：

```jsonc
{
  "provider": {
    "relay": {
      "options": {
        "baseURL": "https://your-relay.example.com/v1",
        "apiKey": "{env:RELAY_API_KEY}"
      },
      "models": {
        "gpt-5": {
          "variants": {
            "low": { "reasoningEffort": "low" },
            "high": { "reasoningEffort": "high" }
          }
        }
      }
    }
  }
}
```

如果中转站其实转发的是 Anthropic 接口（仍走 `openai-compatible` SDK），可以直接写 Anthropic 字段覆盖：

```jsonc
{
  "provider": {
    "relay": {
      "options": {
        "baseURL": "https://your-relay.example.com/v1",
        "apiKey": "{env:RELAY_API_KEY}"
      },
      "models": {
        "claude-sonnet-4-5": {
          "variants": {
            "high": {
              "thinking": { "type": "enabled", "budgetTokens": 20000 }
            },
            "max": {
              "thinking": { "type": "enabled", "budgetTokens": 32000 }
            }
          }
        }
      }
    }
  }
}
```

前提是：你的中转站服务端会把 `thinking` 字段原样转发到 Anthropic。

---

## 检查点 ✅

- [ ] `opencode.json` 中包含 `provider.models.[modelID].variants`
- [ ] 启动后可看到变体名称显示在状态栏
- [ ] <kbd>Ctrl</kbd>+<kbd>T</kbd> 能轮换变体

---

## 踩坑提醒

| 现象 | 原因 | 解决 |
|-----|------|------|
| 按 <kbd>Ctrl</kbd>+<kbd>T</kbd> 没反应 | 当前模型没有变体 | 换支持 reasoning 的模型或添加 variants | 
| 变体有但不显示 | 还未切换到某个变体 | 按一次 <kbd>Ctrl</kbd>+<kbd>T</kbd> | 
| 配置不生效 | 模型 ID 写错 | 从模型列表复制完整 ID | 
| 中转站没变化 | 用的是 `openai-compatible`，只支持 reasoningEffort | 在 variants 里手动覆盖参数 | 

---

## 本课小结

你学会了：

1. 变体是“思考深度档位”，在 `provider.models.[modelID].variants` 配置
2. 默认变体由 ProviderTransform 自动生成，可被配置覆盖
3. <kbd>Ctrl</kbd>+<kbd>T</kbd> 用于循环切换变体

---

## 下一课预告

> 🎉 **恭喜你完成了第五阶段！** 你已经掌握了 OpenCode 的深度定制能力。
>
> 接下来可以：
> - 回顾 [第五阶段目录](./index) 查阅其他参考手册
> - 进入 [第四阶段场景实战](/4-scenarios/) 学习具体应用
> - 查阅 [附录](/appendix/) 获取更多参考资料

---

## 附录：源码参考

<details>
<summary><strong>点击展开查看源码位置</strong></summary>

> 更新时间：2026-01-16

| 功能 | 文件路径 | 行号 |
|-----|---------|------|
| 变体生成入口 | [`src/provider/transform.ts`](https://github.com/anomalyco/opencode/blob/dev/packages/opencode/src/provider/transform.ts#L297-L477) | 297-477 |
| reasoning 过滤与排除 | [`src/provider/transform.ts`](https://github.com/anomalyco/opencode/blob/dev/packages/opencode/src/provider/transform.ts#L298-L301) | 298-301 |
| Anthropic 思考预算默认值 | [`src/provider/transform.ts`](https://github.com/anomalyco/opencode/blob/dev/packages/opencode/src/provider/transform.ts#L371-L385) | 371-385 |
| Gemini 3 思考预算默认值 | [`src/provider/transform.ts`](https://github.com/anomalyco/opencode/blob/dev/packages/opencode/src/provider/transform.ts#L421-L439) | 421-439 |
| 变体配置 Schema | [`src/config/config.ts`](https://github.com/anomalyco/opencode/blob/dev/packages/opencode/src/config/config.ts#L818-L833) | 818-833 |
| 变体配置合并 | [`src/provider/provider.ts`](https://github.com/anomalyco/opencode/blob/dev/packages/opencode/src/provider/provider.ts#L929-L936) | 929-936 |
| Ctrl+T 默认快捷键 | [`src/config/config.ts`](https://github.com/anomalyco/opencode/blob/dev/packages/opencode/src/config/config.ts#L632-L688) | 632-688 |
| Ctrl+T 命令绑定 | [`src/cli/cmd/tui/app.tsx`](https://github.com/anomalyco/opencode/blob/dev/packages/opencode/src/cli/cmd/tui/app.tsx#L393-L399) | 393-399 |
| 变体循环逻辑 | [`src/cli/cmd/tui/context/local.tsx`](https://github.com/anomalyco/opencode/blob/dev/packages/opencode/src/cli/cmd/tui/context/local.tsx#L310-L346) | 310-346 |
| 变体显示逻辑 | [`src/cli/cmd/tui/component/prompt/index.tsx`](https://github.com/anomalyco/opencode/blob/dev/packages/opencode/src/cli/cmd/tui/component/prompt/index.tsx#L696-L700) | 696-700 |
| 变体名称渲染 | [`src/cli/cmd/tui/component/prompt/index.tsx`](https://github.com/anomalyco/opencode/blob/dev/packages/opencode/src/cli/cmd/tui/component/prompt/index.tsx#L946-L950) | 946-950 |
| 变体应用到 LLM 参数 | [`src/session/llm.ts`](https://github.com/anomalyco/opencode/blob/dev/packages/opencode/src/session/llm.ts#L96-L109) | 96-109 |
| 变体 keybind 配置 | [`src/config/config.ts`](https://github.com/anomalyco/opencode/blob/dev/packages/opencode/src/config/config.ts#L632-L688) | 632-688 |

**关键常量**：
- `WIDELY_SUPPORTED_EFFORTS = ["low", "medium", "high"]`
- `OPENAI_EFFORTS = ["none", "minimal", "low", "medium", "high", "xhigh"]`

</details>
