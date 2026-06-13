<div align="center">

# jimeng-skills

**即梦 (Jimeng) AIGC skills — text-to-image, image-to-image, text-to-video, image-to-video via CLI and Prompt**

[![GitHub](https://img.shields.io/badge/github-full--aigc--skills%2Fjimeng-skills-green.svg)](https://github.com/full-aigc-skills/jimeng-skills)
[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](LICENSE)
[![Agent Skills](https://img.shields.io/badge/Agent%20Skills-Compatible-purple.svg)](https://agentskills.io)

English | [简体中文](./README.zh-CN.md)

[Introduction](#-introduction) · [Install](#-install) · [Skills](#-skills) · [Supported Agents](#-supported-agents) · [Ecosystem](#-ecosystem)

</div>

---

## 📖 Introduction

**jimeng-skills** is a curated collection of Agent Skills for AI coding agents, part of the [Full AIGC Skills](https://github.com/full-aigc-skills) ecosystem.

This package includes **12 skills**. Each skill is a self-contained `SKILL.md` file that AI agents load on-demand.

## 📦 Install

```bash
npx skills add full-aigc-skills/jimeng-skills
```

Or install specific skills: `npx skills add full-aigc-skills/jimeng-skills --skill <skill-name>`

## 🎯 Skills (12)

| Skill | Description |
|-------|-------------|
| `jimeng-cli-image2image` |  Provides comprehensive guidance for executing image-to-image (图生图) editing via the dreamina CLI for |
| `jimeng-cli-image2video` |  "Provides comprehensive guidance for executing image-to-video (图生视频) generation via the dreamina CL |
| `jimeng-cli-text2image` |  Provides comprehensive guidance for executing text-to-image generation via the dreamina CLI for 即梦  |
| `jimeng-cli-text2video` |  Provides comprehensive guidance for executing text-to-video generation via the dreamina CLI for 即梦  |
| `jimeng-opencli-image2image` |  Guide Jimeng image-to-image for standard members without dreamina CLI. opencli jimeng has no image2 |
| `jimeng-opencli-image2video` |  Guide Jimeng image-to-video for standard members without dreamina CLI. opencli jimeng has no image2 |
| `jimeng-opencli-text2image` |  Execute Jimeng text-to-image for standard (non-VIP) members using only opencli jimeng browser comma |
| `jimeng-opencli-text2video` |  Guide Jimeng text-to-video for standard members without dreamina CLI. Use opencli jimeng generate-v |
| `jimeng-prompt-image2image` |  "Provides comprehensive guidance for crafting image-to-image (图生图) edit prompts for 即梦 (Dreamina/Ji |
| `jimeng-prompt-image2video` |  "Provides comprehensive guidance for crafting image-to-video (图生视频) prompts for 即梦 (Dreamina/Jimeng |
| `jimeng-prompt-text2image` |  Provides comprehensive guidance for crafting text-to-image prompts for 即梦 (Dreamina/Jimeng) AI imag |
| `jimeng-prompt-text2video` |  "Provides comprehensive guidance for crafting text-to-video prompts for 即梦 (Dreamina/Jimeng) video  |

## 🤖 Supported Agents

Works with [Claude Code](https://code.claude.com), [Codex](https://developers.openai.com/codex), [Cursor](https://cursor.com), [OpenCode](https://opencode.ai), [Gemini CLI](https://geminicli.com), [GitHub Copilot](https://github.com/features/copilot), [Windsurf](https://codeium.com/windsurf), and [70+ others](https://agentskills.io/clients).

### Claude Code Installation

**Option 1: npx skills CLI (Recommended)**

```bash
npx skills add full-aigc-skills/jimeng-skills
```

**Option 2: Manual Installation**

```bash
git clone https://github.com/full-aigc-skills/jimeng-skills.git
cp -r jimeng-skills/skills/* .claude/skills/
```

For more details, see the [Claude Code Skills Guide](https://code.claude.com/docs/en/skills) and [Agent Skills Spec](https://agentskills.io/).

## 🌐 Ecosystem

| Resource | Link |
|----------|------|
| **Full AIGC Skills** | [github.com/full-aigc-skills](https://github.com/full-aigc-skills) |
| **Agent Skills Spec** | [agentskills.io](https://agentskills.io) |
| **Skills CLI** | [github.com/vercel-labs/skills](https://github.com/vercel-labs/skills) |

## 📄 License

Apache 2.0 — see [LICENSE](LICENSE).
