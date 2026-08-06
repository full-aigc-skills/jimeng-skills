# text2image 参数参考（CLI v1.4.14）

| 参数 | 必填 | 取值/约束 |
|---|---|---|
| `--prompt` | 是 | 非空提示词 |
| `--resolution_type` | 是 | 1k、2k、4k；须与模型匹配 |
| `--ratio` | 否 | 21:9、16:9、3:2、4:3、1:1、3:4、2:3、9:16 |
| `--width` + `--height` | 否 | 成对、正整数、与 ratio 互斥 |
| `--model_version` | 否 | 3.0、3.1、4.0、4.1、4.5、4.6、4.7、5.0、`5.0Pro` |
| `--generate_num` | 否 | 1–10 |
| `--session` | 否 | 非负会话 ID |
| `--poll` | 否 | 非负秒数；0 表示仅提交 |

尺寸与模型矩阵以
[`dreamina-cli` skill 的统一 v1.4.14 契约](https://github.com/full-aigc-skills/jimeng-skills/blob/main/skills/dreamina-cli/references/dreamina-cli-v1.4.14-contract.md)(如未安装:`npx skills add full-aigc-skills/jimeng-skills --skill dreamina-cli`)
为准。

```bash
dreamina text2image --prompt="产品海报" --ratio=3:4 --model_version=5.0 --resolution_type=2k --poll=0
```
