---
name: cli-anything-refine
description: "Refine an existing CLI harness to expand coverage and add missing capabilities. Use when: a CLI harness already exists but needs more commands, better coverage, or a specific functionality area expanded. Performs gap analysis and iteratively adds new commands, tests, and documentation."
metadata: {"openclaw":{"emoji":"🔁"}}
---

# cli-anything-refine — Expand CLI Harness Coverage

Refine an **existing** CLI harness to improve coverage of the target software's capabilities. Run this after `cli-anything` has already built the initial harness.

## When to Use

- A CLI harness already exists for the target software
- User wants to expand coverage (more commands, more features)
- User wants to target a specific functionality area (e.g., "batch processing", "particle systems")

## Arguments

| Arg | Required | Description |
|-----|----------|-------------|
| `<software-path>` | ✅ | Local path to the software source code |
| `<focus-area>` | ❌ | Natural-language description of what to focus on |

**Examples:**

```
# Broad refinement — agent analyzes all gaps
cli-anything-refine ./gimp

# Focused — target a specific area
cli-anything-refine ./gimp "batch processing and scripting filters"
cli-anything-refine ./blender "particle systems and physics simulation"
```

## Methodology

**First, read the authoritative methodology document:**

```
read {baseDir}/../../cli-anything-plugin/HARNESS.md
```

> All new commands and tests must follow the same standards as the original build. HARNESS.md is the single source of truth.

## Steps

### Step 1: Inventory Current Coverage

Use `read` and `exec` to understand what's already built:

```
# Read the CLI entry point to see all current commands
read <software>/agent-harness/cli_anything/<software>/<software>_cli.py

# List all core modules
exec command:"find <software>/agent-harness/cli_anything/<software>/core/ -name '*.py' -exec head -5 {} +"

# Check current test coverage
exec command:"grep -c 'def test_' <software>/agent-harness/cli_anything/<software>/tests/test_core.py"
```

Build a coverage map: which functions are exposed via CLI vs. which are missing.

### Step 2: Analyze Software Capabilities

Re-scan the software source code:

```
# Find public APIs and scripting interfaces
exec command:"grep -r 'def ' <software-path>/src/ | head -50"
exec command:"find <software-path> -name '*.py' -path '*/api/*'"
```

If a **focus area** is provided, narrow the analysis to only that domain.

### Step 3: Gap Analysis

Compare current CLI coverage against the software's full capability set. Prioritize by:

1. **High impact** — commonly used functions missing from the CLI
2. **Easy wins** — functions with simple APIs that can be wrapped quickly
3. **Composability** — functions that unlock new workflows when combined

Present the gap report before implementing. If in an interactive session, confirm with the user which gaps to address.

### Step 4: Implement New Commands

Use `write` and `edit` to add new commands:

- Follow the same Click patterns as existing commands (per HARNESS.md)
- Add `--json` output support
- Integrate with session state
- Add core module functions in `core/` or `utils/`

### Step 5: Expand Tests

Use `write` and `edit` to add tests:

- Unit tests for every new function in `test_core.py`
- E2E tests for new commands in `test_full_e2e.py`
- Workflow tests combining new commands with existing ones

Run all tests (old + new) to ensure no regressions:

```
exec command:"cd <software>/agent-harness && python3 -m pytest cli_anything/<software>/tests/ -v --tb=short" timeout:120
```

### Step 6: Update Documentation

Use `edit` to update:

- `README.md` — new commands and usage examples
- `TEST.md` — new test results
- `<SOFTWARE>.md` — new coverage notes

## Success Criteria

- All existing tests still pass (no regressions)
- New commands follow the same architectural patterns (per HARNESS.md)
- New tests achieve 100% pass rate
- Coverage meaningfully improved
- Documentation updated

## Important Notes

- Refine is **incremental** — run it multiple times to steadily expand coverage
- Each run should focus on a **coherent set** of related functions
- Refine **never removes** existing commands — it only adds or enhances
- Only local paths accepted; if starting from a GitHub URL, use `cli-anything` first

## Reference

- **HARNESS.md**: `cli-anything-plugin/HARNESS.md`
- **Example harnesses**: `gimp/agent-harness/`, `blender/agent-harness/`, etc.
