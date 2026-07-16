---
name: jimeng-cli-text2image
description: Provides comprehensive guidance for executing text-to-image generation via the dreamina CLI for 即梦 (Dreamina/Jimeng). The dreamina CLI is ByteDance's command-line tool for submitting image generation tasks to 即梦 models (3.0 through 5.0), supporting credit management, synchronous and asynchronous task submission, result polling, and session-based project organization. Use when the user wants to run an approved prompt through the CLI to generate an image; mentions "dreamina", "即梦CLI", "命令行生图", "生成图片"; asks to run, submit, or execute a prompt; or needs credit checking, task status querying, session management, or result downloading. This skill covers installation verification, authentication, parameter mapping from prompt specifications, 4 standard execution workflows, and 6 error handling patterns from official documentation. Always use this skill when the user wants to generate images via the dreamina command line.
license: Complete terms in LICENSE.txt
---

# jimeng-cli-text2image — 即梦文生图 CLI 执行

Execute text-to-image generation via the `dreamina` CLI tool. This skill handles command construction, credit management, task submission, polling, and result retrieval.

**This skill does NOT craft prompts.** Prompt crafting is handled by `jimeng-prompt-text2image`. Use that skill first to create the prompt, get user approval, then activate this skill for CLI execution.

## When to use this skill

Use this skill when:
- The user has an approved prompt and wants to generate the image
- The user asks to "generate this image" or "run this prompt"
- The user needs to check credits, query task status, or download results
- The user mentions `dreamina` CLI commands explicitly
- The user wants to manage sessions, list tasks, or upscale images

Do NOT use this skill for:
- Writing or improving prompts → use jimeng-prompt-text2image
- Image-to-image CLI → use jimeng-cli-image2image
- Video CLI → use jimeng-cli-text2video / jimeng-cli-image2video

## Core Execution Flow

```
1. CHECK   → dreamina user_credit                  # Always first — check credits
2. SUBMIT  → dreamina text2image --prompt="..." --ratio=X:Y --poll=0  # Async → get submit_id
3. POLL    → Agent 每 ~5 秒手动调用 query_result --submit_id=<id> 检查 gen_status
4. RETRIEVE → gen_status="success" → 提取结果并报告
```

## 定时查询 SOP（智能体行为规范，非 shell 死循环）

提交生成任务后，应由**智能体（AI）**负责任务状态查询，而非用 `while true` shell 脚本阻塞终端：

### 步骤

**Step 1 — 提交任务（一次 terminal 调用）**：
```bash
dreamina text2image --prompt="..." --ratio=16:9 --poll=0
```
→ 解析输出中的 `submit_id`，记录下来。

**Step 2 — 智能体周期查询（多次 terminal 调用，每次单独）**：
```
每 ~5 秒执行一次: dreamina query_result --submit_id=<submit_id>
```
根据返回的 `gen_status` 做分支判断：

| gen_status | 智能体行为 |
|-----------|-----------|
| `"success"` | ✅ 提取结果 URL/文件，报告给用户 |
| `"failed"` | ❌ 报告错误信息给用户 |
| `"querying"` | ⏳ 等待 ~5 秒后再次调用 query_result（最长等待：图片 2 分钟，视频 15 分钟） |
| 长时间无变化（图片 >3min，视频 >20min） | ⚠️ 主动报告给用户，询问是否要继续等 |

> **禁止**在 terminal 中使用 `while true; sleep 5; ...` 死循环。应由智能体在多次对话轮次中调用 `query_result`，每次独立发起 terminal 调用。

### 示例（智能体自身逻辑）

```
# Turn 1
terminal: dreamina text2image --prompt="..." --poll=0
→ parse: submit_id = "abc-123"
→ tell user: "已提交任务，submit_id=abc-123，5秒后检查结果"

# Turn 2 (after ~5s)
terminal: dreamina query_result --submit_id=abc-123
→ gen_status = "querying"
→ tell user: "任务处理中，5秒后再检查"

# Turn 3 (after ~5s)
terminal: dreamina query_result --submit_id=abc-123
→ gen_status = "success"
→ extract result_url, report to user
```

> **原理**: 每次单独调用 terminal 而非 shell 死循环，智能体可以在每次查询结果后做灵活判断（重试、报告进度、超时处理），且不阻塞终端资源。

## How to use this skill

### Step 1: Verify prerequisites

Before any generation command, verify the CLI environment:

**1a. Check CLI is installed:**
```bash
dreamina -h
```
If not installed:
```bash
curl -fsSL https://jimeng.jianying.com/cli | bash
```

**1b. Check login status and credits:**
```bash
dreamina user_credit
```
This is the definitive self-check. If it returns JSON with credit info, login and environment are working.

If login needed:
```bash
dreamina login              # Opens browser for OAuth (default)
dreamina login --debug      # Debug mode — prints callback URL for troubleshooting
dreamina login --headless   # Headless mode — only prints verification_uri / user_code / device_code, no blocking wait
dreamina login checklogin --device_code=<device_code> --poll=30   # Check headless login result (poll=0 = check once)
```

Additional account commands:
```bash
dreamina relogin            # Switch to a different account (clears local OAuth state)
dreamina logout             # Clear OAuth credentials (keeps config.toml and tasks.db)
```

**1c. Check config file (if login issues persist):**
```bash
ls -la ~/.dreamina_cli/
```
> ⚠️ **Note**: The official docs reference `config.toml`, `credential.json`, and `tasks.db` at `~/.dreamina_cli/`, but these files may not exist after login — especially in Docker environments. Auth state can be stored ephemerally. If `ls` shows only `SKILL.md` and `version.json`, login is still valid if `dreamina user_credit` returns your balance. See the `dreamina-cli` skill's `references/docker-compatibility.md` for Docker-specific setup info.

### Step 2: Ensure prompt is ready

This skill requires a production-ready prompt. The prompt should already be:
- Crafted using jimeng-prompt-text2image methodology
- Reviewed and approved by the user
- In Chinese (即梦 works best with Chinese prompts)

If no prompt exists, activate `jimeng-prompt-text2image` first.

### Step 3: Map prompt parameters to CLI arguments

Take the prompt's recommendations and map them to CLI parameters:

| Prompt recommends | CLI parameter | Example |
|------------------|---------------|---------|
| Aspect ratio (e.g., 16:9) | `--ratio=16:9` | `--ratio=16:9` |
| Resolution (2K/4K) | `--resolution_type=2k` | `--resolution_type=4k` |
| Model version (4.5+) | `--model_version=5.0` | `--model_version=5.0` |
| Session organization | `--session=<id>` | `--session=0` |

Load `references/parameter-reference.md` for the complete parameter map.

### Step 4: Execute the generation

**Recommended (async + 智能体周期查询):**
```bash
dreamina text2image --prompt="<prompt>" --ratio=16:9 --model_version=5.0 --resolution_type=4k --poll=0
```
→ 解析获取 `submit_id`，智能体随后每 ~5 秒调用 `query_result` 检查状态。

**Quick poll (for fast tasks, auto 1s polling):**
```bash
dreamina text2image --prompt="<prompt>" --ratio=16:9 --model_version=5.0 --resolution_type=4k --poll=30
```

**With session (organized projects):**
```bash
dreamina session create "project-name"  # First time
dreamina text2image --prompt="<prompt>" --session=<session_id> --ratio=16:9 --poll=0
# Then follow 5s polling SOP using submit_id
```

**Batch generation (v1.4.10+):**
```bash
# Generate 4 images in one submission
dreamina text2image --prompt="同一场景的春、夏、秋、冬四张插画" --ratio=16:9 --model_version=5.0 --resolution_type=4k --generate_num=4 --poll=0
# → submit_id 回来后用 query_result 下载 zip 或逐张获取
```

### Step 5: Handle results

After generation completes:
- Confirm the `gen_status` is `"success"`
- If using the 5s 智能体周期查询 → 每次 `query_result` 返回后智能体根据 `gen_status` 做分支判断
- If using `--poll=N` and it completes → output is available immediately
- If still `"querying"` after reasonable wait time → ask user if they want to continue waiting

### Step 6: Handle errors

Common errors and responses — see `references/workflow-patterns.md` for detailed patterns.

| Error | Meaning | Action |
|-------|---------|--------|
| `AigcComplianceConfirmationRequired` | Model needs first-time web authorization | Guide user to authorize on dreamina website |
| Credit insufficient | Not enough balance | Tell user, suggest checking balance |
| `dreamina: command not found` | CLI not installed | Guide through installation |
| Authentication error | Not logged in | Run `dreamina login` |

## Key Parameters Quick Reference

| Parameter | Required | Values | Default |
|-----------|----------|--------|---------|
| `--prompt` | **Yes** | String (Chinese preferred) | — |
| `--ratio` | No | 21:9, 16:9, 3:2, 4:3, 1:1, 3:4, 2:3, 9:16 | 1:1 |
| `--model_version` | No | 3.0, 3.1, 4.0, 4.1, 4.5, 4.7, 5.0, **5.0 Pro** (v1.4.12+) | 5.0 |
| `--resolution_type` | No | 1k/2k (3.x), 2k/4k (4.0+) | Model default |
| `--generate_num` | No (v1.4.10+) | Integer 1-10 (batch image count) | 1 |
| `--session` | No | Integer session ID | 0 |
| `--poll` | No | Seconds (0 = async) | 0 (async) |

**Poll behavior**: When `--poll=N` is set, the CLI polls every 1 second for up to N seconds.
- If task completes within N seconds → final result returned immediately
- If N seconds elapse without completion → returns a "querying" status intermediate result; use `dreamina query_result --submit_id=<id>` to check later

## Essential CLI Commands

```bash
# Credit & auth
dreamina user_credit                    # Check credit balance
dreamina login                          # Login (OAuth browser, default)
dreamina login --headless               # Headless: print verification_uri/user_code/device_code, no wait
dreamina login checklogin --device_code=<dc> --poll=30   # Poll headless login status
dreamina relogin                        # Switch account (clears OAuth state)
dreamina logout                         # Clear OAuth credentials only

# Generation
dreamina text2image --prompt="..."                          # Basic generation
dreamina text2image --prompt="..." --ratio=16:9 --model_version=5.0 --resolution_type=4k --poll=30
dreamina text2image --prompt="..." --generate_num=4 --poll=0    # v1.4.10+ batch 4 images
dreamina text2image --prompt="..." --model_version=5.0 Pro --poll=30  # v1.4.12+ Seedream 5.0 Pro

# Task management
dreamina query_result --submit_id=<id>               # Check task status
dreamina query_result --submit_id=<id> --download_dir=./downloads  # Query + download
dreamina list_task                                   # List all tasks
dreamina list_task --gen_status=success              # Filter by status
dreamina list_task --submit_id=<id>                  # Filter by submit_id

# Session management (v1.3.5+: full CRUD + search)
dreamina session create "name"          # Create project session
dreamina session list                   # List recent sessions
dreamina session search "关键词"        # Search sessions by name
dreamina session rename <id> "新名字"   # Rename a session
dreamina session delete <id>            # Delete a session

# Utility
dreamina image_upscale --image ./x.png --resolution_type=4k   # Upscale image (4k/8k needs VIP)
dreamina version                                    # Show CLI version + build info
```

## Model Selection Guide

| CLI --model_version | Official Model ID | Resolutions | Best For |
|-------|-------------------|------------|----------|
| **5.0 Pro** (v1.4.12+, 🆕) | `doubao-seedream-5-0-pro` | 2k, 3k, 4k | Latest flagship Pro, all tasks. Default choice when available |
| **5.0** | `doubao-seedream-5-0-260128` | 2k, 3k, 4k | Latest stable flagship, everything. Use unless specific reason otherwise |
| 4.7 (v1.4.4+) | `seedream-4-7` | 2k, 4k | Latest pre-5.0 improvements |
| 4.5 | `doubao-seedream-4-5-251128` | 2k, 4k | Landscapes, illustrations, artistic. Good for creative/artistic prompts |
| 4.1 | — | 2k, 4k | Portrait editing, poster editing, multi-round editing |
| 4.0 | `doubao-seedream-4-0-250828` | 1k, 2k, 4k | General purpose multi-modal editing. Reliable baseline |
| 3.1 | — | 1k, 2k | Cinematic/storytelling, film genre prompts |
| 3.0 | — | 1k, 2k | Simple/short prompts, text rendering, fast generation |

Load `references/model-guide.md` for detailed model selection guidance.

## FAQ (from official documentation)

**Q: Login succeeds but generation commands still fail?**
A: Two checks: (1) verify `~/.dreamina_cli/config.toml` exists and is correct; (2) run `dreamina user_credit` — if this fails, login/config is the issue. Don't test generation commands until `user_credit` works.

**Q: Browser login flow is stuck?**
A: Use `dreamina login --debug` to print detailed debug info including the callback URL. Provide this output for troubleshooting.

**Q: Login shows "非法应用" (illegal application) error?**
A: Known issue reported in v1.4.x: when an Agent (Claude Code / Codex) runs `dreamina login` and prints the verification URL, opening it in browser may show "非法应用" (JSON format error). The CLI URL printed under Agent is wrong. **Workaround: complete login manually**:
1. Log in to jimeng.jianying.com web first
2. In your terminal run `dreamina login`, copy the URL it prints
3. Open URL in browser, click "授权" (Authorize)
4. CLI receives web login state; close authorization page when done
5. Now Agent can use CLI normally via query_result
Official team is still investigating root cause.

**Q: Async task shows no final result for a long time?**
A: Use `--poll=N` (recommend `--poll=30`) for auto-waiting. If timeout, save the `submit_id` from the intermediate result and query manually: `dreamina query_result --submit_id=<id>`.

**Q: How to switch or re-login to another account?**
A: Run `dreamina relogin` — it clears existing login state and starts a new login flow.

**Q: How to completely clear local login info?**
A: Run `dreamina logout` — this clears `credential.json` only. `config.toml` and `tasks.db` are preserved on native installs, but these files may be absent in Docker setups.

**Q: Headless login in non-interactive environments (CI/Agent)?**
A: Run `dreamina login --headless` to print verification_uri + user_code + device_code, do NOT block. Then poll with `dreamina login checklogin --device_code=<dc> --poll=30` (poll=0 = check once).

**Q: How to get CLI version for bug reports?**
A: `dreamina version` returns version + build info. Always include this when reporting issues, along with `~/.dreamina_cli/logs/` snippets and the full command you ran.

## Gotchas

1. **Always check credits first** — never run a generation without `dreamina user_credit`. Warn user if balance is low. If `user_credit` fails, do NOT proceed to generation — fix login/config first
2. **Prompt goes through prompt skill first** — don't craft prompts here. Route to jimeng-prompt-text2image for all prompt writing
3. **`~/.dreamina_cli/` is the config directory** — may contain `config.toml` (settings), `credential.json` (login), `tasks.db` (history) after native (non-Docker) login. In Docker setups these files may be absent. Don't delete this directory
4. **`--poll` polls every 1 second** — using `--poll=30` means up to 30 polling attempts, not a 30-second fixed wait. Timeout returns "querying" status, not failure
5. **Model 5.0 Pro is the latest flagship** (v1.4.12+, 2026-07-15) — default to it when available; fallback to 5.0 if user has older client
6. **Resolution must match model capability** — 1k only for 3.x, 2k for all, 4k for 4.0+
7. **Chinese prompts produce best results** — 即梦 is optimized for Chinese. If the user's prompt is in English, suggest translating
8. **`generate_num` batch (v1.4.10+) cap is 10** — one CLI call returns up to 10 images; download with `query_result --submit_id=<id> --download_dir=<dir>` when gen_status=success
9. **CLI auto-update check** (v1.3.1+) — CLI may print a new-version prompt at startup. Always upgrade before debugging strange errors: `curl -fsSL https://jimeng.jianying.com/cli | bash`. Bug reports without an upgrade first will likely be marked stale.
10. **升级 CLI 优先排查**（v1.4.x 习惯）—— 排查问题前先 curl 更新 CLI，再 reproduce 一次。官方明确"你的问题很可能在新版本已经解决"
11. **不要写 shell 死循环做任务轮询** — 提交任务时用 `--poll=0` 获取 submit_id，然后由智能体在对话轮次中每 ~5 秒手动调用一次 `query_result`。禁止 `while true; sleep 5;` 阻塞终端。智能体需根据每次返回的 gen_status 做分支判断（继续等/报告结果/通知超时）

## Available Resources

| Resource | Description | When to Load |
|----------|-------------|--------------|
| `references/parameter-reference.md` | Complete CLI parameter reference with all options | When mapping prompt specs to CLI arguments |
| `references/model-guide.md` | Detailed model selection guidance by use case | When choosing or recommending a model version |
| `references/workflow-patterns.md` | Standard execution patterns, error handling, async workflows | When executing complex or multi-step generations |
| `examples/basic-generation.md` | Simple single-image generation flows | Default — most common use case |
| `examples/batch-generation.md` | Multi-image, series, and batch patterns | User wants multiple images |
| `examples/async-generation.md` | Async submit + poll patterns | Long-running or non-blocking generations |
| `examples/session-workflow.md` | Session-based project organization | User wants organized project workflow |
