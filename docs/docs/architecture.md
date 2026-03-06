---
sidebar_position: 6
title: 架构
---

# 架构

## 整体架构

```
┌─────────────────────────────────────────────────┐
│  skills/*.md          Markdown 技能定义          │
└────────────┬────────────────────────────────────┘
             │ load
┌────────────▼────────────────────────────────────┐
│            Gateway (control plane)               │
│  ┌──────────────────────────────────────────┐   │
│  │  Agent Loop (observe → think → act)      │   │
│  │  ┌──────────┐ ┌───────┐ ┌────────┐      │   │
│  │  │  Model   │ │ Tools │ │ Memory │      │   │
│  │  │ (Vision) │ │(Mouse)│ │ (JSONL)│      │   │
│  │  └──────────┘ └───────┘ └────────┘      │   │
│  └──────────────────────────────────────────┘   │
│  Scheduler · Queue · StateStore                  │
└──────────┬──────────────────┬───────────────────┘
           │                  │
    ┌──────▼──────┐   ┌──────▼──────┐
    │  TUI (ink)  │   │ Web (Fastify)│
    └─────────────┘   └─────────────┘
```

## 核心组件

- **Gateway** — 控制面板：加载技能、协调调度、管理队列、暴露 API
- **Agent Loop** — 核心执行循环：observe → think → act，直到技能报告 `done`
- **Model (Vision)** — 纯 HTTP 客户端，接收 base64 截图 + 文本提示，返回 JSON 操作指令
- **Tools** — Playwright 浏览器操作：click、scroll、type、press-key、wait
- **Memory** — JSONL transcript + 运行历史持久化
- **Scheduler** — Cron 调度器，按配置定时触发任务
- **Queue** — FIFO 任务队列，防止并发执行冲突

## Agent Loop 详细流程

每个技能的执行都经过相同的 Agent Loop：

```
                    ┌──────────────┐
                    │   开始技能   │
                    └──────┬───────┘
                           │
                    ┌──────▼───────┐
              ┌─────│   Observe    │
              │     │  截取浏览器  │
              │     │  全屏截图    │
              │     └──────┬───────┘
              │            │ base64 PNG
              │     ┌──────▼───────┐
              │     │    Think     │
              │     │  发送截图 +  │
              │     │  技能指令给  │
              │     │  视觉模型    │
              │     └──────┬───────┘
              │            │ JSON ActionPlan
              │     ┌──────▼───────┐
              │     │     Act      │
              │     │  执行操作    │
              │     │  click/wait/ │
              │     │  scroll/type │
              │     └──────┬───────┘
              │            │
              │     ┌──────▼───────┐
              │  NO │  action ==   │
              └─────│   "done"?    │
                    └──────┬───────┘
                           │ YES
                    ┌──────▼───────┐
                    │  记录结果    │
                    │  进入下一技能│
                    └──────────────┘
```

**视觉模型调用细节：**

1. 截图通过 Playwright 的 `page.screenshot()` 获取，编码为 base64 PNG
2. 截图 + 技能的 Background/Goal/Known Issues + 最近操作记录一起发送给模型
3. 模型通过 OpenAI 兼容的 `/chat/completions` 接口调用，使用 `image_url` 类型消息
4. 模型返回归一化坐标（0-999），系统自动转换为视口像素坐标
5. 支持的操作类型：`click`、`wait`、`scroll`、`type`、`press-key`、`done`
6. 内置重复点击检测——如果连续 3 次点击同一位置，提示模型更换策略
7. 单次 API 调用超时 2 分钟，失败自动重试最多 2 次

## 技能执行生命周期

```
加载                排队                执行                持久化
 │                  │                  │                   │
 ▼                  ▼                  ▼                   ▼
从 skillsDirs      进入 FIFO         启动浏览器          JSONL transcript
读取 SKILL.md  →  任务队列      →   登录 → 启动游戏  →  写入 data/transcripts/
解析 frontmatter   (maxDepth=10)     逐个执行技能         截图存入 data/screenshots/
+ Markdown 正文                      Agent Loop           运行摘要写入 state-store
```

**详细步骤：**

1. **加载**：`SkillRegistry` 从配置的 `tasks.skillsDirs` 目录中扫描 `SKILL.md` 文件，解析 YAML frontmatter（id、name、timeoutMs 等）和 Markdown 正文（Background、Goal、Known Issues）
2. **排队**：通过 CLI `run` 命令或 cron 触发时，任务进入 FIFO 队列。队列最大深度由 `queue.maxDepth` 控制，防止任务堆积
3. **执行**：
   - `SessionManager` 启动 Playwright Chromium 浏览器
   - `loginFlow` 加载 cookie，检测登录状态，必要时等待手动登录
   - `TaskRunner.runAll()` 按 `tasks.enabled` 顺序依次执行每个技能
   - 每个技能通过 Agent Loop 反复 observe→think→act，直到模型返回 `done` 或超时
4. **持久化**：每次运行生成 `RunSummary`，通过 `StateStore` 写入历史记录。`TranscriptWriter` 记录每一步的详细操作日志

## 目录结构

```
giclaw/
├── src/
│   ├── cli.ts              # CLI 入口（Commander.js）
│   ├── agent/              # Agent Loop 实现
│   ├── browser/            # Playwright 会话管理、登录流程
│   ├── config/             # 配置 schema（Zod）、路径、预设、交互式向导
│   ├── gateway/            # Gateway 控制面板、调度器、状态管理
│   ├── memory/             # Transcript 写入、State Store、运行历史
│   ├── model/              # 视觉模型客户端、Prompt 模板
│   ├── queue/              # FIFO 任务队列
│   ├── skills/             # 技能注册和加载
│   ├── tasks/              # 任务运行器
│   ├── tools/              # 浏览器操作工具（click、scroll 等）
│   ├── tui/                # TUI 仪表盘（ink）
│   ├── utils/              # 日志、进度事件
│   └── web/                # Web 面板（Fastify）
├── skills/                 # 内置技能（SKILL.md 文件）
├── docs/                   # 文档站点（Docusaurus）
└── dist/                   # 构建产物
```
