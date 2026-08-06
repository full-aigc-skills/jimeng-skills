# 文生视频模型选择（CLI v1.4.14）

| 模型 token | 分辨率 | 场景 |
|---|---|---|
| `seedance2.0fast` | 720p | 默认快速迭代 |
| `seedance2.0` | 720p | 标准高质量 |
| `seedance2.0mini` | 720p | 轻量方案 |
| `seedance2.0fast_vip` | 720p | VIP 快速通道 |
| `seedance2.0_vip` | 720p、1080p、4k | 高分辨率最终输出 |

文生视频不接受旧 3.x 或 Seedance 1.x token。每次都显式传
`--video_resolution`，并使用小写 `p`。
