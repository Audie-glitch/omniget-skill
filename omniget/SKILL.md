---
name: omniget
description: >-
  Use when working on the OmniGet repo (Tauri 2 + SvelteKit), contributing
  UI/backend/plugins, or helping install, run, or debug OmniGet / omniget-cli
  downloads across Windows, macOS, and Linux.
---
# OmniGet

> Skill package repo: https://github.com/Audie-glitch/omniget-skill — upstream app: https://github.com/tonhowtf/omniget


Free open-source desktop download manager: courses (Udemy, Hotmart, …), video/audio from 1,800+ sites via yt-dlp, music, books, torrents. Built with **Tauri 2 (Rust) + SvelteKit (Svelte 5)**. Repo: https://github.com/tonhowtf/omniget (GPL-3.0). Local clone often at `/workspace/omniget`.

**Legal:** Downloads only what the user’s own logged-in session can already open. Do not help bypass DRM or paywalls.

## When to use this skill

- Coding, reviewing, or porting OmniGet (`src-tauri/`, `src/`, plugins, browser extension)
- Install / first-launch / portable mode questions
- `omniget-cli` usage (`info`, `download`, `batch`, `import-cookies`)
- Download UX, queue, progress, or i18n work in this codebase

## Quick facts

- No OmniGet account; no telemetry on what is downloaded
- Engine: bundled self-updating yt-dlp (SHA-256 verified) + FFmpeg
- Hotkey: `Ctrl+Shift+D` / macOS `Cmd+Shift+D` (clipboard → download)
- Releases: https://github.com/tonhowtf/omniget/releases/latest
- Plugins guide: `docs/plugin-development.md`
- Not on Flathub

## Install (user machines)

| Platform | How |
|----------|-----|
| Windows | Portable `.exe` / `.msi`, or `winget install -e --id tonhowtf.OmniGet`. SmartScreen → More info → Run anyway |
| macOS | `.dmg` → Applications, then once: `xattr -cr /Applications/omniget.app` and `codesign --force --deep --sign - /Applications/omniget.app` |
| Linux | `.deb` / `.rpm` / `.AppImage` (x86_64 + ARM64). AppImage on Debian 12+ / Ubuntu 24.04+: `libfuse2` or `--appimage-extract-and-run` |

**Portable:** empty `portable.txt` (or `.portable`) next to the exe → data stays beside the binary.

## Dev commands

```bash
pnpm install
pnpm dev              # SvelteKit only
cargo tauri dev       # full app
cargo check
pnpm check            # svelte-check + tsc
pnpm test
```

Package manager: **pnpm**. Monorepo: `src-tauri/` Rust, `src/` SvelteKit + TypeScript.

## Architecture

- **Backend:** Tauri 2, tokio, reqwest, serde, sqlx (SQLite), chromiumoxide
- **Frontend:** SvelteKit 2, Svelte 5 runes (`$state`, `$derived`, `$effect`, `$props`), TypeScript strict
- **CSS:** Scoped + CSS custom properties only. **No Tailwind. No CSS-in-JS.**
- **Icons:** `@tabler/icons-svelte` (individual imports)
- **i18n:** `sveltekit-i18n`, files in `i18n/{lang}/`, all UI strings via `$t()`

### Layout

```
src-tauri/src/
  commands/     # Tauri IPC
  platforms/    # PlatformDownloader trait + site plugins
  core/         # registry, hls, media_processor, queue
  models/ storage/
src/
  routes/ components/ lib/stores/ lib/i18n/
```

Components live under domain folders: `buttons/`, `dialog/`, `omnibox/`, `settings/`, `toast/`, etc.

## Design rules (non-negotiable)

1. **Clarity over chrome** — every element has a function
2. **Tokens only** — never hardcode colors; theme via `[data-theme="dark"|"light"]`; use `--on-*` for text on fills
3. **Layout constants** — `--padding: 12px`, `--border-radius: 11px`, `--sidebar-width: 80px`
4. **A11y** — WCAG 2.2 AA; `:focus-visible` rings; semantic HTML; `prefers-reduced-motion` / reduce-transparency settings
5. **Mobile** — breakpoint 535px (sidebar → bottom tabs); do **not** force dark theme on mobile
6. **Optimistic UI** for pause/resume/remove; revert on failure

## Download UX

Phases (explicit, never “is it hung?”): Preparing → Fetching Info → Starting → Connecting → Downloading.

Always **determinate** progress: `234 MB / 512 MB (45%) • 2.5 MB/s`. Errors must be actionable (`HTTP 429 - Retrying in 15s…`), not “Error”.

## Coding rules

- Svelte 5 runes only; discriminated unions for state
- Settings: partial-merge store + `schemaVersion` migrations
- `@media (hover: hover)` + `:active` fallback; no `!important` except a11y
- Safe-area insets on fixed/sticky UI
- Loop mascot is decorative only — never required to understand state

## CLI

`omniget-cli` ships with releases: `info`, `download`, `batch`, `import-cookies`. Prefer the GUI for library/player workflows; CLI for scripting and headless pulls.

## Agent workflow tips

1. Read this skill + `AGENTS.md` in-repo before large UI/platform changes
2. Match existing component patterns (SettingsToggle, Dialog, omnibox) before inventing new ones
3. Run `pnpm check` / `cargo check` before claiming done
4. For plugins, follow `docs/plugin-development.md` and the `PlatformDownloader` trait
5. Keep GPL-3.0 and DRM/paywall boundaries in any user-facing guidance
