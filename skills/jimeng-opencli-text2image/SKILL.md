---
name: jimeng-opencli-text2image
description: Execute Jimeng text-to-image for standard (non-VIP) members using only opencli jimeng browser commands on jimeng.jianying.com. Covers generate, history, new, workspaces, user_credit, user_assets, user_subscription. Does not use dreamina CLI. Pair with jimeng-prompt-text2image for prompts. Use when the user cannot use dreamina and must automate via opencli Browser Bridge.
license: Complete terms in LICENSE.txt
---

# jimeng-opencli-text2image — 即梦文生图（opencli / 普通会员）

面向**未开通 dreamina CLI / 高级会员**、仅能用**即梦网页普通会员**能力的用户。本技能**只允许**调用 `opencli jimeng` 子命令，**禁止**调用 `dreamina` 或 `jimeng-cli-*` 执行类技能。

提示词由 `jimeng-prompt-text2image` 产出；本技能只负责 opencli 执行与会话管理。

## opencli jimeng 命令清单

| 子命令 | 作用 | 关键参数 / 输出 |
|--------|------|-----------------|
| `generate <prompt>` | 文生图 | `--model`、`--wait`、`--workspace`；列 `status`、`media_type`、`media_count`、`media_urls` |
| `generate-image2image <prompt>` | 图生图 | 必填 `--images`（逗号分隔路径，最多 10 张） |
| `history` | 最近作品 | `--limit`、`--workspace`、`--type`；列 `history_id`、`media_type`、`media_url` 等 |
| `new` | 新建 workspace | `--type`；列 `workspace_id`、`workspace_url`、`type` |
| `workspaces` | 工作区列表 | 列 `workspace_id`、`name`、`is_pinned`、`updated_at` |
| `user_credit` | 积分余额 | 列 `balance`、`vip_credit`、`gift_credit`、`purchase_credit` |
| `user_subscription` | 会员信息 | 列 `cur_vip_level`、`subscribe_type`、`end_time` 等 |
| `user_assets` | 资产库各 Tab | `--tab`、`--wait`；列 `tab`、`item_count`、`request_url` |

通用选项（各子命令）：`-f/--format`（table、plain、json 等）、`-v/--verbose`。策略：Cookie + Browser，域名 `jimeng.jianying.com`。

## 前置条件

1. 已安装 `opencli`，且 `opencli jimeng -h` 可列出上述四条子命令。
2. Chrome 已登录即梦，并安装 Browser Bridge（与 opencli 文档一致）。
3. 执行前用 `opencli jimeng history --limit 1` 或浏览器打开即梦，确认未掉线。

## 核心流程

```
1. PROMPT → jimeng-prompt-text2image 定稿并获用户确认
2. GEN    → opencli jimeng generate "<prompt>" [--model ...] [--wait ...]
3. CHECK  → 解析 status；timeout 时加大 --wait 或 history 对照
4. ORG    → 可选 new / workspaces 管理会话
```

## generate 用法

```bash
opencli jimeng generate "一只在星空下的猫"
opencli jimeng generate "赛博朋克城市夜景" --model high_aes_general_v50 --wait 60
opencli jimeng generate "水墨山水" --format json
```

| 参数 | 说明 |
|------|------|
| `prompt` | 位置参数，必填 |
| `--model` | 默认 `high_aes_general_v50`；可选 `high_aes_general_v42`（4.6）、`high_aes_general_v40`（4.0） |
| `--wait` | 等待出图秒数，默认 `40` |

`status` 为 `success` 时从 `media_urls` 取链接；`timeout` 时增加 `--wait` 或查 `history`；`failed` 时提示检查登录、积分（`user_credit`）或网页合规弹窗。

## 辅助命令

```bash
opencli jimeng history --limit 10
opencli jimeng new
opencli jimeng workspaces
```

## 与 jimeng-cli-text2image 的分工

| 用户条件 | 使用技能 |
|----------|----------|
| 仅有普通会员 / 无 dreamina CLI | **本技能**（仅 opencli） |
| 已安装 dreamina CLI、可 `user_credit` | `jimeng-cli-text2image` |

## 智能体规范

- 不得建议安装 `dreamina` 或执行 `dreamina text2image`。
- 不得用 shell `while true` 死循环；等待由 `generate --wait` 在浏览器内完成。
- 不得在本技能内改写 prompt。

## Gotchas

1. `generate` 固定打开 `type=image` 文生图页；不含图生图、视频参数。
2. `--model` 以 `opencli jimeng generate -h` 为准；若网页未切换模型，以页面实际选项为准。
3. 中文 prompt 通常效果更好。
4. `history` 的 `status` 为 `completed` / `pending`，与 `generate` 的 `success` / `timeout` 字段不同，勿混用。
