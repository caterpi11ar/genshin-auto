---
sidebar_position: 1
title: 快速开始
---

# 快速开始

Genshin Impact Claw（`giclaw`）是专为原神服务的智能体。通过视觉模型分析游戏截图，自动完成云原神的日常任务——登录、领取月卡、收取邮件、探索派遣、纪行奖励。

无需选择器、无需坐标硬编码。截图发给 AI，AI 决定下一步操作。

## 环境要求

- **Node.js >= 20**

## 安装

```bash
npm install -g giclaw@latest
# 或
pnpm add -g giclaw@latest
```

安装后全局可用 `giclaw` 命令。首次运行时自动下载 Chromium，无需手动安装。

## 交互式配置

```bash
giclaw init
```

`giclaw init` 会引导你选择模型提供商（Gemini、OpenAI、豆包、通义千问等）并配置 API key，配置保存到 `~/.giclaw/config.json`。

如果跳过 init 直接运行，程序会自动检测未配置状态并触发引导。

:::tip
在 CI 或非交互环境中，可以使用 `giclaw init --non-interactive` 创建默认配置文件，然后手动编辑 `~/.giclaw/config.json` 填入模型配置。
:::

## 首次运行

```bash
giclaw run --no-headless
```

首次运行必须使用 `--no-headless`，此时浏览器可见，你需要手动登录米哈游账号。登录成功后，cookie 会自动保存到 `~/.giclaw/cookies.json`。

登录完成后，giclaw 自动接管浏览器，依次执行已启用的技能（月卡领取 → 邮件收取 → 派遣收取 → 纪行奖励）。

## 后续运行

```bash
giclaw run
```

后续运行自动复用已保存的 cookie，以 headless 模式执行，无需手动操作。如果 cookie 过期，程序会提示你重新以 `--no-headless` 模式登录。

## 验证配置

```bash
giclaw run --dry-run
```

`--dry-run` 仅验证配置是否正确（模型连接、技能加载等），不会实际启动浏览器或执行任务。

## 从源码安装（开发）

<details>
<summary>展开查看</summary>

推荐使用 `pnpm`。

```bash
git clone https://github.com/caterpi11ar/giclaw.git
cd giclaw

pnpm install
pnpm build

# 单次运行
pnpm start

# Dev loop（auto-reload on TS changes）
pnpm dev
```

`pnpm dev` 通过 `tsx watch` 直接运行 TypeScript。`pnpm build` 产出 `dist/`，通过 `pnpm start` 或全局 `giclaw` 命令运行。

</details>
