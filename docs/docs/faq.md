---
sidebar_position: 9
title: 常见问题
---

# 常见问题

## 首次登录 / Cookie 失效

**Q: 首次运行如何登录？**

使用 `giclaw run --no-headless` 启动，浏览器可见窗口会打开云原神页面。手动完成米哈游账号登录后，cookie 自动保存到 `~/.giclaw/cookies.json`，后续运行自动复用。

**Q: Cookie 失效了怎么办？**

重新使用 `--no-headless` 模式运行即可：

```bash
giclaw run --no-headless
```

程序会检测 cookie 无效并等待你重新登录。登录成功后 cookie 自动更新。

## Chromium 下载失败

**Q: 首次运行时 Chromium 下载失败或超时？**

giclaw 依赖 Playwright 内置的 Chromium。如果下载失败，可以尝试：

1. 检查网络连接，必要时配置代理
2. 手动安装 Playwright 浏览器：
   ```bash
   npx playwright install chromium
   ```
3. 如果在中国大陆，可以设置 Playwright 镜像：
   ```bash
   PLAYWRIGHT_DOWNLOAD_HOST=https://npmmirror.com/mirrors/playwright npx playwright install chromium
   ```

## API 报错

**Q: 运行时提示 API 错误（401、403、429 等）？**

- **401 Unauthorized**：API Key 无效或已过期，检查 `config.json` 中的 `model.apiKey`
- **403 Forbidden**：API Key 没有访问该模型的权限，确认模型名称正确
- **429 Too Many Requests**：请求频率超限，稍后重试或升级配额
- **5xx 服务端错误**：供应商临时故障，giclaw 会自动重试最多 2 次

运行 `giclaw run --dry-run` 可以快速验证 API 配置是否正确。

## 无头模式

**Q: 无头模式下无法登录？**

这是预期行为。无头模式（`headless: true`，默认）不显示浏览器窗口，无法进行手动登录。解决方法：

1. 首次使用 `--no-headless` 完成登录并保存 cookie
2. 后续即可使用默认 headless 模式运行

**Q: 如何始终使用可见浏览器？**

在 `config.json` 中设置：

```json
{
  "browser": {
    "headless": false
  }
}
```

或每次运行时加 `--no-headless`。

## 定时任务

**Q: Daemon 模式的定时任务不执行？**

检查以下几点：

1. 确认 daemon 进程正在运行：`giclaw daemon`
2. 检查 cron 表达式和时区配置：
   ```json
   {
     "schedule": {
       "cron": "0 6 * * *",
       "timezone": "Asia/Shanghai"
     }
   }
   ```
3. 确认系统时间和配置的时区一致
4. 查看日志输出确认调度器是否正常工作

## 技能执行

**Q: 技能执行超时？**

每个技能都有独立的超时时间（默认 10 分钟）。超时可能由以下原因导致：

- 网络延迟导致截图 / API 调用过慢
- AI 模型陷入循环操作（反复点击同一位置）
- 游戏加载时间过长

解决方法：
- 检查网络和 API 响应速度
- 在对应技能的 `SKILL.md` 中增加 `timeoutMs` 值
- 使用 `--no-headless` 模式观察实际执行过程，排查卡住的步骤

**Q: 某个技能失败但其他技能正常？**

技能按 `tasks.enabled` 顺序依次执行。如果某个技能失败，后续技能仍会继续执行。可以通过 `--tasks` 参数单独运行某个技能进行调试：

```bash
giclaw run --tasks claim-mail --no-headless
```

## 日志

**Q: 如何查看详细执行日志？**

- 运行时加 `-v` 或 `--verbose` 启用 debug 级别日志
- 或在 `config.json` 中设置 `"logLevel": "debug"`
- 每次运行的详细记录（transcript）保存在 `~/.giclaw/data/transcripts/` 目录
- 执行截图保存在 `~/.giclaw/data/screenshots/` 目录
