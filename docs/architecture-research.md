# Envquake Architecture Research

## Pipeline
Load profile → validate policy → create isolated workspace → run baseline child process → run variations → classify outcomes → compare → emit report.

## Intended stack
- Python 3.13+ for CLI, profile model, subprocess runner, and comparison.
- pytest integration for selecting and interpreting tests.
- Hypothesis if property-based generation produces useful coverage.
- TOML or YAML profiles with Pydantic/JSON Schema validation.
- JSON/JSONL reports; SQLite only if local experiment indexing becomes useful.
- Docker for selected reproducible profiles, not as an unconditional requirement.
- GitHub Actions for cross-runner regression testing.

## Profile model
Each profile should identify test command, argument list, working directory, environment allowlist, variable mutations, locale/timezone options, timeout, maximum number of runs, cleanup directory, baseline expectations, redaction policy, and profile version. Do not accept arbitrary shell snippets when structured argument arrays suffice.

## Child process contract
Build the child environment from a documented baseline and explicit profile additions/removals. Capture exit code, signal/timeout, start/end, duration, capped stdout/stderr or hashes, tool versions, and profile identifier. Avoid logging environment values by default. The parent process remains unchanged.

## Variation planner
Single-variable variations are the default. Selected pairwise or multi-variable experiments require an explicit budget. If a candidate failure occurs, optional minimization can reduce the set of changes while preserving the failure; the minimized result must be rerun and labelled as a reproduction, not definitive causation.

## Failure states
Represent passed, failed, skipped, timed out, blocked by policy, environment setup failed, and incomplete separately. A missing result must never be silently interpreted as pass.

## Trust and safety boundaries
Test code can execute arbitrary local actions. Profiles must be explicit, resources bounded, and runs confined to a selected workspace where feasible. The tool should warn when a test command is opaque or may affect files outside the workspace. Reports and stdout are untrusted content.

## Performance
Enforce maximum runtime, output size, and profile fan-out. Record enough metadata for reproduction without storing secrets. Cache results only when the code revision, test selection, toolchain, profile, and relevant input fingerprints match.
