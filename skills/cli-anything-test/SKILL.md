---
name: cli-anything-test
description: "Run tests for a CLI-Anything harness and update TEST.md with results. Use when: user wants to run the test suite for a generated CLI harness, verify all tests pass, and update documentation with results."
metadata: {"openclaw":{"emoji":"🧪"}}
---

# cli-anything-test — Run & Document Tests

Run the test suite for a CLI-Anything harness and update TEST.md with results.

## When to Use

- After building or refining a CLI harness
- To verify all tests pass before publishing
- To update TEST.md with latest test results

## Arguments

| Arg | Required | Description |
|-----|----------|-------------|
| `<software-path-or-repo>` | ✅ | Local path to source, or GitHub URL (will be cloned) |

## Methodology

**First, read the test standards:**

```
read {baseDir}/../../cli-anything-plugin/HARNESS.md
```

> HARNESS.md defines what constitutes a passing test suite, expected structure, and test layers.

## Steps

### Step 1: Locate the Harness

Derive the software name from the path:

```
# Verify the harness exists
exec command:"ls <software>/agent-harness/cli_anything/<software>/tests/"
```

If a GitHub URL was given, clone first:

```
exec command:"git clone <url> /tmp/<software-name>"
```

### Step 2: Run Tests

```
exec command:"cd <software>/agent-harness && python3 -m pytest cli_anything/<software>/tests/ -v -s --tb=short" timeout:180
```

Capture the full output.

### Step 3: Verify Subprocess Backend

Check that installed-command tests work:

```
exec command:"cd <software>/agent-harness && CLI_ANYTHING_FORCE_INSTALLED=1 python3 -m pytest cli_anything/<software>/tests/ -v -s --tb=short -k TestCLISubprocess" timeout:120
```

Confirm `[_resolve_cli] Using installed command:` appears in output.

### Step 4: Update TEST.md

If all tests pass, use `edit` to update the test results section in `tests/TEST.md`:

```markdown
## Test Results

Last run: YYYY-MM-DD HH:MM:SS

\`\`\`
[full pytest -v --tb=no output]
\`\`\`

**Summary**: N passed in X.XXs
```

### Step 5: Report Status

Summarize:
- Total tests, passed, failed
- Any warnings or skipped tests
- Whether TEST.md was updated

## Failure Handling

If tests fail:

1. Show which tests failed and why
2. **Do NOT** update TEST.md (preserve previous passing results)
3. Suggest fixes based on error messages
4. Offer to re-run after fixes are applied

## Test Layers

| Layer | What it tests | File |
|-------|--------------|------|
| Unit tests | Core functions in isolation | `test_core.py` |
| E2E tests (native) | Project file generation pipeline | `test_full_e2e.py` |
| E2E tests (backend) | Real software invocation + output | `test_full_e2e.py` |
| CLI subprocess | Installed command via subprocess | `test_full_e2e.py::TestCLISubprocess` |

## Reference

- **HARNESS.md**: `cli-anything-plugin/HARNESS.md`
- **Test standards**: See "Phase 5: Test Implementation" and "Testing Strategy" sections in HARNESS.md
