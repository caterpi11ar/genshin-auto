---
id: battle-pass-claim
name: Battle Pass Rewards Claim
description: Open the battle pass (Gnostic Hymn) interface and claim all available rewards.
enabled: true
timeoutMs: 300000
retries: 1
---

## Background

你正在原神游戏主界面中操作。游戏已启动并加载完毕，角色站在游戏世界中。
纪行（Battle Pass）可以通过按 F5 键或在派蒙菜单中找到入口打开。
纪行分为多个等级，完成任务会积累经验升级，每个等级有奖励可领取。

## Goal

打开纪行界面，领取所有可领取的等级奖励。

操作步骤：
1. 按 F5 键打开纪行界面（如果 F5 无效，打开派蒙菜单找到纪行入口）。
2. 查看纪行等级列表，找到有金色高亮或可点击状态的等级奖励。
3. 点击「一键领取」按钮领取所有可领取的奖励（如果有的话）。
4. 如果没有一键领取按钮，逐个点击可领取的等级奖励。
5. 领取完毕后关闭纪行界面，回到游戏主界面，任务完成。

如果没有可领取的奖励，直接关闭并报告 done success。

## Known Issues

- 纪行界面有多个标签页（任务、奖励等）——确保在「奖励」页面领取。
- F5 可能在云游戏中不生效——改用派蒙菜单进入。
- 领取奖励后出现物品展示弹窗——点击空白处或确认按钮关闭。
- 纪行可能在版本更新后重置——如果显示「纪行未开启」，报告 done success。
- 纪行免费轨道和付费轨道——只领取已解锁的（亮起的）奖励，灰色不可点击的跳过。
