# 视频命令路由

- 无参考媒体：加载 `jimeng-cli-text2video`。
- 单图动画、首尾帧、故事板或全能参考：加载 `jimeng-cli-image2video`。
- 先根据输入意图选择命令，再运行对应 `-h` 校验模型、时长和必填分辨率。

不要把 `multiframe2video` 与 `multimodal2video` 混用：前者是 2–20 张故事板，后者支持图、视频、音频组合。
