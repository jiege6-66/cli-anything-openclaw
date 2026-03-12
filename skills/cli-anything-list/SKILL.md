---
name: cli-anything-list
description: "List all available CLI-Anything tools — both installed (pip) and generated (local directories). Use when: user wants to see which CLI harnesses are available, their status (installed vs generated), and where they are located."
metadata: {"openclaw":{"emoji":"📋"}}
---

# cli-anything-list — List Available CLI Tools

Discover all CLI-Anything tools on the system: installed packages and locally generated harnesses.

## When to Use

- User wants to see what CLI harnesses are available
- Before running tests or validation, to confirm which tools exist
- To get an overview of the CLI-Anything ecosystem on this machine

## Arguments

| Arg | Required | Description |
|-----|----------|-------------|
| `--path <dir>` | ❌ | Directory to search for generated CLIs (default: `.`) |
| `--depth <n>` | ❌ | Max recursion depth for scanning (default: unlimited) |

## Steps

### 1. Find Installed CLIs

Use `exec` to check for pip-installed CLI-Anything packages:

```
exec command:"pip list 2>/dev/null | grep -i cli-anything"
```

Also check which executables are on PATH:

```
exec command:"compgen -c 2>/dev/null | grep '^cli-anything-' | sort -u || ls /usr/local/bin/cli-anything-* 2>/dev/null"
```

### 2. Find Generated (Local) CLIs

Scan for local harness directories:

```
exec command:"find <search-path> -maxdepth <depth> -path '*/agent-harness/cli_anything/*/__init__.py' -type f 2>/dev/null"
```

For each found harness, extract:
- Software name (from directory structure)
- Version (from `setup.py` if present)
- Path to the harness

### 3. Check Each Tool's Status

For each discovered tool:

```
exec command:"which cli-anything-<software> 2>/dev/null && echo 'installed' || echo 'generated only'"
exec command:"cli-anything-<software> --help 2>/dev/null | head -5"
```

### 4. Present Results

Format as a table:

```
CLI-Anything Tools

Name            Status      Version   Source
─────────────────────────────────────────────────────
gimp            installed   1.0.0     ./gimp/agent-harness
blender         installed   1.0.0     ./blender/agent-harness
inkscape        generated   1.0.0     ./inkscape/agent-harness
audacity        generated   1.0.0     ./audacity/agent-harness

Total: 4 tools (2 installed, 2 generated only)
```

## Notes

- Installed CLIs are found via `pip list` and `which`
- Generated CLIs are found by scanning the filesystem for `agent-harness/cli_anything/` directories
- A tool can be both generated and installed (installed takes precedence in status display)
- Use `--depth 0` to scan only the specified directory, `--depth 1` for one level deep, etc.

## Reference

- **Generated harness structure**: See `cli-anything` skill for the expected directory layout
