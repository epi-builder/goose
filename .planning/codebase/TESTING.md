# TESTING

## Overview
Testing is split across Rust integration/unit tests, desktop Vitest tests, Playwright-based end-to-end checks, and GitHub Actions CI enforcement.

## Rust testing
### Main commands
- Full test suite: `cargo test`
- Core crate: `cargo test -p goose`
- Specific integration suite noted in guidance: `cargo test --package goose --test mcp_integration_test`

### Test locations
- Core tests: `crates/goose/tests/`
- ACP tests: `crates/goose-acp/tests/`
- Server tests: `crates/goose-server/tests/`
- Additional scenario-oriented helpers in `crates/goose-cli/src/scenario_tests/`

### Example test files
- `crates/goose/tests/agent.rs`
- `crates/goose/tests/mcp_integration_test.rs`
- `crates/goose/tests/adversary_inspector_tests.rs`
- `crates/goose/tests/session_id_propagation_test.rs`
- `crates/goose-server/tests/tls_test.rs`
- `crates/goose-acp/tests/provider_test.rs`

### Rust test characteristics
- Mix of integration tests and module-focused tests.
- Heavy use of async `#[tokio::test]`.
- Test doubles/mocks appear via custom mock types and crates like `wiremock`, `mockall`, and `serial_test` from `crates/goose/Cargo.toml` dev-dependencies.
- Some tests use fixture data and replay artifacts, e.g. `crates/goose/tests/mcp_replays/`.

## Desktop/UI testing
### Test frameworks
- Vitest is the main unit/integration test runner in `ui/desktop/package.json`.
- React Testing Library is used for component testing.
- Playwright is configured for end-to-end tests in `ui/desktop/playwright.config.ts`.

### Main UI commands
- `cd ui/desktop && pnpm test`
- `cd ui/desktop && pnpm run test:run`
- `cd ui/desktop && pnpm run test:integration`
- `cd ui/desktop && pnpm run test-e2e`
- `cd ui/desktop && pnpm run lint:check`

### UI test locations
- Source-colocated tests such as:
  - `ui/desktop/src/App.test.tsx`
  - `ui/desktop/src/components/MarkdownContent.test.tsx`
  - `ui/desktop/src/utils/htmlSecurity.test.ts`
- Nested `__tests__/` folders such as:
  - `ui/desktop/src/components/alerts/__tests__/`
  - `ui/desktop/src/utils/__tests__/`
- Integration tests in `ui/desktop/tests/integration/`

### Notable integration pattern
`ui/desktop/tests/integration/goosed.test.ts` spawns a real `goosed` process and exercises it using the generated TypeScript API client. This gives the repo a valuable cross-boundary integration test covering server + client contract behavior.

## CI coverage
Primary CI workflow: `.github/workflows/ci.yml`.

### CI jobs of note
- Rust format check: `cargo fmt --check`
- Rust build and tests: cargo test commands from `crates/`
- Rust clippy lint: `cargo clippy --workspace --all-targets --exclude v8 -- -D warnings`
- OpenAPI up-to-date check: `just check-openapi-schema`
- Desktop lint and tests: `pnpm run lint:check`, `pnpm run test:run`

### Additional automation
- Dependency advisories: `.github/workflows/cargo-deny.yml`
- Security posture: `.github/workflows/scorecard.yml`
- Various packaging/bundle smoke flows in `.github/workflows/`

## Test data and fixtures
- ACP fixture/server helpers in `crates/goose-acp/tests/fixtures/`
- Recorded MCP interactions in `crates/goose/tests/mcp_replays/`
- Desktop test setup in `ui/desktop/src/test/setup.ts`
- Binary/server integration setup under `ui/desktop/tests/integration/`

## Quality gates implied by repo guidance
Per `AGENTS.md`, if changes are requested and relevant, expected checks are:
- `cargo fmt`
- `cargo build`
- `cargo test -p <crate>`
- `cargo clippy --all-targets -- -D warnings`
- `just generate-openapi` for server changes
- `cd ui/desktop && pnpm test` for UI work

## Observed strengths
- Good breadth across backend, API boundary, and UI tests.
- CI enforces formatting, linting, contract freshness, and desktop tests.
- Real-process integration test of `goosed` from TypeScript is especially useful.
- Security-sensitive areas have targeted tests such as CSP/HTML security tests.

## Gaps / watch items
- The codebase is large, so coverage is likely uneven despite many tests.
- Some complex core files likely rely on integration coverage more than small focused unit tests.
- Generated-client/server contract correctness depends on continuing to run OpenAPI freshness checks.
- Scenario tests in CLI are substantial and may be heavier/slower than standard unit tests.