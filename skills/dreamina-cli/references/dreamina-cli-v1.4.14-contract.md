# Dreamina CLI v1.4.14 契约

> 来源：官方安装器 `curl -fsSL https://jimeng.jianying.com/cli | bash` 安装的
> 2026-07-21 构建（commit `b5ccc5d`）及各子命令 `-h`。执行真实任务前仍须重跑 help。

## 图片生成

| 命令 | 模型 | `resolution_type` |
|---|---|---|
| `text2image` | 3.0/3.1 | 1k、2k |
| `text2image` / `image2image` | 4.0/4.1/4.5/4.6/4.7/5.0 | 2k、4k |
| `text2image` / `image2image` | `5.0Pro` | 1k、2k、4k |

- `--resolution_type` 必填。
- `--width` 与 `--height` 必须成对提供、为正整数，并与 `--ratio` 互斥。
- 自定义尺寸边长/总像素同时受限：
  - 1k：边长 512–2016，总像素不超过 1,763,584；
  - 2k：边长 768–3072，总像素不超过 4,194,304；
  - 4k：边长 1536–6240，总像素不超过 16,777,216。
- `generate_num` 范围 1–10。
- CLI token 是 `5.0Pro`；“Seedream 5.0 Pro”只用于展示。

## 视频生成

| 命令 | 公开模型 | 分辨率 |
|---|---|---|
| `text2video` | Seedance 2.0 家族 | VIP 模型 720p/1080p/4k；其他 720p |
| `image2video` | `seedance1.0fast`、`seedance1.5pro`、Seedance 2.0 家族 | 同上 |
| `frames2video` | `seedance1.5pro`、Seedance 2.0 家族 | 同上 |
| `multimodal2video` | Seedance 2.0 家族 | 同上 |
| `multiframe2video` | 固定模型，不可覆写 | 720p、1080p |

- 所有视频生成命令的 `--video_resolution` 必填，token 使用小写 `720p`、`1080p`、`4k`。
- `image2video` 的 `--image`、`--prompt`、`--video_resolution` 均必填。
- `seedance1.0fast` 时长 5–10 秒；`seedance1.5pro` 时长 5–12 秒；
  Seedance 2.0 家族时长 4–15 秒。
- `multimodal2video` 至少包含一个 image 或 video；audio 不能单独提交。
- `multiframe2video` 接受 2–20 张图；单段时长 1–8 秒，总时长至少 2 秒。

## 异步状态

- `querying`：任务仍在进行，不是成功终态。
- `success`：成功终态。
- `fail`：失败终态；读取并报告 `fail_reason`。
- 不再把已失败任务继续标记为 querying；兼容读取历史别名 `failed`，但文档和新代码统一写 `fail`。

## 防漂移流程

1. 通过官方安装器升级 CLI。
2. 运行每个生成子命令的 `-h`。
3. 更新 SDK 的
   `src/test/resources/cli-contract/dreamina-v1.4.14-help.snapshot.tsv`。
4. 运行：

```bash
JAVA_HOME=<jdk17> mvn -Ddreamina.cli.contract.verify=true test
```

该门禁先比较 SDK 与提交快照，再把本机官方 CLI help 与同一快照逐项比较。
