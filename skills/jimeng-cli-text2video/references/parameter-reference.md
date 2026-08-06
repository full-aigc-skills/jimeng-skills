# text2video 参数参考（CLI v1.4.14）

| 参数 | 必填 | 取值/约束 |
|---|---|---|
| `--prompt` | 是 | 非空提示词 |
| `--video_resolution` | 是 | 720p；仅 `seedance2.0_vip` 可用 1080p/4k |
| `--duration` | 否 | 4–15 秒，默认 5 |
| `--ratio` | 否 | 1:1、3:4、16:9、4:3、9:16、21:9 |
| `--model_version` | 否 | seedance2.0、seedance2.0fast、seedance2.0_vip、seedance2.0fast_vip、seedance2.0mini |
| `--session` / `--poll` | 否 | 非负数 |

```bash
dreamina text2video --prompt="镜头缓慢推进" --duration=5 --ratio=16:9 --model_version=seedance2.0fast --video_resolution=720p --poll=0
```
