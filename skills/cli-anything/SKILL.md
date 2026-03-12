---
name: cli-anything
description: "Build a complete CLI harness for any GUI application (all 7 phases). Use when: user wants to make a software agent-native by generating a full CLI interface from its source code or GitHub repo. Runs the full CLI-Anything pipeline: codebase analysis, CLI architecture design, implementation, test planning, test writing, documentation, and publishing."
metadata: {"openclaw":{"emoji":"🔧"}}
---

# cli-anything — Build a Complete CLI Harness

Build a production-ready, stateful CLI harness for **any** GUI application, following the proven CLI-Anything 7-phase methodology.

## When to Use

- User provides a **local path** to a software's source code, or a **GitHub repo URL**
- Goal: generate a full CLI that lets AI agents control the software without a GUI

## Prerequisites

- Python 3.10+
- Target software source code available locally (or a GitHub URL to clone)
- The target software installed on the system (for backend integration / E2E tests)

## Arguments

| Arg | Required | Description |
|-----|----------|-------------|
| `<software-path-or-repo>` | ✅ | Local path to source code, or a GitHub URL |

## Methodology

**Before starting, read the authoritative methodology document:**

```
read {baseDir}/../../cli-anything-plugin/HARNESS.md
```

> `HARNESS.md` (located at `cli-anything-plugin/HARNESS.md` relative to the repo root) is the **single source of truth** for architecture standards, implementation patterns, and quality requirements. Every phase below follows it. Do not improvise — follow the harness specification.

## The 7-Phase Pipeline

### Phase 0: Source Acquisition

If the user gives a GitHub URL, clone it first:

```
exec command:"git clone <url> /tmp/<software-name>"
```

Verify the local path exists and contains source code. Derive the software name from the directory name.

### Phase 1: Codebase Analysis

Use `read` and `exec` (with `find`, `grep`, `head`, etc.) to:

1. Identify the backend engine (e.g., MLT for Shotcut, bpy for Blender)
2. Map GUI actions to API calls
3. Identify the data model and file formats
4. Find existing CLI tools the backend ships
5. Catalog the command/undo system

### Phase 2: CLI Architecture Design

Design the CLI structure:

- Command groups matching the app's logical domains
- State model (what persists between commands)
- Output formats (`--json` for agents, human-readable for debugging)
- Create the software-specific SOP document (`<SOFTWARE>.md`)

### Phase 3: Implementation

Create the directory structure and write all source files:

```
<software-name>/
└── agent-harness/
    ├── <SOFTWARE>.md
    ├── setup.py
    └── cli_anything/          # Namespace package (NO __init__.py)
        └── <software>/        # Sub-package (HAS __init__.py)
            ├── README.md
            ├── <software>_cli.py
            ├── core/
            │   ├── project.py
            │   ├── session.py
            │   └── export.py
            ├── utils/
            └── tests/
```

Use `write` to create each file. Follow patterns from HARNESS.md:

- Click-based CLI with REPL support (using `repl_skin.py`)
- `--json` output mode for all commands
- `--project` flag for project file management
- `handle_error` decorator for consistent error handling
- Real backend integration via `utils/<software>_backend.py`

### Phase 4: Test Planning

Create `tests/TEST.md` with:

- Unit test plan (synthetic data, no external deps)
- E2E test plan (real files, full pipeline)
- Workflow test scenarios

### Phase 5: Test Implementation

Write test files using `write`:

- `tests/test_core.py` — unit tests
- `tests/test_full_e2e.py` — E2E tests with `TestCLISubprocess` class
- Use `_resolve_cli("cli-anything-<software>")` (no hardcoded paths)

### Phase 6: Test Documentation

Run tests and capture results:

```
exec command:"cd <software>/agent-harness && python3 -m pytest cli_anything/<software>/tests/ -v --tb=short" timeout:120
```

Update `TEST.md` with results.

### Phase 7: PyPI Publishing & Installation

Create `setup.py`, install locally, verify:

```
exec command:"cd <software>/agent-harness && pip install -e ."
exec command:"which cli-anything-<software>"
exec command:"cli-anything-<software> --help"
```

## Success Criteria

1. All core modules implemented and functional
2. CLI supports both one-shot commands and REPL mode
3. `--json` output works for all commands
4. All tests pass (100% pass rate)
5. TEST.md contains both plan and results
6. README.md documents installation and usage
7. CLI is available in PATH as `cli-anything-<software>`

## Reference

- **HARNESS.md**: `cli-anything-plugin/HARNESS.md` — the methodology bible
- **Example harnesses**: `gimp/agent-harness/`, `blender/agent-harness/`, etc.
- **REPL skin**: `cli-anything-plugin/repl_skin.py`
