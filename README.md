# RunningHub Agent Skills

Give **any** AI coding agent the ability to generate images, videos, audio, and 3D
models through the [RunningHub](https://www.runninghub.cn) API — and to run any
RunningHub AI Application (custom ComfyUI workflow).

> Adapted for cross-agent use from
> [HM-RunningHub/OpenClaw_RH_Skills](https://github.com/HM-RunningHub/OpenClaw_RH_Skills)
> (Apache-2.0). The original targets OpenClaw only; this fork works in Claude
> Code, Cursor, Qoder, OpenClaw, or any agent that can read a `SKILL.md` and
> run Python scripts.

## What you get

| Capability | Endpoints | Examples |
|---|---|---|
| **Image** | 97 | text-to-image, image edit, upscaling, style transfer |
| **Video** | 213 | text-to-video, image-to-video, Seedance, Hailuo, Kling |
| **Audio** | 20 | TTS, music generation, voice clone |
| **3D** | 16 | text-to-3D, image-to-3D, multi-image-to-3D |
| **Text** | 48 | image understanding, video understanding, text processing |
| **AI Apps** | ∞ | run any RunningHub AI Application (custom ComfyUI workflow) |

## Prerequisites

- Python 3.10+ (stdlib only, no pip installs)
- `curl` on PATH
- A RunningHub API key — get one at
  [runninghub.cn → Enterprise API](https://www.runninghub.cn/enterprise-api/sharedApi)

## Install

Copy (or clone) this repo, then place the `skills/runninghub` folder where
your agent discovers skills:

### Claude Code
```bash
# user-level (all projects)
git clone https://github.com/johndelapena168/runninghub-agent-skills /tmp/rh-skills
mkdir -p ~/.claude/skills
cp -r /tmp/rh-skills/skills/runninghub ~/.claude/skills/
```

### Cursor
```bash
# project-level
git clone https://github.com/johndelapena168/runninghub-agent-skills /tmp/rh-skills
mkdir -p .cursor/skills
cp -r /tmp/rh-skills/skills/runninghub .cursor/skills/
```

### Qoder
```bash
# user-level (all projects)
git clone https://github.com/johndelapena168/runninghub-agent-skills /tmp/rh-skills
mkdir -p ~/.qoder/skills
cp -r /tmp/rh-skills/skills/runninghub ~/.qoder/skills/
```
Or install as a Qoder plugin — this repo ships a `.qoder-plugin/plugin.json`.

### OpenClaw
```
从 https://github.com/johndelapena168/runninghub-agent-skills 安装 RunningHub 技能
```
(The upstream install flow still works; this fork keeps OpenClaw compatibility.)

## Configure your API key

The scripts read the key in this order: `--api-key` flag → `RUNNINGHUB_API_KEY`
env var → OpenClaw config. For every agent except OpenClaw, set the env var:

```bash
export RUNNINGHUB_API_KEY="your-key-here"     # macOS/Linux
setx RUNNINGHUB_API_KEY "your-key-here"       # Windows
```

Verify it works:
```bash
python3 skills/runninghub/scripts/runninghub.py --check
# → {"status": "ready", "balance": "...", "coins": "...", "api_type": "SHARED"}
```

## Usage

Just talk to your agent naturally — the skill routes intent to the right
endpoint:

- "Generate a photo of a red sports car" → text-to-image
- "Animate this image" (with attachment) → image-to-video
- "Make a 10-second cinematic drone shot of a beach" → text-to-video
- "Turn this text into speech" → TTS
- "Generate a 3D model from this photo" → image-to-3D
- "Run my ComfyUI app 123456 with this image" → AI Application

The skill presents model menus for ambiguous requests (e.g. 5 image models,
8 video models) and confirms cost-affecting settings before generating.

## Scripts

| Script | Purpose |
|---|---|
| `runninghub.py` | Universal client for all 394 standard Model API endpoints (`--check`, `--list`, `--info`, `--endpoint`, `--task`) |
| `runninghub_app.py` | AI Application client — run any ComfyUI workflow by webappId (`--list`, `--info`, `--run`) |
| `build_capabilities.py` | Regenerate `data/capabilities.json` from the live API catalog |

All scripts are Python stdlib + curl only. Output goes to `--output` or a
cross-platform temp dir (`/tmp/rh-output` on Linux/macOS).

## Repository layout

```
runninghub-agent-skills/
├── skills/runninghub/
│   ├── SKILL.md                      # Cross-agent skill definition
│   ├── scripts/
│   │   ├── runninghub.py             # Standard Model API client (394 endpoints)
│   │   ├── runninghub_app.py         # AI Application client
│   │   └── build_capabilities.py     # Regenerate capabilities.json
│   ├── data/capabilities.json        # Full endpoint catalog (auto-generated)
│   └── references/                   # Model menus, key setup, delivery rules
├── .qoder-plugin/plugin.json         # Qoder packaging
├── assets/avatar.svg
├── LICENSE                           # Apache-2.0
└── README.md
```

## What changed vs upstream

- **Agent-agnostic**: removed OpenClaw-only metadata, persona, and the
  `message` tool delivery requirement — files are delivered via your agent's
  native mechanism.
- **Cross-platform output paths**: no hardcoded `/tmp/openclaw`; defaults to
  the system temp dir on any OS.
- **Env-var-first key setup**: `RUNNINGHUB_API_KEY` works everywhere;
  OpenClaw config remains a fallback.
- **Windows console fixes**: scripts force UTF-8 stdout/stderr and read curl
  output as UTF-8, so Chinese catalog text doesn't crash cp1252 consoles.
- **Overridable API host**: set `RUNNINGHUB_BASE_URL` to switch nodes
  (some models are region-restricted — see
  `references/image-models.md` for the CN compliance notice).

## License

Apache-2.0 — same as upstream. Copyright of the original scripts belongs to
HM-RunningHub; adaptations in this fork are by NextEra AI.

**Never commit your API key.** Keep it in the environment variable only.
