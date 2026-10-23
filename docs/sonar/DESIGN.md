# SonarQube Cloud — Design

## Proposed Design
- Connect this repository to SonarQube Cloud under organization `manuelselch`, with project key `ManuelSelch_pi-sdk-typescript`.
- Add `sonar-project.properties` with UTF-8 encoding and the Vitest LCOV report path. Leave `sonar.sources` and `sonar.tests` unset. Use `sonar.exclusions` for `test/**`, `protocol/**`, `dist/**`, `coverage/**`, `node_modules/**`, `scripts/**` and root configuration files.
- Add a Vitest coverage provider matching the installed Vitest version. Measure all TypeScript source files, including unexecuted files, and produce text and LCOV reports through a dedicated coverage script.
- Run coverage in the existing TypeScript CI job, preserving its build, typecheck, package and protocol checks. Upload LCOV for a dependent Sonar job.
- Reuse Pi Chat’s pinned Sonar scan action, full-history checkout, repository-variable configuration and fork-PR guard. Wait for the quality gate and fail the job if it fails.

## Decisions & Trade-offs
- Use Cloud rather than the separate SonarQube MCP server, matching the confirmed target.
- Keep ordinary local tests unchanged; coverage is an explicit command used by CI.
- Use exclusion-based scope as requested. Tests, generated artifacts, scripts and configuration files are excluded from analysis, not classified as test code. Tests still run and contribute source coverage.
- Current SonarQube Cloud documentation still supports `sonar.sources` and `sonar.tests`; using exclusions is a configuration preference, not a verified deprecation. With the CLI defaults, the source baseline is the project root and the test baseline is empty. Exclusions reduce that baseline; they do not create a test scope.
- Gate scanning on configured project/organization variables; never expose the analysis token to fork pull requests.
- GitHub requires a `SONAR_TOKEN` secret. Existing secret values cannot be copied out of another repository. Cloud project import, disabling automatic analysis, and token provisioning may require the user’s authenticated setup.
- A failing quality-gate job does not itself create a branch-protection rule.

## Verification
- Run coverage and confirm that LCOV includes SDK source files.
- Run all existing local pipeline suites.
- Check workflow syntax, dependency-version alignment and scanner scope.
- Verify configured GitHub variable/secret names without reading secret values. Verify a live scan only when the changes are pushed and Cloud credentials are available.

## Open Questions
None.
