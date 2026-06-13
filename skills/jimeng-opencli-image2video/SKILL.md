---
name: jimeng-opencli-image2video
description: Guide Jimeng image-to-video for standard members without dreamina CLI. opencli jimeng has no image2video, frames2video, or multimodal subcommands. Use with jimeng-prompt-image2video for incremental motion prompts, manual mode selection on the Jimeng web UI, and opencli history for verification. Never invoke dreamina or jimeng-cli execution skills.
license: Complete terms in LICENSE.txt
---

# jimeng-opencli-image2video — 即梦图生视频（opencli / 普通会员）

面向**无 dreamina CLI** 的普通会员。仅允许 `opencli jimeng` 子命令，**禁止** `dreamina image2video`、`frames2video`、`multiframe2video`、`multimodal2video` 及 `jimeng-cli-image2video`。

增量运动 prompt 由 `jimeng-prompt-image2video` 提供；**图生视频 / 首尾帧 / 分镜 / 全能参考**在即梦网页由用户选择模式并上传素材后生成。

## opencli 命令

| 子命令 | 用途 |
|--------|----------------|
| `generate-image2video <prompt> --image <path>` | 单张参考图 + 运动 prompt（`type=video`） |
| `history` | 任务完成后核对 |
| `new` / `workspaces` | 会话管理 |

首尾帧、多分镜、多模态仍须在网页选手动模式；opencli **无** `--first`/`--last` 路由。

## 网页模式与 prompt 技能对齐

| 用户输入 | 网页侧（用户操作） | prompt 技能要点 |
|----------|-------------------|-----------------|
| 单张图 | 图生视频，上传 1 张 | 只写运动增量 |
| 首尾 2 张 | 首尾帧 / 过渡 | 描述过渡过程 |
| 多张分镜 | 多帧 / 故事板 | 叙事 + 转场 |
| 图 + 音视频 | 全能参考 / 多模态 | 合成关系说明 |

路由在**网页**完成；智能体用 prompt 技能协助文案，用 opencli 做会话与验收。

## 核心流程

```
1. PROMPT  → jimeng-prompt-image2video
2. SESSION → 可选 opencli jimeng new --type=video
3. GEN     → opencli jimeng generate-image2video "<motion prompt>" --image ./frame.png --wait 180
4. VERIFY  → opencli jimeng history --limit N --type=video（视频可长达十余分钟，分轮查询）
```

## 允许的 opencli 示例

```bash
opencli jimeng generate-image2video "镜头缓慢推进，发丝随风飘动" --image ./portrait.jpg --wait 180
opencli jimeng history --limit 10 --type=video
```

## 禁止事项

- 不得调用任何 dreamina 视频子命令或 `query_result` 轮询。
- 不得虚构 opencli 图生视频参数。
- 不得引导安装 dreamina。

## 与 jimeng-cli-image2video

已开通 dreamina CLI 的用户用 `jimeng-cli-image2video` 做子命令路由与轮询；本技能用户**不得**改用。

## 智能体规范

- prompt 只描述**动起来**的部分，不重复画面静态内容。
- 分轮 `history` 验收，禁止 shell 死循环。
- 单图图生视频用 `generate-image2video --image`；复杂模式仍引导用户在网页操作。

## Gotchas

1. 首尾帧 / 分镜 / 多模态须网页手动，不得虚构 opencli 参数。
2. 视频耗时长，默认 `--wait=120`，图生视频建议 180+。
3. 普通会员排队与积分以网页为准。
