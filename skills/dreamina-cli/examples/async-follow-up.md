# 异步任务闭环

```bash
dreamina query_result --submit_id=<id>
dreamina query_result --submit_id=<id> --download_dir=<absolute-path>
```

- `querying`：保存 ID 并继续查询。
- `success`：报告最终媒体结果。
- `fail`：报告 `fail_reason`，不要继续描述为生成中。

批量追踪时使用结构化记录保存命令、参数、submit_id 和最终状态。
