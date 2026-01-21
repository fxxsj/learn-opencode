---
title: 安装：5 分钟搞定
subtitle: 一键安装到本地
course: OpenCode 中文实战课
stage: 第一阶段
lesson: "1.2"
duration: 5 分钟
practice: 5 分钟
level: 新手
description: 一键安装 OpenCode AI 编程助手，支持 macOS、Windows、Linux，5分钟完成安装配置。
tags:
  - 安装
  - macOS
  - Windows
  - Linux
prerequisite:
  - 1.1 这是什么
---

# 安装：5 分钟搞定

## 📝 课程笔记

本课核心知识点整理：

<img src="/images/1-start/install-notes.mini.jpeg" 
     alt="安装：5 分钟搞定学霸笔记" 
     data-zoom-src="/images/1-start/install-notes.jpeg" />

---

## 学完你能做什么

- 在终端输入 `opencode --version`，看到版本号

---

## 核心思路

安装 OpenCode 就三步：

1. 运行安装命令
2. 重启终端（让 PATH 生效）
3. 验证安装

---

## 跟我做
<AdInArticle />

### macOS / Linux 用户

复制下面这行命令，粘贴到终端，按回车：

```bash
curl -fsSL https://opencode.ai/install | bash
```

::: details 你会看到什么（成功的情况）
```
Installing OpenCode...

→ Detected: macOS arm64
→ Downloading opencode-darwin-arm64...
  ████████████████████████████████████████ 100%
→ Installing to ~/.opencode/bin/opencode...
→ Updating shell configuration...

✓ Installation complete!

To get started, restart your terminal and run:

  opencode
```
:::

看到 `Installation complete!` 就成功了。

---

### Windows 用户

Windows 推荐用 **Scoop** 安装（不需要管理员权限）。

**如果你已经有 Scoop：**

```powershell
scoop bucket add extras
scoop install extras/opencode
```

::: details 没装过 Scoop？点这里展开安装步骤
打开 **PowerShell**（推荐普通用户权限），**按顺序逐条运行**以下命令：

**第 1 步：允许运行脚本（只需执行一次）**

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

**第 2 步：安装 Scoop**

```powershell
Invoke-RestMethod -Uri https://get.scoop.sh | Invoke-Expression
```

> **⚠️ 以管理员身份运行？**
>
> 如果你的 PowerShell 是以**管理员身份**运行的（窗口标题带"管理员"字样），上面的命令会报错。需要改用这个命令：
>
> ```powershell
> iex "& {$(irm get.scoop.sh)} -RunAsAdmin"
> ```

**第 3 步：安装 Git（Scoop 需要 Git 来添加 bucket）**

```powershell
scoop install git
```

**第 4 步：安装 OpenCode**

```powershell
scoop bucket add extras
scoop install extras/opencode
```
:::

::: tip 其他 Windows 安装方式
如果你更习惯 Chocolatey、npm、Docker 等方式，请看 [1.2a 备用安装方式](./02a-install-alternatives)。
:::

---

### 重启终端

**为什么要重启？**

安装脚本修改了环境变量（PATH），但当前终端还在用旧的配置。重启后才能识别 `opencode` 命令。

**怎么重启：**

1. 关闭当前终端窗口（直接点叉，或者按 Cmd+Q / Alt+F4）
2. 重新打开终端

::: details 不想关窗口？
也可以手动重新加载配置：

**macOS/Linux (zsh)：**
```bash
source ~/.zshrc
```

**macOS/Linux (bash)：**
```bash
source ~/.bashrc
```

但我建议直接重启，更稳妥。
:::

---

### 验证安装

输入：

```bash
opencode --version
```

**你应该看到：**

```
opencode 1.1.6
```

版本号可能不同，但只要有输出就说明安装成功了。

---

## 检查点 ✅

> 全部通过才能继续

- [ ] 终端输入 `opencode --version` 能看到版本号
- [ ] 没有报 `command not found` 错误

---

## 遇到问题？

如果安装过程中遇到问题（找不到命令、网络超时、权限不足等），请看 [1.2b 装不上怎么办？](./02b-install-troubleshoot)

---

## 🚀 想立刻体验？（可选）

安装成功后，你可以直接启动 OpenCode 体验免费模型：

```bash
opencode
```

启动后，输入 `/models`，选择一个带 `-free` 后缀的免费模型（如 `opencode/glm-4.7-free`），然后发送任意消息即可对话。

::: tip 提示
免费模型无需注册账号、无需 API Key，适合快速体验。详见 [1.4a 免费模型](./04a-free-models)。
:::

---

## 本课小结

你学会了：

1. 用一行命令安装 OpenCode
2. 验证安装是否成功

---

## 下一课预告

> 下一课我们配置网络，让 OpenCode 能连上 AI 服务。
>
> **国内用户注意**：如果你打算用智谱、DeepSeek 等国产模型，可以跳过网络配置那一课，直接去"连接模型"。

::: tip 遇到问题？
安装过程中卡住了？[加入社群](/community)，和 500+ 同路人一起交流，实时答疑。
:::
