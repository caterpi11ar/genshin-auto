---
sidebar_position: 3
title: 模型配置
---

# 模型配置

giclaw 使用 **OpenAI 兼容的视觉模型 API** 分析游戏截图并决定操作。运行 `giclaw init` 可以交互式选择供应商，也可以手动在 `config.json` 中配置。

## 推荐供应商

### Google Gemini（推荐）

性价比最优。Gemini 2.5 Flash 具有出色的视觉理解能力，响应速度快，免费额度充足。

```json
{
  "model": {
    "name": "gemini-2.5-flash",
    "baseUrl": "https://generativelanguage.googleapis.com/v1beta/openai",
    "apiKey": "你的 API Key"
  }
}
```

获取 API Key：[Google AI Studio](https://aistudio.google.com/apikey)

### OpenAI

GPT-4o 视觉能力强大，适合需要高精度识别的场景。

```json
{
  "model": {
    "name": "gpt-4o",
    "baseUrl": "https://api.openai.com/v1",
    "apiKey": "sk-xxx"
  }
}
```

获取 API Key：[OpenAI Platform](https://platform.openai.com/api-keys)

### 豆包 / 火山引擎

国内访问稳定，无需代理。

```json
{
  "model": {
    "name": "doubao-seed-1.6-thinking-vision-250428",
    "baseUrl": "https://ark.cn-beijing.volces.com/api/v3",
    "apiKey": "你的 API Key"
  }
}
```

获取 API Key：[火山引擎控制台](https://console.volcengine.com/ark)

### 通义千问 Qwen-VL

阿里云提供，国内访问稳定。

```json
{
  "model": {
    "name": "qwen-vl-max",
    "baseUrl": "https://dashscope.aliyuncs.com/compatible-mode/v1",
    "apiKey": "sk-xxx"
  }
}
```

获取 API Key：[阿里云百炼](https://bailian.console.aliyun.com/)

## 自定义供应商

任何兼容 OpenAI `/v1/chat/completions` 接口且支持 `image_url` 类型输入的视觉模型均可使用。只需在 `config.json` 中填入对应的 `baseUrl`、`name` 和 `apiKey`。

```json
{
  "model": {
    "name": "your-model-name",
    "baseUrl": "https://your-api-endpoint.com/v1",
    "apiKey": "your-api-key"
  }
}
```

:::tip
giclaw 通过 `POST {baseUrl}/chat/completions` 发送请求，消息体包含 `image_url`（base64 编码的截图）和文本提示。确保你的供应商支持此格式即可。
:::

## 模型选择建议

| 供应商 | 模型 | 优势 | 注意事项 |
|--------|------|------|----------|
| Google Gemini | `gemini-2.5-flash` | 免费额度大，速度快 | 需要外网访问 |
| OpenAI | `gpt-4o` | 视觉精度高 | 价格较高，需要外网访问 |
| 豆包 | `doubao-seed-1.6-thinking-vision-250428` | 国内直连 | — |
| 通义千问 | `qwen-vl-max` | 国内直连 | — |
