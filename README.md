# OmniGet agent skill

Agent skill for [OmniGet](https://github.com/tonhowtf/omniget) — the free open-source desktop downloader (Tauri 2 + SvelteKit) for courses, YouTube, and 1,800+ sites.

This repo is a **thin skill package** so Cursor / Grok Bot / other agents can load OmniGet contributor and install guidance without cloning the full app.

## Install

### Cursor / agentskills-style

Copy or submodule the `omniget/` folder into your skills directory:

```text
skills/
  omniget/
    SKILL.md
```

Or point your agent at this repo and load [`omniget/SKILL.md`](./omniget/SKILL.md).

### Grok Bot

Ask your assistant to install the [omniget](sand-workflow:omniget) skill from this repo, or paste `omniget/SKILL.md` via your usual skill import.

## What’s inside

| Path | Purpose |
|------|---------|
| [`omniget/SKILL.md`](./omniget/SKILL.md) | When-to-use + stack, commands, UX/coding rules, install & CLI |
| [`SKILL.md`](./SKILL.md) | Same file at repo root for one-file loaders |

Content is distilled from OmniGet’s `AGENTS.md`, `llms.txt`, and README (install/CLI bits).

## Upstream

- App: https://github.com/tonhowtf/omniget
- License: GPL-3.0 (this packaging inherits GPL-3.0 because it derives from that project’s docs)

## Not included

The full OmniGet source, binaries, or plugins — only agent instructions. Clone upstream to develop the app.

```bash
git clone https://github.com/tonhowtf/omniget.git
```
