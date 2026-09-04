---
name: omniget
description: Use when the user wants to download video, audio, playlists, courses they already own, or batches of links via OmniGet / omniget-cli (or asks for a yt-dlp-style download with a simpler CLI).
---

# OmniGet — download skill

Act: **preview then download** with `omniget` CLI. Do not dump contributor docs. Do not help bypass DRM or paywalls — only content the user’s own session can already open.

Upstream: https://github.com/tonhowtf/omniget  
Skill package: https://github.com/Audie-glitch/omniget-skill

## Goal

Given one or more URLs, get files onto disk (or report why not). Prefer CLI automation over telling the user to click the GUI.

## Preconditions

1. Find the binary: `omniget` or `omniget-cli` on `PATH`, or under a release extract / `cargo build --release -p omniget-cli`.
2. If missing: install from https://github.com/tonhowtf/omniget/releases/latest (`omniget-cli-<ver>-<target>`), or build from a clone, then continue.
3. Pick an output dir (ask once if unclear; default `~/Downloads/omniget` or `/workspace/downloads`).

## Workflow (always)

```text
info → confirm → download (or batch)
```

### 1) Preview (no download)

```bash
omniget info "<url>"
omniget --json info "<url>"    # for agents parsing title/formats/size
```

Use `--proxy URL` when the environment needs it.

### 2) Download one URL

```bash
omniget download "<url>" -o ~/Downloads/omniget
omniget download "<url>" -q 1080 -o ~/Videos
omniget download "<url>" --audio-only -o ~/Music
omniget download "<url>" --audio-only --subs en,pt
omniget download "<url>" --format mp4 -q 720
```

Flags: `-q/--quality` height, `-o/--output` dir, `--audio-only`, `--subs lang,list`, `--format mp4|mkv|webm`, global `--json`, `--proxy`.

### 3) Batch

Write one URL per line, then:

```bash
omniget batch links.txt -m 3 -o ~/Downloads/omniget
```

`-m` = max concurrent (default 3).

### 4) Auth / cookies (owned content only)

Netscape `cookies.txt` from the user’s browser session:

```bash
omniget import-cookies cookies.txt
omniget import-cookies cookies.txt --dry-run
omniget import-cookies cookies.txt -n my-account
```

Never invent credentials. Never coach DRM cracks.

## Decision rules

| User ask | Do |
|----------|-----|
| “What’s at this link?” | `info` (+ `--json` if you’ll summarize formats) |
| “Download this” | `info` if quality ambiguous, else `download` with sensible `-q` / `--audio-only` |
| Many links | `batch` |
| Course they bought | cookies + download; refuse paywall bypass |
| GUI preference | Point to app + hotkey `Ctrl+Shift+D` / `Cmd+Shift+D`; still offer CLI if they’re fine with files only |

## Report back

After a run, tell the user: output path, title (from info), success/fail, and the exact command used. On failure, paste the useful error line and one next step (proxy, cookies, quality, update via app).

## Out of scope

- Full OmniGet GUI development (Tauri/Svelte) — not this skill
- Fake engagement, starring repos for pay
- Bypassing DRM, logins you don’t have, or site ToS scrapers beyond normal omniget/yt-dlp use
