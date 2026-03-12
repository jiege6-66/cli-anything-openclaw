<h1 align="center"><img src="assets/icon.png" alt="" width="64" style="vertical-align: middle;">&nbsp; CLI-Anything for OpenClaw</h1>

<p align="center">
  <strong>OpenClaw Skills Adaptation of <a href="https://github.com/HKUDS/CLI-Anything">HKUDS/CLI-Anything</a></strong><br>
  Make any software agent-native through automated CLI generation
</p>

<p align="center">
  <a href="#-openclaw-quick-start"><img src="https://img.shields.io/badge/OpenClaw_Skills-5_skills-blue?style=for-the-badge" alt="Skills"></a>
  <a href="#-original-demos"><img src="https://img.shields.io/badge/Demos-11_Apps-green?style=for-the-badge" alt="Demos"></a>
  <a href="#-test-results"><img src="https://img.shields.io/badge/Tests-1%2C508_Passing-brightgreen?style=for-the-badge" alt="Tests"></a>
  <a href="#-license"><img src="https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge" alt="License"></a>
</p>

---

## ⚡ What Is This?

This is an **OpenClaw skills adaptation** of the [CLI-Anything](https://github.com/HKUDS/CLI-Anything) project by HKUDS. It packages the CLI-Anything methodology as **OpenClaw AgentSkills**, so any OpenClaw agent can build, refine, test, validate, and list CLI harnesses for GUI applications — using standard OpenClaw tools (`read`, `exec`, `write`, `edit`).

> **Upstream**: [github.com/HKUDS/CLI-Anything](https://github.com/HKUDS/CLI-Anything)
>
> The original project provides a Claude Code plugin and OpenCode commands. This repo adapts those into OpenClaw-native skills while preserving all upstream documentation, examples, and the authoritative `HARNESS.md` methodology.

---

## 🚀 OpenClaw Quick Start

中文安装文档：[`OPENCLAW_INSTALL_CN.md`](OPENCLAW_INSTALL_CN.md)


### Installation

Copy or symlink the `skills/` directory into your OpenClaw skills location:

```bash
# Option 1: Symlink into workspace skills
ln -s /path/to/cli-anything-openclaw/skills/* ~/.openclaw/workspace/skills/

# Option 2: Symlink into global skills
ln -s /path/to/cli-anything-openclaw/skills/* ~/.openclaw/skills/

# Option 3: Clone directly into skills directory
cd ~/.openclaw/skills
git clone https://github.com/xliangj/cli-anything-openclaw.git _cli-anything-repo
for d in _cli-anything-repo/skills/*/; do ln -s "$(realpath "$d")" .; done
```

After installation, OpenClaw agents will auto-discover these skills via their `description` fields.

### Usage

Once installed, just tell your OpenClaw agent what you want:

- *"Build a CLI for GIMP from ~/src/gimp"* → agent picks `cli-anything` skill
- *"Expand the Blender CLI with particle system commands"* → `cli-anything-refine`
- *"Run tests for the inkscape harness"* → `cli-anything-test`
- *"Validate the audacity CLI against standards"* → `cli-anything-validate`
- *"List all CLI-Anything tools on this machine"* → `cli-anything-list`

---

## 📦 Skills Overview

The `skills/` directory is the **primary entry point** for OpenClaw users.

| Skill | Description | Upstream Equivalent |
|-------|-------------|-------------------|
| [`cli-anything`](skills/cli-anything/SKILL.md) | Build complete CLI harness (all 7 phases) | `/cli-anything` command |
| [`cli-anything-refine`](skills/cli-anything-refine/SKILL.md) | Expand coverage of an existing harness | `/cli-anything-refine` command |
| [`cli-anything-test`](skills/cli-anything-test/SKILL.md) | Run tests and update TEST.md | `/cli-anything-test` command |
| [`cli-anything-validate`](skills/cli-anything-validate/SKILL.md) | Validate harness against HARNESS.md standards | `/cli-anything-validate` command |
| [`cli-anything-list`](skills/cli-anything-list/SKILL.md) | List installed & generated CLI tools | `/cli-anything-list` command |

### Key Differences from Upstream

| Aspect | Upstream (Claude Code / OpenCode) | This Repo (OpenClaw) |
|--------|----------------------------------|---------------------|
| Tool interface | `/slash-commands` in Claude Code/OpenCode | OpenClaw `read`/`exec`/`write`/`edit` tools |
| Discovery | Plugin marketplace or manual copy | OpenClaw skill auto-discovery via `description` |
| Methodology | Same HARNESS.md | Same HARNESS.md (referenced, not duplicated) |
| Agent runtime | Claude Code session / OpenCode session | Any OpenClaw agent session |

---

## 📖 Methodology: HARNESS.md

The **single source of truth** for the CLI-Anything methodology is:

📄 **[`cli-anything-plugin/HARNESS.md`](cli-anything-plugin/HARNESS.md)**

It covers:
- The 7-phase pipeline (analysis → design → implement → plan tests → write tests → document → publish)
- Architecture standards and implementation patterns
- Critical lessons learned (rendering gap, filter translation, timecode precision, output verification)
- Testing strategy and directory structure
- Key principles and rules

All skills reference this document. Read it before using any skill.

---

## 🎯 Original Demos

The upstream project includes 11 battle-tested CLI harnesses:

| Software | Domain | CLI Command | Tests |
|----------|--------|-------------|-------|
| 🎨 GIMP | Image Editing | `cli-anything-gimp` | ✅ 107 |
| 🧊 Blender | 3D Modeling | `cli-anything-blender` | ✅ 208 |
| ✏️ Inkscape | Vector Graphics | `cli-anything-inkscape` | ✅ 202 |
| 🎵 Audacity | Audio Production | `cli-anything-audacity` | ✅ 161 |
| 📄 LibreOffice | Office Suite | `cli-anything-libreoffice` | ✅ 158 |
| 📹 OBS Studio | Live Streaming | `cli-anything-obs-studio` | ✅ 153 |
| 🎞️ Kdenlive | Video Editing | `cli-anything-kdenlive` | ✅ 155 |
| 🎬 Shotcut | Video Editing | `cli-anything-shotcut` | ✅ 154 |
| 📞 Zoom | Video Conferencing | `cli-anything-zoom` | ✅ 22 |
| 📐 Draw.io | Diagramming | `cli-anything-drawio` | ✅ 138 |
| ✨ AnyGen | AI Content Gen | `cli-anything-anygen` | ✅ 50 |

> **1,508 total tests** — 100% pass rate across all harnesses.

---

## 📊 Test Results

Each CLI harness undergoes rigorous multi-layered testing:

| Layer | What it tests | Example |
|-------|---------------|---------|
| **Unit tests** | Every core function in isolation | `test_core.py` — project creation, layer ops |
| **E2E tests (native)** | Project file generation pipeline | Valid ODF ZIP, correct MLT XML |
| **E2E tests (backend)** | Real software invocation + output | LibreOffice → PDF, Blender → PNG |
| **CLI subprocess** | Installed command via `subprocess.run` | `cli-anything-gimp --json project new` |

```
================================ Test Summary ================================
gimp          107 passed  ✅   (64 unit + 43 e2e)
blender       208 passed  ✅   (150 unit + 58 e2e)
inkscape      202 passed  ✅   (148 unit + 54 e2e)
audacity      161 passed  ✅   (107 unit + 54 e2e)
libreoffice   158 passed  ✅   (89 unit + 69 e2e)
obs-studio    153 passed  ✅   (116 unit + 37 e2e)
kdenlive      155 passed  ✅   (111 unit + 44 e2e)
shotcut       154 passed  ✅   (110 unit + 44 e2e)
zoom           22 passed  ✅   (22 unit + 0 e2e)
drawio        138 passed  ✅   (116 unit + 22 e2e)
anygen         50 passed  ✅   (40 unit + 10 e2e)
──────────────────────────────────────────────────────────────────────────────
TOTAL        1,508 passed  ✅   100% pass rate
```

---

## 📂 Repository Structure

```
cli-anything-openclaw/
│
├── skills/                              ⭐ START HERE — OpenClaw skills
│   ├── cli-anything/SKILL.md            Build complete CLI harness
│   ├── cli-anything-refine/SKILL.md     Expand harness coverage
│   ├── cli-anything-test/SKILL.md       Run tests & update docs
│   ├── cli-anything-validate/SKILL.md   Validate against standards
│   └── cli-anything-list/SKILL.md       List available CLI tools
│
├── cli-anything-plugin/                 Upstream plugin & methodology
│   ├── HARNESS.md                       ⭐ Methodology SOP (source of truth)
│   ├── README.md                        Plugin documentation
│   ├── QUICKSTART.md                    5-minute guide
│   ├── PUBLISHING.md                    Distribution guide
│   ├── repl_skin.py                     Unified REPL interface
│   └── LICENSE                          MIT License
│
├── opencode-commands/                   Upstream OpenCode commands (reference)
│   ├── cli-anything.md
│   ├── cli-anything-refine.md
│   ├── cli-anything-test.md
│   ├── cli-anything-validate.md
│   └── cli-anything-list.md
│
├── assets/                              Images and media
├── README.md                            This file
└── README_CN.md                         Chinese documentation (upstream)
```

---

## 🤔 Why CLI?

CLI is the universal interface for both humans and AI agents:

- **Structured & Composable** — text commands match LLM format and chain for complex workflows
- **Lightweight & Universal** — minimal overhead, works across all systems
- **Self-Describing** — `--help` flags provide automatic documentation agents can discover
- **Agent-First Design** — structured JSON output eliminates parsing complexity
- **Deterministic & Reliable** — consistent results enable predictable agent behavior

---

## 🏗️ Architecture

<p align="center">
  <img src="assets/architecture.png" alt="CLI-Anything Architecture" width="750">
</p>

### Core Design Principles

1. **Authentic Software Integration** — Generate valid project files and delegate to real applications for rendering
2. **Dual Interaction Models** — Stateful REPL for interactive sessions + subcommand CLI for scripting
3. **Consistent UX** — Unified REPL interface (`repl_skin.py`) across all CLIs
4. **Agent-Native** — `--json` on every command, discoverable via `--help` and `which`
5. **Zero Compromise** — Real software is a hard requirement, no fallbacks

---

## 🤝 Contributing

- **New skills** — extend the `skills/` directory with new CLI-Anything workflows
- **Methodology improvements** — PRs to upstream `HARNESS.md`
- **New harnesses** — generate CLIs for more software and contribute them
- **OpenClaw integration improvements** — better skill descriptions, new tool patterns

---

## 📄 License

The upstream CLI-Anything project is released under the **MIT License** (see [`cli-anything-plugin/LICENSE`](cli-anything-plugin/LICENSE)).

This adaptation inherits the same license. The upstream README and `cli-anything-plugin/LICENSE` both state MIT.

---

## 🔗 Links

- **Upstream Repository**: [github.com/HKUDS/CLI-Anything](https://github.com/HKUDS/CLI-Anything)
- **OpenClaw**: [github.com/nicholasgasior/openclaw](https://github.com/nicholasgasior/openclaw)
- **HARNESS.md** (methodology): [`cli-anything-plugin/HARNESS.md`](cli-anything-plugin/HARNESS.md)

---

<div align="center">

**CLI-Anything for OpenClaw** — *Make any software agent-native, powered by OpenClaw skills.*

<sub>Adapted from HKUDS/CLI-Anything | 5 OpenClaw skills | 11 demo harnesses | 1,508 passing tests</sub>

</div>
