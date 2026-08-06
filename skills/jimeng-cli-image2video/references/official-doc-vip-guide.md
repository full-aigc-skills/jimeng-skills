# 图生视频 VIP 契约（CLI v1.4.14）

- `image2video`、`frames2video`、`multimodal2video` 只有
  `seedance2.0_vip` 可选 1080p 或 4k。
- `seedance2.0fast_vip` 仍只支持 720p。
- `multiframe2video` 不接受 model_version，但可选 720p/1080p。
- token 使用小写 `720p`、`1080p`、`4k`。

```bash
dreamina frames2video --first=./start.png --last=./end.png --prompt="自然过渡" --model_version=seedance2.0_vip --video_resolution=1080p --poll=0
```
