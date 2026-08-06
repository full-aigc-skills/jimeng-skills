# Multi-Frame Story — 多帧故事执行

## 两帧快捷模式

```bash
dreamina user_credit
test -r ./start.png
test -r ./end.png

dreamina multiframe2video \
  --images=./start.png,./end.png \
  --prompt="人物从门口走到窗边，镜头平稳跟随" \
  --duration=3 \
  --video_resolution=720p \
  --poll=0
```

## 四帧故事板

N 张图必须提供 N−1 个 transition prompt；时长可以同样逐段提供。

```bash
dreamina user_credit
test -r ./step1.png
test -r ./step2.png
test -r ./step3.png
test -r ./step4.png

dreamina multiframe2video \
  --images=./step1.png,./step2.png,./step3.png,./step4.png \
  --transition-prompt="从取出产品过渡到在手背点涂" \
  --transition-prompt="从点涂过渡到均匀推开" \
  --transition-prompt="从推开过渡到展示吸收后的光泽" \
  --transition-duration=3 \
  --transition-duration=3 \
  --transition-duration=3 \
  --video_resolution=1080p \
  --poll=0
```

`multiframe2video` 不接受 `--model_version`，单段时长必须为 1–8 秒。
