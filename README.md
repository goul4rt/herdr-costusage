# Herdr CostUsage (`herdr-costusage`)

A unified [Herdr](https://herdr.dev) **layout + standalone mod** for tracking AI agent usage, quota windows, local token costs, and macOS system telemetry from a clean `28px` bottom status bar, quick drill-down popover, and `840×616` native inspector dialog.

![CostUsage](preview.png)

## Features

- **Clean Workspace Layout (`layouts/costusage.json`):** Keeps Herdr's native sidebar (`Spaces` + `Agents`) completely untouched while docking `usage-meter` across a unified `28px` bottom status slot.
- **Standalone Mod (`mods/usage-meter/`):** Drop into any custom Herdr layout or install together with `costusage.json`.
- **Multi-Provider & Multi-Account Tracking:**
  - **Claude Code** (auto-discovers multiple profiles like `~/.claude`, `~/.claude-*`, Keychain credentials, emails, and plans).
  - **LiteLLM / Harness Gateways** (isolates local/proxy models such as `ollama-*`, `kiro-*`, etc. at `$0.00` without distorting Anthropic API cost stats).
  - **Antigravity** (live Gemini + Claude/GPT quota bars and active model/conversation detection).
  - **Codex, OpenCode & Extra CLIs** (`Gemini CLI`, `Kimi`, `Grok`, `Cursor` auto-detection).
- **Quick Popover & Full Inspector (`840×616`):**
  - `Detailed` / `Compact` modes, `Used` / `Left` toggles, interactive provider drill-down.
  - 35-day activity intensity heatmap, token mix bar, unified 4-cell KPI strips, 14-day chart, top models/projects, and live macOS CPU/RAM/Ports (`lsof`).
- **Granular Customization & i18n:**
  - Always starts collapsed by default (`alwaysStartCollapsed: true`), silent background polling (`15m`), optional `$USD` visibility toggles, inline account alias editor (`Input`), 17-icon picker, and **Auto / Português / English / Español** UI.

## Installation

### Option A: Full Layout (`costusage` + `usage-meter`)

```bash
cp layouts/costusage.json ~/.config/herdr-app/layouts/costusage.json
cp -R mods/usage-meter ~/.config/herdr-app/mods/usage-meter
herdr-app check ~/.config/herdr-app/mods/usage-meter
herdr-app do layout.switch --name costusage
```

*(Use `Cmd+Shift+L` anytime to switch between layouts.)*

### Option B: Standalone Mod Only (`usage-meter` in your own layout)

1. Copy the mod and validate:

   ```bash
   cp -R mods/usage-meter ~/.config/herdr-app/mods/usage-meter
   herdr-app check ~/.config/herdr-app/mods/usage-meter
   ```

2. Add the `status` block to your layout:

   ```json
   {
     "id": "status",
     "type": "block",
     "content": {
       "mod": "usage-meter",
       "props": {
         "mode": "status",
         "poller": true
       }
     },
     "size": {
       "px": 28
     }
   }
   ```

3. *(Optional)* Merge [`keys.json`](keys.json) (`alt-u` / `alt-shift-u`) into `~/.config/herdr-app/keys.json`.

## Commands

Available via `keys.json` or `herdr-app do`:

- `usage-meter.popover` — Toggle quick usage popover (`alt-u`)
- `usage-meter.details` — Open Usage Overview inspector (`alt-shift-u`)
- `usage-meter.claude` / `usage-meter.all` — Open Overview filtered by Claude / All providers
- `usage-meter.agents` / `usage-meter.ports` / `usage-meter.settings` — Jump directly to specific inspector sections
- `usage-meter.refresh` — Trigger immediate usage & quota refresh
- `usage-meter.toggle_bar` — Collapse/expand the bottom status bar

## Requirements & Privacy

- **macOS** (`python3`, `curl`, Keychain `security`, `lsof`, `vm_stat`, `top`)
- All log parsing and token/cost aggregation runs **locally** on your machine. Quota checks query only the provider endpoints already authenticated by your local CLI/Keychain sessions.

## License

MIT
