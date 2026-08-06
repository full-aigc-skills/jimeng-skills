---
name: jimeng-cli-image2image
description: Use when the user provides one to ten local images and wants to submit, poll, or troubleshoot Dreamina 即梦 image-to-image editing through `dreamina image2image`. Covers CLI v1.4.14 required resolution, custom width/height, Seedream model tokens, batch count, sessions, and async terminal statuses.
license: Complete terms in LICENSE.txt
---

# 即梦 CLI 图生图

执行前先运行 `dreamina image2image -h`。本技能记录 v1.4.14 稳定工作流；实际 help 始终是参数事实源。

## When to use and boundary

用于已有本地参考图、需要通过 CLI 编辑并跟踪结果的任务。不该用于文生图、视频生成或纯提示词润色；分别加载对应的执行或 prompt 技能。

## 必须遵守

- `--images` 接收 1–10 个本地文件，多个路径使用逗号分隔。
- 每次提交显式传 `--resolution_type=1k|2k|4k`。
- `--width` 与 `--height` 必须成对出现、为正整数，并与 `--ratio` 互斥。
- 仅 `5.0Pro` 支持 1k；4.x/5.0 只支持 2k 或 4k。
- 模型 token 是 `5.0Pro`，不是 `5.0Pro`。
- `generate_num` 范围为 1–10。

完整模型矩阵和像素限制见
[`dreamina-cli` skill 的 `references/dreamina-cli-v1.4.14-contract.md`](https://github.com/full-aigc-skills/jimeng-skills/blob/main/skills/dreamina-cli/references/dreamina-cli-v1.4.14-contract.md)。
如未安装,请先 `npx skills add full-aigc-skills/jimeng-skills --skill dreamina-cli`。

## 标准执行

```bash
test -r ./input.png
dreamina user_credit
dreamina image2image \
  --images=./input.png \
  --prompt="保留主体与构图，转换为透明水彩风格" \
  --model_version=5.0 \
  --resolution_type=2k \
  --ratio=1:1 \
  --poll=0
```

自定义尺寸时省略 ratio：

```bash
dreamina image2image \
  --images=./input.png \
  --prompt="改造成竖版电商海报" \
  --model_version=5.0 \
  --resolution_type=2k \
  --width=1536 \
  --height=2048 \
  --poll=0
```

## 异步闭环

### Step 1：验证

逐个验证输入文件可读，并用 help 校验模型、分辨率、ratio 或自定义尺寸组合。

### Step 2：提交

检查积分、说明消费影响、提交并保存 `submit_id`。

### Step 3：终态闭环

对 `querying` 持续调用 `query_result`，直到 `success` 或 `fail`。失败时报告
`fail_reason`，不要把 submit 成功当作生成成功。

## Gotchas

1. **文件数量**：只允许 1–10 张可读本地图片。
2. **分辨率遗漏**：v1.4.14 必须传 `resolution_type`。
3. **宽高冲突**：width/height 成对且与 ratio 互斥。
4. **1k 误用**：只有 `5.0Pro` 支持图生图 1k。
5. **状态误判**：`querying` 不是成功终态。

## References

- [`dreamina-cli` skill 的 v1.4.14 参数契约](https://github.com/full-aigc-skills/jimeng-skills/blob/main/skills/dreamina-cli/references/dreamina-cli-v1.4.14-contract.md)(如未安装:`npx skills add full-aigc-skills/jimeng-skills --skill dreamina-cli`)
- [参数参考](references/parameter-reference.md)
- [模型选择](references/model-guide.md)
- [工作流模式](references/workflow-patterns.md)
- [示例](examples/basic-generation.md)
