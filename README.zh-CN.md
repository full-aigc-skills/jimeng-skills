<div align="center">

# jimeng-skills

**即梦 (Jimeng) AIGC 技能 — 文生图、图生图、文生视频、图生视频，CLI 与 Prompt 双通道**

[![GitHub](https://img.shields.io/badge/github-full--aigc--skills%2Fjimeng-skills-green.svg)](https://github.com/full-aigc-skills/jimeng-skills)
[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](LICENSE)
[![Agent Skills](https://img.shields.io/badge/Agent%20Skills-兼容-purple.svg)](https://agentskills.io)

[English](./README.md) | 简体中文

</div>

---

## 📖 简介

**jimeng-skills** 是一组 AI 编码智能体技能，属于 [Full AIGC Skills](https://github.com/full-aigc-skills) 生态。包含 **12 个技能**。

## 📦 安装

```bash
npx skills add full-aigc-skills/jimeng-skills
```

## 🎯 技能列表 (12)

| 技能 | 描述 |
|------|------|
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

## 🤖 支持的智能体

适用于 [Claude Code](https://code.claude.com)、[Codex](https://developers.openai.com/codex)、[Cursor](https://cursor.com)、[OpenCode](https://opencode.ai)、[Gemini CLI](https://geminicli.com)、[GitHub Copilot](https://github.com/features/copilot)、[Windsurf](https://codeium.com/windsurf) 及 [70+ 其他](https://agentskills.io/clients)。

### Claude Code 安装

**方式一：npx skills CLI（推荐）**

```bash
npx skills add full-aigc-skills/jimeng-skills
```

**方式二：手动安装**

```bash
git clone https://github.com/full-aigc-skills/jimeng-skills.git
cp -r jimeng-skills/skills/* .claude/skills/
```

## 📄 License

Apache 2.0
