# Envquake Mitigation Strategy

Envquake should uncover environmental assumptions while containing the experiment.

| Risk | Control | Validation | Residual limitation |
|---|---|---|---|
| Parent shell altered | Use child processes with explicit environment dictionaries | Compare parent environment before/after | Child process can still create other side effects |
| Secrets logged | Allowlist variable names, redact values, exclude common credential variables | Secret sentinel must not appear in outputs | Applications may write secrets themselves |
| Production command run by accident | Require explicit command and working directory; dry-run profile preview; production patterns warned or blocked | CLI tests for dangerous profile configuration | User can still invoke commands outside the tool |
| Nondeterministic tests mislabelled | Repeat baseline and candidate runs; show variance | Flaky fixture with repeated outcome states | Some flakiness needs deeper debugging |
| Causal confusion | Change one factor at a time; record every difference | Single-variable reproducibility tests | Interactions may require multiple-factor tests |
| Excessive combinations | Explicit maximum run count and timeout budget | Large profile rejected or bounded | Important interactions may remain unexplored |
| Host data modified | Temporary working directory and safe profile defaults; refuse unknown destructive hooks | Verify protected fixture tree unchanged | Test code may touch arbitrary paths if allowed |
| Toolchain confounds results | Capture versions, command, profile hash, lockfile fingerprint | Same profile across declared toolchains | External services can change |
| CI output leaks | JSON report with redaction and bounded stdout/stderr | Secret fixture scan | Third-party build logs may have their own retention |

## Operating safeguards
- Do not modify system-wide configuration or the parent shell.
- Do not run experiments against production by default.
- Do not capture values for environment variables outside the declared allowlist.
- Never store credentials in experiment profiles.
- Apply timeouts and run-count budgets.
- Treat subprocess output as untrusted and bound its size.
- Mark interrupted or partial runs as incomplete.
- Make each profile reviewable before it executes.

## Release gates
The tool needs tests for parent-state preservation, secret redaction, timeout handling, cleanup, baseline stability, and reproducible profile serialization. Docker is an optional aid, not a complete security boundary by itself.
