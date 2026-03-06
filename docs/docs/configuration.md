---
sidebar_position: 3
title: 配置
---

# 配置

推荐通过 `giclaw init` 交互式引导完成模型配置。也可以手动编辑配置文件。

两层覆盖优先级：`config.json` < CLI 参数。

## config.json

```json
{
  "model": {
    "name": "gemini-2.5-flash",
    "baseUrl": "https://generativelanguage.googleapis.com/v1beta/openai",
    "apiKey": "sk-xxx",
    "family": "gemini"
  },
  "tasks": {
    "enabled": ["welkin-moon", "claim-mail", "expedition-collect", "battle-pass-claim"],
    "skillsDirs": ["./skills"]
  },
  "schedule": { "cron": "0 6 * * *", "timezone": "Asia/Shanghai" },
  "browser": { "headless": true },
  "logLevel": "info"
}
```

所有配置统一在 `~/.giclaw/config.json` 中管理。`giclaw init` 交互式引导会自动写入该文件。也可以在项目根目录放置 `config.json`，程序启动时优先加载。

## 配置项说明

| 配置项 | 说明 | 默认值 |
|--------|------|--------|
| `model.name` | 模型名称 | - |
| `model.baseUrl` | OpenAI 兼容 API 地址 | - |
| `model.apiKey` | API 密钥 | - |
| `model.family` | 模型系列（`gemini`、`openai` 等） | - |
| `tasks.enabled` | 启用的技能 ID 列表 | 所有技能 |
| `tasks.skillsDirs` | 技能目录路径 | `["./skills"]` |
| `schedule.cron` | Cron 表达式 | `"0 6 * * *"` |
| `schedule.timezone` | 时区 | `"Asia/Shanghai"` |
| `browser.headless` | 是否无头模式 | `true` |
| `logLevel` | 日志级别（`debug`、`info`、`warn`、`error`） | `"info"` |
