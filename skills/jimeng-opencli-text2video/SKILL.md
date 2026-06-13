---
name: jimeng-opencli-text2video
description: Guide Jimeng text-to-video for standard members without dreamina CLI. Use opencli jimeng generate-video (type=video), history, new, workspaces, and account commands. Use with jimeng-prompt-text2video for motion prompts. Never invoke dreamina or jimeng-cli execution skills.
license: Complete terms in LICENSE.txt
---

# jimeng-opencli-text2video — 即梦文生视频（opencli / 普通会员）

面向**无 dreamina CLI** 的普通会员。仅允许 `opencli jimeng` 子命令，**禁止** `dreamina text2video`、`query_result` 及 `jimeng-cli-text2video`。

视频 prompt 由 `jimeng-prompt-text2video` 提供；执行使用 `opencli jimeng generate-video`（`type=video` 工作台 DOM 自动化）。

## opencli 命令

| 子命令 | 用途 |
|--------|------|
| `generate-video <prompt>` | 文生视频（默认 `--wait=120`） |
| `generate-image2video <prompt>` | 图生视频（必填 `--image` 单张参考图） |
| `generate-audio` / `generate-digital-human` / `generate-action-copy` | 配音 / 数字人 / 动作模仿 |
| `history` | `--type=video` 核对作品 |
| `new --type=video` / `workspaces` | 会话管理 |
| `user_credit` | 积分检查 |

无 dreamina 的 `--duration`、`--model_version=seedance*` 等参数。

## 核心流程

```
1. PROMPT  → jimeng-prompt-text2video
2. SESSION → 可选 opencli jimeng new --type=video
3. GEN     → opencli jimeng generate-video "<prompt>" [--workspace ...] [--wait 180]
4. VERIFY  → opencli jimeng history --limit N --type=video
```

## 允许的 opencli 示例

```bash
opencli jimeng generate-video "镜头缓慢推进，橘猫跳下沙发" --wait=180
opencli jimeng new --type=video
opencli jimeng history --limit 10 --type=video --format json
opencli jimeng user_credit
```

## 禁止事项

- 不得使用 dreamina CLI 提交或轮询视频任务。
- 不得用 `generate`（`type=image`）冒充视频生成。

## 与 jimeng-cli-text2video

已开通 dreamina CLI 的用户使用 `jimeng-cli-text2video`；本技能读者**不得**改用 dreamina。

## 智能体规范

- 视频耗时长：多次 `history` 属正常，禁止 shell 死循环；由智能体分轮调用 `history`。
- 时长与动作复杂度匹配（见 prompt 技能）；网页侧由用户选择秒数。
- 积分不足、合规拦截在网页提示，opencli 无法 `user_credit` 查询。

## Gotchas

1. `history` 列主要为图生历史接口字段，视频条目以网页实际展示为准；无记录时以用户浏览器状态为准。
2. 普通会员以网页套餐与排队为准，与 dreamina VIP 队列无关。
3. 首尾帧 / 多分镜须网页操作；单图图生视频用 `generate-image2video --image`。
