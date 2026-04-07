# STRUCTURE

## Repository layout
Top-level structure is split by product/runtime rather than by a single app package.

- `crates/` — Rust workspace crates
- `ui/` — TypeScript/Electron/UI-related packages
- `documentation/` — Docusaurus docs site
- `services/` — auxiliary services like Discord bot
- `scripts/` — repo automation and diagnostics scripts
- `evals/` — benchmarking/evaluation assets
- `examples/` — sample integrations and helper examples
- `workflow_recipes/` — workflow recipe definitions
- `vendor/` — vendored dependencies such as `vendor/v8`

## Rust crate structure
### `crates/goose/`
Core library crate. High-value directories/files:
- `crates/goose/src/lib.rs` — module exports and feature guards
- `crates/goose/src/agents/` — agent runtime, extension manager, prompt manager, platform tools
- `crates/goose/src/providers/` — provider integrations and provider abstractions
- `crates/goose/src/session/` — local persistence and session operations
- `crates/goose/src/security/` — adversary/egress/security inspectors
- `crates/goose/src/config/` — config loading, permissions, provider declarations
- `crates/goose/src/recipe/` — recipe read/build/validate logic
- `crates/goose/src/prompts/` — prompt templates
- `crates/goose/tests/` — integration-style Rust tests

Naming pattern: domain-oriented modules, mostly snake_case files and directories.

### `crates/goose-cli/`
CLI wrapper crate.
- `crates/goose-cli/src/main.rs` — CLI binary entry
- `crates/goose-cli/src/cli.rs` — top-level CLI orchestration
- `crates/goose-cli/src/commands/` — subcommand handlers
- `crates/goose-cli/src/session/` — interactive/session terminal UX
- `crates/goose-cli/src/scenario_tests/` — scenario test helpers and recordings

### `crates/goose-server/`
HTTP/server wrapper crate.
- `crates/goose-server/src/main.rs` — `goosed` entry
- `crates/goose-server/src/routes/` — Axum route modules by feature area
- `crates/goose-server/src/commands/agent.rs` — agent server startup path
- `crates/goose-server/src/openapi.rs` — OpenAPI assembly
- `crates/goose-server/src/bin/generate_schema.rs` — schema generator binary
- `crates/goose-server/tests/` — integration tests

Route naming is feature-oriented: `session.rs`, `schedule.rs`, `config_management.rs`, etc.

### Other Rust crates
- `crates/goose-mcp/` — MCP server implementations and helpers
- `crates/goose-acp/` — ACP protocol/client/server support
- `crates/goose-acp-macros/` — proc macros
- `crates/goose-sdk/` — SDK-oriented crate
- `crates/goose-test/`, `crates/goose-test-support/` — test utilities

## UI structure
### `ui/desktop/`
Main Electron app.

Important locations:
- `ui/desktop/src/main.ts` — Electron main process
- `ui/desktop/src/preload.ts` — preload bridge
- `ui/desktop/src/renderer.tsx` — React bootstrap
- `ui/desktop/src/App.tsx` — main router/view composition
- `ui/desktop/src/api/` — generated API client
- `ui/desktop/src/components/` — feature/component tree
- `ui/desktop/src/contexts/` and `ui/desktop/src/hooks/` — shared React state/hooks
- `ui/desktop/src/utils/` — renderer/main utilities
- `ui/desktop/tests/` — integration tests

### Component organization
`ui/desktop/src/components/` is grouped largely by product feature:
- `settings/`
- `sessions/`
- `recipes/`
- `schedule/`
- `extensions/`
- `apps/`
- `alerts/`
- `bottom_menu/`
- `Layout/`
- `ui/` for reusable primitives

Naming conventions are PascalCase React components, with colocated tests in `*.test.tsx` or `__tests__/` folders.

## Docs and service structure
### `documentation/`
- Docusaurus content in `documentation/docs/`
- Blog posts in `documentation/blog/`
- scripts in `documentation/scripts/`
- site source in `documentation/src/`

### `services/ask-ai-bot/`
- `clients/`, `commands/`, `events/`, `utils/`
- Bun/TS Discord bot service

## Generated and contract-bound locations
- `ui/desktop/openapi.json` — generated contract artifact; should not be edited manually
- `ui/desktop/src/api/` — generated TS client
- provider canonical/generated content around `crates/goose/src/providers/canonical/`

## Typical change paths
### Add core product behavior
- Start in `crates/goose/src/`.
- Then wire into:
  - CLI in `crates/goose-cli/src/commands/` or session code
  - server routes in `crates/goose-server/src/routes/`
  - desktop UI in `ui/desktop/src/components/` if needed

### Add backend-powered desktop feature
- Core logic in `crates/goose/src/`
- route in `crates/goose-server/src/routes/`
- regenerate OpenAPI/client
- UI consumption in `ui/desktop/src/api/` callers and React components

### Add provider
- `crates/goose/src/providers/`
- possibly `crates/goose/src/config/`
- maybe desktop onboarding/settings views under `ui/desktop/src/components/settings/providers/`

## Tests and verification locations
- Rust tests: `crates/*/tests/`
- UI tests: `ui/desktop/src/**/*.test.ts(x)`
- UI integration: `ui/desktop/tests/integration/`
- CI workflows: `.github/workflows/`

## Structure observations
- The repo is intentionally monorepo-style.
- `crates/goose/src/` and `ui/desktop/src/components/` are the two densest areas.
- Code is mostly organized by domain/feature rather than strictly by technical layer inside each surface.
- Generated API code is clearly separated from handwritten UI code, which is helpful for maintenance.