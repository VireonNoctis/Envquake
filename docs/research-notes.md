# Envquake Research Notes

## Research question
Which undeclared environment assumptions cause tests to behave differently, and how can they be exposed through controlled experiments without destabilizing a developer or CI machine?

## Relevant practice

pytest's monkeypatch fixture can safely set or delete environment variables within test scope and restore state afterward. Its documentation also recommends making dependencies explicit where possible.
Reference: https://docs.pytest.org/en/stable/how-to/monkeypatch.html

The Twelve-Factor App's configuration principle argues that deploy-specific configuration should be external to code.
Reference: https://12factor.net/config

**Design implication:** the core experiment must run tests in a child process with a constructed environment, rather than modifying the runner's parent shell. Envquake should expose assumptions, not normalize unstable production systems into an unsafe state.

## What to vary
Initial candidates include missing versus present environment variables, empty values, locale, timezone, working directory, PATH order, temporary directory, line endings, declared dependency versions, and filesystem case behaviour when the platform supports a safe fixture.

Every profile must name its variations, expected scope, and limits. Variables likely to contain credentials, tokens, or private data are excluded by default.

## Experimental method
1. Run a baseline under a captured profile.
2. Apply one documented variation.
3. Execute the same command, tests, working tree, and toolchain.
4. Compare results, exit status, timeout state, and output fingerprints.
5. Repeat candidates when failures are nondeterministic.
6. Test selected combinations only after single-variable results provide useful evidence.

One-factor experiments aid attribution; pairwise tests can reveal interactions. Neither explores all possible environments.

## Threats to validity

| Threat | Consequence | Response |
|---|---|---|
| Multiple variables change together | Cause cannot be attributed | Change one dimension first |
| Flaky tests | Noise resembles an assumption | Repeat baseline and candidate runs |
| Secret values captured | Credentials leak into logs | Allowlist variables and redact values |
| Parent environment mutated | Developer session is damaged | Child process with explicit environment |
| Experiment touches real data | Test has side effects | Temporary workspace, safety profile, opt-in |
| Variation exceeds real conditions | False alarms | Document why each profile is plausible |
| Combination space grows rapidly | Cost and noisy results | Bounded profiles and prioritization |
| Different toolchains | Environment variation is confounded | Record executable versions and dependency lock data |

## Open questions
1. How should a user declare a safe baseline for each project?
2. Which variations should be built in and which must be project-specific?
3. What minimization algorithm can shrink a failing profile without hiding interactions?
4. How should test flakiness be reported?
5. Which runner isolation guarantees are required for each variation class?

## Research exit criteria
A prototype must demonstrate that it leaves the parent process unchanged, does not print secret values, reproduces a known environment-dependent failure, and reports unknown or unstable causes honestly.
