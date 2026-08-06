---
name: jimeng-cli-text2image
description: Use when the user wants to submit, poll, or troubleshoot Dreamina 即梦 text-to-image tasks through `dreamina text2image`. Covers CLI v1.4.14 required resolution, custom width/height, Seedream model tokens, batch count, sessions, and async terminal status handling.
license: Complete terms in LICENSE.txt
---

# 即梦 CLI 文生图

执行前先运行 `dreamina text2image -h`。本技能记录 v1.4.14 稳定工作流；实际 help 始终是参数事实源。

## When to use and boundary

用于已经进入 CLI 执行、轮询或故障诊断阶段的文生图任务。不该用于图生图、视频生成或单纯的提示词创作；这些场景分别加载对应的 `jimeng-cli-*` 或 `jimeng-prompt-*` 技能。

## 必须遵守

- 每次提交显式传 `--resolution_type=1k|2k|4k`。
- `--width` 与 `--height` 必须成对出现、为正整数，并与 `--ratio` 互斥。
- 模型 token 使用 `5.0Pro`，不要写成人类可读名称 `5.0 Pro`。
- `generate_num` 范围为 1–10。
- 提交前说明会消耗积分；优先 `--poll=0` 获取 `submit_id`。

模型与分辨率矩阵、宽高像素限制见
[`dreamina-cli` skill 的 `references/dreamina-cli-v1.4.14-contract.md`](https://github.com/full-aigc-skills/jimeng-skills/blob/main/skills/dreamina-cli/references/dreamina-cli-v1.4.14-contract.md)。
如未安装,请先 `npx skills add full-aigc-skills/jimeng-skills --skill dreamina-cli`。

## 标准执行

```bash
dreamina user_credit
dreamina text2image \
  --prompt="一只橘猫坐在窗边，柔和晨光，浅景深" \
  --ratio=1:1 \
  --model_version=5.0 \
  --resolution_type=2k \
  --poll=0
```

自定义尺寸时省略 ratio：

```bash
dreamina text2image \
  --prompt="竖版产品海报，留出标题区域" \
  --model_version=5.0 \
  --resolution_type=2k \
  --width=1536 \
  --height=2048 \
  --poll=0
```

## 异步闭环

### Step 1：验证

运行 `dreamina text2image -h`，校验模型、分辨率、ratio 或自定义尺寸组合。

### Step 2：提交

检查积分、说明消费影响、提交并保存 `submit_id`。

### Step 3：终态闭环

`querying` 仅表示已受理；继续 `query_result`，直到 `success` 或 `fail`。失败时报告
`fail_reason`；需要文件时增加 `--download_dir=<absolute-path>`。

## Gotchas

1. **分辨率遗漏**：v1.4.14 会拒绝未传 `resolution_type` 的请求。
2. **宽高冲突**：自定义 width/height 不能与 ratio 同时出现。
3. **展示名误作 token**：`5.0 Pro` 必须转换为 `5.0Pro`。
4. **受理误作成功**：`querying` 不是成功终态。
5. **积分消耗**：真实提交前必须让用户知道会消费积分。

## References

- [`dreamina-cli` skill 的 v1.4.14 参数契约](https://github.com/full-aigc-skills/jimeng-skills/blob/main/skills/dreamina-cli/references/dreamina-cli-v1.4.14-contract.md)(如未安装:`npx skills add full-aigc-skills/jimeng-skills --skill dreamina-cli`)
- [参数参考](references/parameter-reference.md)
- [模型选择](references/model-guide.md)
- [工作流模式](references/workflow-patterns.md)
- [示例](examples/basic-generation.md)
