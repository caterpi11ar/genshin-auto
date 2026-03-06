# Genshin Impact Claw

专为原神服务的智能体。

通过视觉模型分析游戏截图，自动完成云原神的日常任务——登录、领取月卡、收取邮件、探索派遣、纪行奖励。无需选择器、无需坐标硬编码。截图发给 AI，AI 决定下一步操作。

[文档](https://giclaw.cn) · [快速开始](https://giclaw.cn/docs/getting-started) · [技能列表](https://giclaw.cn/docs/skills/overview)

## 特性

- **视觉 AI 驱动** — 纯截图理解，不依赖固定选择器或坐标
- **文件驱动技能** — 写一个 Markdown 就能定义新任务，无需 TypeScript
- **多模型支持** — Gemini、OpenAI、豆包、通义千问，任意 OpenAI 兼容视觉 API
- **Daemon 模式** — cron 定时调度 + TUI 仪表盘 + Web 面板
- **云游戏适配** — 无需本地安装原神客户端，低资源占用

## 安装

运行环境：**Node >= 20**

```bash
npm install -g giclaw@latest
```

## 快速开始

```bash
giclaw init                  # 交互式配置
giclaw run --no-headless     # 首次运行，手动登录
giclaw run                   # 后续运行，自动执行
```

详细指南请访问 [文档站点](https://giclaw.cn/docs/getting-started)。

## License

MIT
