---
name: omniget
description: Use when the user wants to download video, audio, playlists, courses they already own, or batches of links via OmniGet / omniget-cli (or asks for a yt-dlp-style download with a simpler CLI).
---

# OmniGet — download skill

Act: **preview then download** with `omniget` CLI. Do not dump contributor docs. Do not help bypass DRM or paywalls — only content the user’s own session can already open.

Upstream: https://github.com/tonhowtf/omniget  
Skill package: https://github.com/Audie-glitch/omniget-skill

## Goal

Given one or more URLs, get files onto disk (or report why not). Prefer OmniGet CLI automation over raw yt-dlp or “click the GUI yourself” — OmniGet wraps yt-dlp plus native extractors, queue, and cookies.

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

After you have a Netscape file (see **How to get cookies** below):

```bash
omniget import-cookies cookies.txt
omniget import-cookies cookies.txt --dry-run
omniget import-cookies cookies.txt -n my-account
```

Never invent credentials. Never coach DRM cracks.

## How to get cookies (user’s own session)

Need a **Netscape `cookies.txt`** for `import-cookies`. Only export cookies from an account the user controls. Never pull someone else’s session or scrape login pages for passwords.

### Preferred: OmniGet browser extension

1. Install OmniGet desktop app + its **Chrome/Firefox extension** (from the app’s plugins / pairing UI).
2. Log into the site in that browser (Udemy, etc.).
3. Use the extension’s one-click handoff / cookie pairing so the app stores cookies locally.
4. CLI can then use the shared cookie store, or export/import as Netscape if the UI offers it.

### Manual Netscape export (works with CLI)

1. In Chrome/Firefox, install a reputable **cookies.txt export** extension that writes Netscape format (e.g. “Get cookies.txt LOCALLY” or similar — prefer ones that keep data on-device).
2. Open the site while logged in.
3. Export cookies for that site/domain to `cookies.txt`.
4. Import:

```bash
omniget import-cookies cookies.txt --dry-run   # preview
omniget import-cookies cookies.txt -n udemy
```

5. Re-run `omniget info` / `download` on the URL.

### Note on yt-dlp

OmniGet **already bundles and self-updates yt-dlp** (SHA-256 verified) and uses it for generic sites. Prefer OmniGet’s GUI pairing + `import-cookies` — do **not** tell the user to install or run a separate yt-dlp just for cookies unless OmniGet itself is unavailable.

### Agent checklist

- Ask the user to export (or use OmniGet pairing) — don’t silently read browser profiles unless they explicitly ask and it’s their machine.
- Keep `cookies.txt` out of git; treat as secret.
- If downloads still 401/403 after import: cookies expired → re-login → re-export.


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
