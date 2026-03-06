---
sidebar_position: 2
title: 编写技能
---

# 编写技能

## SKILL.md 格式

每个技能是一个 Markdown 文件，包含 YAML frontmatter 和结构化正文：

```markdown
---
id: my-skill
name: My Skill Name
description: One-line English description for logs and API.
enabled: true
timeoutMs: 600000
retries: 1
---

## Background
场景背景描述（中文）。告诉 AI 当前看到的是什么界面、有哪些元素。

## Goal
任务目标和操作步骤（中文）。越具体越好，列出分步操作指南。

## Known Issues
- 已知问题 1——处理方式。
- 已知问题 2——处理方式。
```

## Frontmatter 字段

| 字段 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `id` | string | **必填** | 唯一标识，与目录名一致 |
| `name` | string | **必填** | 显示名称 |
| `description` | string | **必填** | 英文简述，用于日志和 API |
| `enabled` | boolean | `true` | 是否可被加载 |
| `timeoutMs` | number | `600000` | 单次执行超时（毫秒） |
| `retries` | number | `1` | 失败重试次数 |

## Markdown 正文

按 `## ` 标题分段，解析为 `TaskDescription`：

| 标题 | 映射字段 | 说明 |
|------|----------|------|
| `## Background` | `background` | AI 看到的场景上下文 |
| `## Goal` | `goal` | AI 需要完成的目标及操作步骤 |
| `## Known Issues` | `knownIssues` | 每行 `- ` 开头的已知问题列表 |

## 完整示例：claim-mail

下面以内置技能 `claim-mail`（邮件领取）为例，展示一个完整的 `SKILL.md` 从零编写的过程。

### 第一步：创建目录

```bash
mkdir skills/claim-mail
```

### 第二步：编写 SKILL.md

```markdown title="skills/claim-mail/SKILL.md"
---
id: claim-mail
name: Claim Mail Rewards
description: Open the in-game mailbox and claim all pending mail rewards.
enabled: true
timeoutMs: 300000
retries: 1
---

## Background
你正在操作原神游戏的主界面。游戏已经启动，角色站在游戏世界中。

屏幕左上角区域有 Paimon 图标，附近通常有邮件图标（信封形状）。
如果有未读邮件，邮件图标上会有红点提示。

## Goal
打开游戏内邮箱，领取所有未读邮件的附件。

1. 在屏幕左上角区域找到邮件图标（信封形状，靠近 Paimon 图标），点击打开邮箱。
2. 如果邮件列表中有"全部领取"按钮，直接点击一键领取所有附件。
3. 如果没有"全部领取"，逐个点击未读邮件，点击"领取附件"按钮。
4. 领取完成后，关闭邮箱界面，回到游戏主画面。
5. 如果邮件图标上没有红点（没有新邮件），直接报告 done success。

## Known Issues
- 邮件图标上没有红点说明没有新邮件，直接报告 done success，不需要打开邮箱。
- 邮件列表为空时，直接关闭邮箱报告 done success。
- 领取附件后可能弹出物品展示窗口，需要点击关闭或点击空白处关闭后再继续。
- 邮件列表可能很长需要滚动，如果看到的邮件都已经领取过了，尝试向下滚动查找更多。
- 如果主界面有其他弹窗遮挡邮件图标，先关闭弹窗（点击关闭按钮或按 Escape）。
```

### 第三步：注册技能

在 `config.json` 的 `tasks.enabled` 中添加：

```json
{
  "tasks": {
    "enabled": ["welkin-moon", "claim-mail", "expedition-collect", "battle-pass-claim"]
  }
}
```

### 第四步：验证

```bash
giclaw run --dry-run
```

确认技能被正确加载后，使用 `--no-headless` 模式实际运行观察效果：

```bash
giclaw run --tasks claim-mail --no-headless
```

## 编写技巧

- **Background** 要描述 AI 在截图中会看到什么，帮它定位当前状态。越具体越好——描述屏幕区域、UI 元素的位置和外观。
- **Goal** 写成分步操作指南（1、2、3…），明确每一步该点什么、找什么。包含"如果...则..."的条件分支，帮 AI 处理不同场景。
- **Known Issues** 列出容易踩坑的 UI 场景和正确处理方式。这是提升技能稳定性最重要的部分——每次发现 AI 执行出错的场景，都应该补充到这里。
- 所有 AI 指令用中文编写（prompt 模板为中文）。
- 如果任务在某些条件下无需执行（如没有新邮件），在 Goal 中明确说明直接报告 `done success`。
- `timeoutMs` 根据任务复杂度调整——简单任务 5 分钟足够，复杂任务（如派遣收取）建议 10 分钟。

## 添加新技能

1. 创建目录：`mkdir skills/my-skill`
2. 编写 `skills/my-skill/SKILL.md`
3. 在 `config.json` 的 `tasks.enabled` 中添加 `"my-skill"`
4. 运行验证：`giclaw run --dry-run`
