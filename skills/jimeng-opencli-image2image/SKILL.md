---
name: jimeng-opencli-image2image
description: Guide Jimeng image-to-image for standard members without dreamina CLI. opencli jimeng has no image2image subcommand; only generate, history, new, and workspaces are allowed. Use with jimeng-prompt-image2image for edit prompts, manual reference upload on the Jimeng web UI in the same browser session, and opencli history for verification. Never invoke dreamina or jimeng-cli execution skills.
license: Complete terms in LICENSE.txt
---

# jimeng-opencli-image2image — 即梦图生图（opencli / 普通会员）

面向**无 dreamina CLI** 的普通会员。本技能**仅允许** `opencli jimeng` 已注册的子命令，**禁止** `dreamina`、`jimeng-cli-image2image` 及任何 dreamina 安装说明。

编辑 prompt 由 `jimeng-prompt-image2image` 提供；图生图**上传与点击生成**在即梦网页完成（与 opencli 共用同一 Chrome 登录态）。

## opencli 命令

| 子命令 | 用途 |
|--------|------|
| `generate-image2image <prompt> --images <paths>` | 图生图：上传 1–10 张参考图 + prompt（`type=image`） |
| `history` | 提交后核对作品 |
| `new` / `workspaces` | 会话管理 |
| `user_credit` | 积分检查 |

`--images` 为英文逗号分隔的本地路径，最多 10 张。`generate` 仅文生图，**不含**参考图上传。

## 核心流程

```
1. PROMPT  → jimeng-prompt-image2image 定稿（Keep/Change）
2. SESSION → 可选 opencli jimeng new
3. GEN     → opencli jimeng generate-image2image "<prompt>" --images /path/a.png,/path/b.png [--wait 60]
4. VERIFY  → opencli jimeng history --limit N
```

## 允许的 opencli 示例

```bash
opencli jimeng generate-image2image "将背景改为日落海滩" --images ./ref.png --wait 60
opencli jimeng generate-image2image "保持人脸，换装红色礼服" --images ./a.jpg,./b.jpg --workspace 12958724505868
opencli jimeng history --limit 10 --format table
```

## 禁止事项

- 不得执行 `dreamina image2image`、`dreamina query_result` 等。
- 不得引导用户安装 dreamina CLI 作为回退。
- 不得用 `generate`（无 `--images`）冒充图生图。

## 与 jimeng-cli-image2image

已开通 dreamina CLI / 高级会员且可本地提交图生图任务时，改用 `jimeng-cli-image2image`；本技能用户**不要**混用。

## 智能体规范

- prompt 由 `jimeng-prompt-image2image` 负责；参考图路径由用户提供，由 `generate-image2image` 自动上传。
- `status=success` 时从 `media_urls` 取图；`timeout` 时加大 `--wait` 或查 `history`。
- 若用户坚持「全自动」：使用 `generate-image2image`，**仍不得**改用 dreamina。

## Gotchas

1. 多参考图用逗号分隔；单张过大（>15MB）会报错。
2. 2k/4k、4.0+ 模型可在网页侧再调，或 `--model`（页面会尝试切换）。
3. 编辑 prompt 应用 Keep/Change，避免未描述区域被改写。
