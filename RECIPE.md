# CostUsage

A Herdr workspace layout (`costusage`) and standalone `usage-meter` mod featuring a unified `28px` bottom AI usage, quota, local token cost, and macOS system telemetry bar: keeps the native `268px` left sidebar focused solely on spaces and agents, opens a `400px` quick drill-down popover above the footer, and launches an `840x616` native dialog inspector for multi-account Claude profiles, LiteLLM/Gateway harnesses, Antigravity (Gemini + Claude/GPT), Codex, OpenCode, other detected CLIs, local project token totals, listening ports, and per-account customization in Portuguese, English, and Spanish.

![CostUsage](preview.png)

## What you see

- **Left sidebar (`268px`, `background: "chrome"`, non-resizable):** Top `46px` window-drag header (`workspace-chrome`), split stack of `spaces` (`weight: 1`, `min: 90`) and `agents` (`weight: 1`, `min: 90`), and a `69px` footer (`workspace-chrome` with `{ "footer": true }`).
- **Right work column (`work`, non-resizable):** Top `42px` draggable tab bar (`tabs`), `work-body` (`padding: 8`, `gap: 8`) with the active pane stack (`radius: 10`, `1px` `border` ring, `1px` `accent` focus ring, `30px` `pane` header) plus a toggleable `368px` right panel (`files` / `changes`, `hidden: true` by default), and a full-width `28px` bottom `status` bar (`usage-meter`).
- **Bottom status bar (`mode: "status"`, `28px`):** Displays live machine/agent counts (`{machine} • {n} agents`), optional `Used`/`Left` toggle chip, compact provider pills (`Claude` profiles with custom aliases/icons, LiteLLM/Gateway harnesses, `Antigravity` `Gem X%/Y% · Cl Z%`), optional CPU/`$USD`/action icons, collapse chevron (`«` / `»`), and command palette hint.
- **Quick popover (`400px` overlay):** Agent title, silent/manual refresh control, `Used ↑↓` / `Left ↓↑` sort toggle, `Detailed | Compact` segmented control, expandable provider rows with in-place window drill-down (`5h`, `Weekly`), optional `$USD` footer, and shortcuts into the full inspector.
- **Inspector dialog (`840x616` overlay with `190px` navigation rail):**
  - **Overview:** Timeframe (`Today` / `7 days` / `30 days`) and scope (`Overview` / `Claude` / `Harness`) switches; unified 4-cell KPI strip(s) (`Total tokens`, `Est. cost`, `Active days`, `Cache share`, plus `Input`, `Output`, `Cache read`, `Cache write`, `Cache reuse`, `No cache read`, `Sessions / Turns`, `Est. cost`); 35-day activity intensity heatmap; token mix bar; dynamic 2-column `PROVIDERS` grid (`Claude`, `Harness (LiteLLM)`, `Antigravity (Gemini)`, `Codex`, `OpenCode`, and extra detected CLIs); 14-day daily chart; top models and projects.
  - **Agents & Quotas:** Full quota window cards (`5h`, `Weekly`, model buckets) and detected CLI cards (`Gemini CLI`, `Kimi`, `Grok`, `Cursor`, `Codex`, `OpenCode`).
  - **Projects:** Per-project local token, cache, session/turn counts, and optional `$USD` equivalents.
  - **System:** Live macOS CPU, RAM, disk summary, top processes, and local listening TCP ports (`lsof`).
  - **Settings:** Language selector (`Auto` / `Portuguese` / `English` / `Español`), `Used`/`Left` mode, default popover mode, optional `$USD` visibility toggles, granular bar element toggles (`alwaysStartCollapsed`, USD pill, action icons, CPU, ports), poll interval (`5m` / `15m` / `30m`), provider toggles, and a fixed-column `Detected Accounts` table with inline renaming (`Input`), 17-icon picker, `In bar`, and `Active` toggles.

## How it behaves

- Can be installed as a complete layout (`layouts/costusage.json` + `mods/usage-meter/`) or dropped as a standalone mod (`mods/usage-meter/` + `keys.json`) into any custom layout's `28px` status block.
- Starts collapsed by default (`alwaysStartCollapsed: true`, `barCollapsed: true`) to keep the status bar minimal until expanded.
- Polls full usage/quota silently every `15m` by default (spinner animates only on manual click or initial empty cache, with `5s` manual throttle) and refreshes lightweight macOS CPU/ports every `120_000 ms` (`collect.py sys`).
- Supports `nextCommand()` bindings (`popover`, `details`, `claude`, `all`, `agents`, `ports`, `settings`, `refresh`, `toggle_bar`, `toggle_sidebar`), with `keys.json` binding `alt-u` (`usage-meter.popover`) and `alt-shift-u` (`usage-meter.details`).
- Dedupes Claude Code JSONL entries by `message.id:requestId` with monotonic max token merging across `~/.claude*/projects` and subagent transcripts, keeping non-Anthropic / Harness models (`ollama-*`, `kiro-*`, etc.) priced at `$0.00` so subscription/local gateways never inflate Anthropic API cost totals.

## Built from

- **Layout:** `layouts/costusage.json` (cloned from Herdr `main` `0.1.17`, `titlebar: "none"`, `traffic_lights: [12, 16]`, `background: "canvas"`, `terminal_padding: [14, 12]`, `divider: { width: 1, color: "border" }`), mounting `"mod": "usage-meter"` with `"props": { "mode": "status", "poller": true }` in `"id": "status"` (`{ "px": 28 }`).
- **Shipped mods used:** `workspace-chrome`, `spaces`, `agents`, `tabs`, `pane`, `files`, `changes`.
- **Mod files:** `mods/usage-meter/mod.json` (`place: "none"`, `overlay: { kind: "dialog", width: 840, height: 616 }`), `mods/usage-meter/main.js`, `mods/usage-meter/i18n.js`, `mods/usage-meter/collect.py`, monochrome SVGs (`arrows-up-down.svg`, `chart.svg`, `chevron-down.svg`, `chevron-right.svg`, `cpu.svg`, `ports.svg`, `refresh.svg`, `sliders.svg`), and optional `keys.json`.
- **Herdr APIs:** `exec`, `shared` / `setShared`, `openOverlay` / `closeOverlay`, `nextCommand`, `theme`, `hosts`, `agents`, `spaces`, `tabs`, `run`, `appHome`, and `gpui-kit` primitives (`v_flex`, `h_flex`, `div`, `label`, `button`, `switch`, `Input`, `Scrollbar`, `svg`, `image`).

## Watch out

- **macOS dependency in `collect.py`:** Uses macOS Keychain (`security find-generic-password`) plus `top -l 1`, `vm_stat`, `sysctl`, `df -k`, and `lsof -iTCP -sTCP:LISTEN -n -P`, alongside `python3` and `curl`. Linux setups need `/proc` + Secret Service fallbacks.
- **Runtime caches stay local:** `collect.py` writes `cost-cache.json`, `quota-cache.json`, and `snapshot.json` inside `mods/usage-meter/` on first run (`OAUTH_MIN_TTL_MS = 180_000` with `429` fallback to `lastOk`). Never commit those files.
- **Key merges:** Adding `keys.json` adds `alt-u` and `alt-shift-u` under `"global"`; inspect your existing `~/.config/herdr-app/keys.json` first if those shortcuts are already bound.

## Done when

- `herdr-app check ~/.config/herdr-app/mods/usage-meter` passes cleanly with zero errors.
- `herdr-app do layout.switch --name costusage` loads with `"layout": "costusage"` and `"errors": []` in `herdr-app status`.
- The bottom bar populates provider pills, `herdr-app do usage-meter.popover` opens the quick popover, and `herdr-app do usage-meter.details` opens the `840x616` inspector dialog cleanly.
