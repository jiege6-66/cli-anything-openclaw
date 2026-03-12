---
name: cli-anything-validate
description: "Validate a CLI-Anything harness against HARNESS.md standards and best practices. Use when: user wants to check if a generated CLI meets all quality standards before publishing or sharing — covers directory structure, required files, CLI implementation, tests, documentation, packaging, and code quality."
metadata: {"openclaw":{"emoji":"✅"}}
---

# cli-anything-validate — Standards Validation

Validate a CLI-Anything harness against the quality standards defined in HARNESS.md.

## When to Use

- Before publishing a generated CLI harness
- After refining, to ensure standards are still met
- As a quality gate in a CI/review workflow

## Arguments

| Arg | Required | Description |
|-----|----------|-------------|
| `<software-path-or-repo>` | ✅ | Local path to source, or GitHub URL (will be cloned) |

## Methodology

**First, read the validation standards:**

```
read {baseDir}/../../cli-anything-plugin/HARNESS.md
```

> Every check below maps to a requirement in HARNESS.md.

## Validation Checks

Run these checks using `exec` and `read`. Report pass/fail for each category.

### 1. Directory Structure

```
exec command:"test -d <software>/agent-harness/cli_anything/<software> && echo PASS || echo FAIL"
exec command:"test ! -f <software>/agent-harness/cli_anything/__init__.py && echo 'PASS (no namespace __init__)' || echo 'FAIL (namespace __init__ exists)'"
exec command:"test -f <software>/agent-harness/cli_anything/<software>/__init__.py && echo PASS || echo FAIL"
exec command:"ls <software>/agent-harness/cli_anything/<software>/{core,utils,tests}/ 2>/dev/null && echo PASS || echo FAIL"
```

### 2. Required Files

Check for existence of:
- `README.md`
- `<software>_cli.py` (main CLI entry point)
- `core/project.py`, `core/session.py`, `core/export.py`
- `tests/TEST.md`, `tests/test_core.py`, `tests/test_full_e2e.py`
- `../<SOFTWARE>.md` (software-specific SOP)

```
exec command:"for f in README.md <software>_cli.py core/project.py core/session.py core/export.py tests/TEST.md tests/test_core.py tests/test_full_e2e.py; do test -f <software>/agent-harness/cli_anything/<software>/$f && echo \"PASS: $f\" || echo \"FAIL: $f\"; done"
```

### 3. CLI Implementation Standards

Use `exec` with `grep` to verify:

- Uses Click framework (`import click`)
- Has command groups (`@click.group`)
- Implements `--json` flag
- Implements `--project` flag
- Has `handle_error` decorator
- Has REPL mode
- Has global session state

### 4. Core Module Standards

Check that core modules have required functions:

```
exec command:"grep -c 'def create\|def open\|def save\|def info' <software>/agent-harness/cli_anything/<software>/core/project.py"
exec command:"grep -c 'class Session\|def undo\|def redo\|def snapshot' <software>/agent-harness/cli_anything/<software>/core/session.py"
```

### 5. Test Standards

Verify test structure:

```
exec command:"grep -c 'class TestCLISubprocess' <software>/agent-harness/cli_anything/<software>/tests/test_full_e2e.py"
exec command:"grep '_resolve_cli' <software>/agent-harness/cli_anything/<software>/tests/test_full_e2e.py"
```

### 6. Documentation Standards

Check README.md completeness and that no duplicate HARNESS.md exists in the harness.

### 7. PyPI Packaging Standards

```
exec command:"grep 'find_namespace_packages' <software>/agent-harness/setup.py"
exec command:"grep 'console_scripts' <software>/agent-harness/setup.py"
exec command:"grep 'python_requires' <software>/agent-harness/setup.py"
```

### 8. Code Quality

```
exec command:"cd <software>/agent-harness && python3 -c 'import cli_anything.<software>' 2>&1"
exec command:"python3 -m py_compile <software>/agent-harness/cli_anything/<software>/<software>_cli.py 2>&1"
```

## Output Format

Generate a validation report:

```
CLI Harness Validation Report
Software: <software>
Path: <path>

Directory Structure    (N/N checks passed)
Required Files         (N/N files present)
CLI Implementation     (N/N standards met)
Core Modules           (N/N standards met)
Test Standards         (N/N standards met)
Documentation          (N/N standards met)
PyPI Packaging         (N/N standards met)
Code Quality           (N/N checks passed)

Overall: PASS | FAIL (total/total checks)
```

## Reference

- **HARNESS.md**: `cli-anything-plugin/HARNESS.md`
- **Validation details**: See "Directory Structure", "Key Principles", and "Rules" sections in HARNESS.md
