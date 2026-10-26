# Envquake Validation Matrix

## Required tests

| Category | Case | Pass condition |
|---|---|---|
| Parent isolation | Add/remove variables in child profile | Parent environment unchanged |
| Secrets | Sentinel credential variable | Secret value absent from logs and report |
| Baseline | Same test run repeatedly | Variance is visible and repeat count recorded |
| Missing configuration | Required variable removed | Test outcome and triggering profile recorded |
| Empty vs absent | Variable set to empty versus missing | Conditions remain distinct |
| Locale/timezone | Alternate supported locale or timezone | Profile and outcome are reproducible |
| PATH | Controlled PATH order | Child-only change; executable identity recorded |
| Working directory | Run from temporary directory | No unintended write to original working tree |
| Timeouts | Hanging child | Clearly timed out; cleanup executed |
| Output limits | Large stdout/stderr | Safe cap and truncation marker |
| Pairwise combinations | Two interacting variables | Run budget enforced; profile recorded |
| Cleanup | Normal and interrupted runs | Temporary data removed or tracked for cleanup |
| Policy | Disallowed variation/profile | Blocked before process launch |

## Acceptance criteria
- Baseline and candidate use the same test selection and code revision.
- Only declared dimensions change.
- Parent shell and protected source tree remain unchanged.
- Profiles are deterministic and versioned.
- Secret values do not appear in stored reports.
- All outcomes, including timeout and incomplete, are explicit.
- Every finding includes a reproduction profile and command.
- Flaky evidence is labelled inconclusive rather than forced into a binary answer.

## Evaluation plan
Use fixtures where the expected result depends on a known missing variable, timezone, path, or working directory. Include negative controls where a variation should not change the result. Measure reproduced failures, false alarms, run overhead, cleanup reliability, and secret leakage. Document host OS and runner configuration for every result.
