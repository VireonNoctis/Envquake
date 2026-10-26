# Envquake

<p align="center">
  <strong>Shake the assumptions. Find the hidden dependency.</strong><br/>
  A controlled test runner for discovering environment-sensitive behaviour in software.
</p>

---

## The problem

Tests often pass only because a developer's machine happens to provide a variable, locale, timezone, path, working directory, dependency, filesystem behaviour, or network condition that the code never declared. In CI or production, that assumption changes and the same test fails—or worse, the program behaves incorrectly without failing.

Envquake is planned as a CLI and test harness that reruns selected tests under controlled environmental variations, then records which conditions change the outcome.

It is not chaos for chaos's sake. Variations are explicit, bounded, reversible, and designed to reveal hidden dependencies.

## Planned capabilities

- Inventory selected environment inputs from an explicit allowlist.
- Define named, versioned test profiles.
- Run each test in a child process with a controlled environment.
- Perturb one dimension at a time before exploring selected combinations.
- Cover missing and empty variables, alternate locale/timezone, changed working directories, altered PATH, temporary directories, and declared dependency versions.
- Capture commands, exit codes, timeouts, output hashes, and test results.
- Compare outcomes against a baseline and identify the smallest known change that explains a failure.
- Emit JSON/JSONL reports suitable for CI and reproducible bug reports.
- Integrate with pytest without requiring tests to mutate the developer's global shell.

## Workflow

1. Select a test command and explicit profile.
2. Capture a baseline run under the declared environment.
3. Apply one controlled variation to an isolated child process.
4. Run the same test selection and compare outcomes.
5. Repeat promising candidates or run selected pairwise combinations.
6. Produce a report showing the altered condition, baseline, changed result, and reproduction command.
7. Add confirmed assumptions to project documentation or tests.

## Planned technology stack

| Area | Planned technology | Purpose |
|---|---|---|
| CLI and runner | Python 3.13+ | Environment profiles, subprocess orchestration, outcome comparison |
| Test integration | pytest | Select tests and report test outcomes |
| Property-based cases | Hypothesis, if useful | Explore structured input/environment combinations |
| Profile format | TOML or YAML with schema validation | Explicit, reviewable variations |
| Reports | JSON and JSONL | Reproducible machine-readable results |
| Optional local history | SQLite only if useful | Store experiment metadata |
| Isolation | Child processes first; Docker for selected reproducible profiles | Keep environment changes contained |
| Tests and CI | pytest + GitHub Actions | Regression testing on supported runners |

The first version should not require Rust or a web dashboard. Add components only where they improve measured correctness or usability.

## Documentation

- [Research notes](docs/research-notes.md)
- [Mitigation strategy](docs/mitigation-strategy.md)
- [Architecture research](docs/architecture-research.md)
- [Validation matrix](docs/validation-matrix.md)

## Safety boundaries

Envquake must not alter the developer's parent shell, modify system-wide settings, run against production by default, or print secret environment values into a report. Profiles use allowlisted variable names and safe synthetic values. Destructive or privileged experiments are out of scope for the default runner.

## Principles

- Every variation is explicit and reproducible.
- Baseline and candidate runs use the same test selection and recorded toolchain.
- One factor changes at a time unless a named combination experiment is requested.
- Failed, skipped, timed-out, and passed outcomes remain distinct.
- Secret values are redacted; reports describe the changed variable without leaking credentials.
- A changed outcome indicates an environmental dependency, not automatically a software defect.

## Project status

**Research / early planning.** Planned features become supported only after implementation and repeatable test evidence.

## Roadmap

- [ ] Define profile and result schemas.
- [ ] Implement baseline and controlled child-process execution.
- [ ] Add safe variations for missing variables, locale, timezone, PATH, and working directory.
- [ ] Add deterministic outcome comparison and reproduction commands.
- [ ] Integrate with pytest and CI.
- [ ] Add bounded pairwise experiments and evidence-based minimization.

## Research references

- pytest monkeypatch documentation: https://docs.pytest.org/en/stable/how-to/monkeypatch.html
- The Twelve-Factor App, configuration: https://12factor.net/config
- Docker Engine security: https://docs.docker.com/engine/security/
- Hypothesis property-based testing: https://hypothesis.readthedocs.io/

## Contributing

Include a reproducible test fixture, the baseline environment, exactly which dimension changes, expected outcomes, and a check that the host environment is unchanged afterward.

## License

Add an explicit licence before treating this repository as licensed for reuse.
