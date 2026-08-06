---
name: jimeng-cli-image2video
description: Use when the user provides image, video, or audio references and wants Dreamina 即梦 video generation through `image2video`, `frames2video`, `multiframe2video`, or `multimodal2video`. Covers CLI v1.4.14 mode routing, required video resolution, Seedance model sets, input limits, and async terminal statuses.
license: Complete terms in LICENSE.txt
---

# 即梦 CLI 图生视频

先按输入意图选命令，再运行对应的 `dreamina <command> -h`：

## When to use and boundary

用于至少有一张参考图或一个参考视频的 CLI 视频任务。不该用于纯文本视频、图片生成或纯提示词润色；这些场景改用对应的 `jimeng-cli-*` 或 `jimeng-prompt-*` 技能。

| 场景 | 命令 | 关键输入 |
|---|---|---|
| 单图动画 | `image2video` | `--image`、必填 `--prompt` |
| 首尾帧过渡 | `frames2video` | `--first`、`--last` |
| 2–20 张故事板 | `multiframe2video` | `--images` |
| 图/视频/音频全能参考 | `multimodal2video` | 至少一张图或一个视频 |

## v1.4.14 硬约束

- 所有四种模式都必须显式传 `--video_resolution`。
- 分辨率 token 使用小写 `720p`、`1080p`、`4k`。
- `multiframe2video` 固定模型，只接受 720p/1080p。
- 其他三种模式只有 `seedance2.0_vip` 可用 1080p/4k，其他模型仅 720p。
- `image2video` 支持 `seedance1.0fast`、`seedance1.5pro` 和 Seedance 2.0 家族。
- `frames2video` 支持 `seedance1.5pro` 和 Seedance 2.0 家族。
- `multimodal2video` 仅支持 Seedance 2.0 家族，音频不能作为唯一输入。

完整矩阵见 [`dreamina-cli` skill 的 v1.4.14 契约](https://github.com/full-aigc-skills/jimeng-skills/blob/main/skills/dreamina-cli/references/dreamina-cli-v1.4.14-contract.md)(如未安装:`npx skills add full-aigc-skills/jimeng-skills --skill dreamina-cli`)。

## 最小正确示例

```bash
dreamina image2video --image=./photo.png --prompt="镜头缓慢推近" --video_resolution=720p --poll=0
dreamina frames2video --first=./start.png --last=./end.png --prompt="季节自然变化" --video_resolution=720p --poll=0
dreamina multiframe2video --images=./a.png,./b.png --prompt="人物转身走向远处" --video_resolution=720p --poll=0
dreamina multimodal2video --image=./subject.png --audio=./music.mp3 --prompt="按音乐节奏运镜" --video_resolution=720p --poll=0
```

3 张以上多帧故事，每 N 张图提供 N−1 个 transition；单段时长 1–8 秒：

```bash
dreamina multiframe2video \
  --images=./a.png,./b.png,./c.png \
  --transition-prompt="从 A 走向 B" \
  --transition-prompt="从 B 走向 C" \
  --transition-duration=3 \
  --transition-duration=3 \
  --video_resolution=1080p \
  --poll=0
```

## 异步闭环

### Step 1：路由与验证

根据输入意图选择四个命令之一，验证文件可读，再用对应 help 校验模型、时长和分辨率。

### Step 2：提交

检查积分、说明消费影响、异步提交并保存 `submit_id`。

### Step 3：终态闭环

对 `querying` 持续调用 `query_result`，直到 `success` 或 `fail`。失败时报告
`fail_reason`。遇到 `AigcComplianceConfirmationRequired` 时提示用户先在 Web 端完成授权。

## Gotchas

1. **模式误路由**：单图、首尾帧、故事板、全能参考的参数名不同。
2. **分辨率遗漏**：四个命令都必须传 `video_resolution`。
3. **多帧越权**：multiframe 不接受 model_version，也不能用 4k。
4. **transition 数量**：N 张图需要 N−1 条 transition。
5. **音频单独提交**：multimodal 至少还要有一张图或一个视频。

## References

- [`dreamina-cli` skill 的 v1.4.14 参数契约](https://github.com/full-aigc-skills/jimeng-skills/blob/main/skills/dreamina-cli/references/dreamina-cli-v1.4.14-contract.md)(如未安装:`npx skills add full-aigc-skills/jimeng-skills --skill dreamina-cli`)
- [模式选择](references/mode-guide.md)
- [参数参考](references/parameter-reference.md)
- [VIP 指南](references/official-doc-vip-guide.md)
- [工作流模式](references/workflow-patterns.md)
- [示例](examples/single-image.md)
