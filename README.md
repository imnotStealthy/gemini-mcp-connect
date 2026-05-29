# gemini-mcp-connect

A Claude Code plugin that connects **Claude** to **Google Gemini** as a native MCP tool - enabling dual-AI code review, plan validation, security audits, and critical second-opinion analysis.

[![PyPI](https://img.shields.io/pypi/v/gemini-mcp-connect)](https://pypi.org/project/gemini-mcp-connect/)
[![Python](https://img.shields.io/pypi/pyversions/gemini-mcp-connect)](https://pypi.org/project/gemini-mcp-connect/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

---

## How it works

Before modifying any file, Claude automatically submits its plan or code to Gemini for a critical independent review - logic flaws, security issues, wrong assumptions, optimizations. Only after integrating Gemini's feedback does Claude apply the changes.

Gemini runs as a **native MCP tool**, not a bash script. Claude calls it directly, the same way it uses any other tool.

---

## Installation

### One-liner (no clone needed)

**macOS / Linux:**
```bash
curl -fsSL https://raw.githubusercontent.com/imnotStealthy/gemini-mcp-connect/main/install.sh | bash
```

**Windows (PowerShell):**
```powershell
irm https://raw.githubusercontent.com/imnotStealthy/gemini-mcp-connect/main/install.ps1 | iex
```

Both scripts prompt for your Gemini API key. Get a free key at [aistudio.google.com/apikey](https://aistudio.google.com/apikey).

If you skip the key during install, activate later with `/gemini:activate YOUR_KEY` in Claude Code.

---

### MCP only (no slash commands)

```bash
claude mcp add --scope user gemini-mcp-connect -e GEMINI_API_KEY=your_key_here -- uvx gemini-mcp-connect
```

---

### From clone

**macOS / Linux:**
```bash
git clone https://github.com/imnotStealthy/gemini-mcp-connect
cd gemini-mcp-connect
bash install.sh
```

**Windows (PowerShell):**
```powershell
git clone https://github.com/imnotStealthy/gemini-mcp-connect
cd gemini-mcp-connect
.\install.ps1
```

---

## Slash commands

| Command | Description |
|---------|-------------|
| `/gemini:model <lite\|flash\|pro> <prompt>` | Query Gemini with the chosen tier |
| `/gemini:review <file or code>` | Critical code review (CRITICAL / WARNING / SUGGESTION) |
| `/gemini:validate <plan>` | Validate a plan before executing (PROCEED / REVISE / DO NOT PROCEED) |
| `/gemini:security <file or code>` | Security audit - OWASP Top 10, secrets, injections |
| `/gemini:debug <error>` | Diagnose an error or stack trace - root cause + fix |
| `/gemini:config [setting] [value]` | View or update settings (thinking, temperature, media…) |
| `/gemini:status` | Remaining API quota for today |
| `/gemini:activate <key>` | Set your Gemini API key (no restart needed) |

### /gemini:model examples

```
/gemini:model flash what is the difference between TCP and UDP?
/gemini:model pro review this architecture for scalability issues
/gemini:model lite quick summary of this file
```

### /gemini:config examples

```
/gemini:config                   → show all current settings
/gemini:config thinking high     → set thinking level to HIGH
/gemini:config thinking off      → disable thinking (faster)
/gemini:config temperature 0.5   → more deterministic responses
/gemini:config media high        → higher media resolution
/gemini:config tokens 32768      → limit output length
```

---

## Tier prefix (fastest way)

Start any message with `pro,`, `flash,` or `lite,` to choose the Gemini model for that task:

```
pro, refactor this code
flash, search for information on X
lite, quick look at my code
```

No prefix → `pro` by default.

---

## MCP Tools

Claude calls these tools natively - no bash command needed.

| Tool | Default tier | Description |
|------|-------------|-------------|
| `query_gemini(prompt, tier)` | `pro` | Open-ended question to Gemini |
| `review_code(code, language, tier)` | `flash` | Critical code review |
| `validate_plan(plan, tier)` | `pro` | Validate a plan before executing |
| `security_audit(code, language, tier)` | `pro` | Security audit (OWASP, secrets, injections) |
| `debug_error(error, context, tier)` | `flash` | Diagnose errors and stack traces |
| `configure_gemini(setting, value)` | - | View/update configuration settings |
| `activate_gemini(api_key)` | - | Set API key without restart |
| `gemini_status()` | - | Remaining quota for today |

---

## Models

| Tier | Model | Best for |
|------|-------|----------|
| `lite` | `gemini-3.1-flash-lite-preview` | Quick checks, fast & cheap |
| `flash` | `gemini-3-flash-preview` | Code review, balanced speed |
| `pro` | `gemini-3.1-pro-preview` | Architecture, security, deep reasoning (**default**) |

---

## Pricing

| Tier | Cost/request\* | Requests for $1 |
|------|---------------|-----------------|
| `lite` | ~$0.002 | ~500 |
| `flash` | ~$0.004 | ~250 |
| `pro` | ~$0.016 | ~62 |

\*Assumes ~2K input tokens + ~1K output tokens per request.

The `pro` tier is rate-limited to **100 requests/day** by default (~$1.60/day max). `flash` and `lite` are unlimited.

---

## Configuration

Managed via `/gemini:config` or by setting environment variables (`claude mcp add -e`).

| Setting | Variable | Default | Values |
|---------|----------|---------|--------|
| `thinking` | `GEMINI_THINKING_LEVEL` | `HIGH` | `OFF` / `LOW` / `MEDIUM` / `HIGH` |
| `temperature` | `GEMINI_TEMPERATURE` | `1.0` | `0.0` – `2.0` |
| `media` | `GEMINI_MEDIA_RESOLUTION` | `MEDIUM` | `LOW` / `MEDIUM` / `HIGH` |
| `tokens` | `GEMINI_MAX_OUTPUT_TOKENS` | `65536` | `1` – `65536` |
| `top_p` | `GEMINI_TOP_P` | `0.95` | `0.0` – `1.0` |

---

## Plugin structure

```
gemini-mcp-connect/
├── gemini_bridge_mcp.py         # MCP server (main entry point)
├── gemini_bridge/               # Python package (CLI + shared core)
├── commands/
│   └── gemini/                  # Slash commands
│       ├── model.md             # /gemini:model <tier> <prompt>
│       ├── review.md            # /gemini:review
│       ├── validate.md          # /gemini:validate
│       ├── security.md          # /gemini:security
│       ├── debug.md             # /gemini:debug
│       ├── config.md            # /gemini:config
│       ├── status.md            # /gemini:status
│       └── activate.md          # /gemini:activate
├── install.sh                   # macOS/Linux installer
├── install.ps1                  # Windows installer
└── pyproject.toml               # PyPI packaging
```

---

## License

MIT - [imnotStealthy](https://github.com/imnotStealthy)
