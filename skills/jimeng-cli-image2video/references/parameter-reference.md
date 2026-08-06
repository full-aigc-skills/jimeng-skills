# 图生视频参数参考（CLI v1.4.14）

| 命令 | 必填输入 | 模型 | 分辨率 |
|---|---|---|---|
| `image2video` | image、prompt | 1.0fast、1.5pro、2.0 家族 | 必填；VIP 可 1080p/4k |
| `frames2video` | first、last | 1.5pro、2.0 家族 | 必填；VIP 可 1080p/4k |
| `multiframe2video` | images（2–20） | 固定 | 必填；720p/1080p |
| `multimodal2video` | 至少 image 或 video | 2.0 家族 | 必填；VIP 可 1080p/4k |

非 VIP 模型仅支持 720p。模型 token、时长与输入上限见
[`dreamina-cli` skill 的统一 v1.4.14 契约](https://github.com/full-aigc-skills/jimeng-skills/blob/main/skills/dreamina-cli/references/dreamina-cli-v1.4.14-contract.md)(如未安装:`npx skills add full-aigc-skills/jimeng-skills --skill dreamina-cli`)。

```bash
dreamina image2video --image=./input.png --prompt="镜头推进" --video_resolution=720p --poll=0
```
