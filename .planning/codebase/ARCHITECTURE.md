# ARCHITECTURE

## System shape
This is a multi-surface AI agent product with a shared Rust core and two primary user-facing entry points:
- CLI via `crates/goose-cli/`
- Desktop app via `ui/desktop/` backed by local Rust server `crates/goose-server/`

The architectural center of gravity is `crates/goose/`, which contains agent execution, providers, sessions, permissions, security, recipe handling, extensions, and scheduling.

## Main layers

### 1. Core domain layer
Implemented in `crates/goose/src/`.

Major subsystems:
- Agent orchestration: `crates/goose/src/agents/`
- Provider abstraction and implementations: `crates/goose/src/providers/`
- Session persistence and history: `crates/goose/src/session/`
- Config and permissions: `crates/goose/src/config/`, `crates/goose/src/permission/`
- Security checks: `crates/goose/src/security/`
- Recipes and templates: `crates/goose/src/recipe/`
- Scheduling: `crates/goose/src/scheduler.rs`, `crates/goose/src/scheduler_trait.rs`
- Context/compaction management: `crates/goose/src/context_mgmt/`

This crate is intentionally the reusable business-logic layer shared by CLI and server.

### 2. Delivery surfaces
#### CLI surface
- Entry: `crates/goose-cli/src/main.rs`
- Command parsing and user interaction: `crates/goose-cli/src/cli.rs`, `crates/goose-cli/src/commands/`, `crates/goose-cli/src/session/`
- Depends on `goose` and `goose-mcp`.

#### Server surface
- Entry: `crates/goose-server/src/main.rs`
- Runs either:
  - `agent` HTTP server mode
  - `mcp` server mode
  - bundled-extension validation mode
- HTTP/API routes under `crates/goose-server/src/routes/`
- OpenAPI assembly in `crates/goose-server/src/openapi.rs`

#### Desktop surface
- Electron main process: `ui/desktop/src/main.ts`
- React renderer: `ui/desktop/src/renderer.tsx`, `ui/desktop/src/App.tsx`
- Generated API client to talk to local `goosed`: `ui/desktop/src/api/`

## Key architectural pattern
### Shared-core / thin-interface pattern
Non-trivial logic belongs in `crates/goose/`; interfaces mainly adapt it:
- CLI wraps the core for terminal interaction.
- Server wraps the core for HTTP/OpenAPI access.
- Desktop wraps server APIs for graphical interaction.

This pattern is explicitly reinforced by repository guidance in `AGENTS.md`.

## Agent execution model
The central runtime object is `Agent` in `crates/goose/src/agents/agent.rs`.

### Responsibilities of Agent
- Manage provider interaction
- Build prompts and tool lists
- Coordinate tool calls and confirmation routing
- Load/manage extensions via `ExtensionManager`
- Run permission and security inspection
- Persist/stream conversation/session state

### Supporting abstractions
- `ExtensionManager` in `crates/goose/src/agents/extension_manager.rs`
- Provider trait and implementations under `crates/goose/src/providers/base.rs` and sibling files
- Session lifecycle through `SessionManager` in `crates/goose/src/session/session_manager.rs`
- Tool inspection through `crates/goose/src/tool_inspection.rs` and `crates/goose/src/tool_monitor.rs`

## Data flow
### CLI request flow
1. User invokes `goose` via `crates/goose-cli/src/main.rs`.
2. CLI command/session code in `crates/goose-cli/src/commands/` or `crates/goose-cli/src/session/` builds runtime state.
3. Core `Agent` from `crates/goose/` executes provider/tool workflow.
4. Results stream back to terminal presentation code.
5. Sessions/messages persist through SQLite-backed `SessionManager`.

### Desktop request flow
1. Electron main process in `ui/desktop/src/main.ts` starts or connects to `goosed`.
2. React UI in `ui/desktop/src/App.tsx` invokes generated client functions from `ui/desktop/src/api/`.
3. Axum routes in `crates/goose-server/src/routes/` translate HTTP requests into core operations.
4. `crates/goose/` executes agent/session/provider logic.
5. Results return as REST/SSE/API responses to the renderer.

### Server route composition
`crates/goose-server/src/routes/mod.rs` merges route modules for:
- status
- reply
- action_required
- agent
- config_management
- prompts
- recipe
- session
- schedule
- setup
- telemetry
- tunnel
- gateway
- session_events
- sampling
- dictation
- features
- optional `local_inference`

## Persistence architecture
- Primary application data store: SQLite via `sqlx` in `crates/goose/src/session/session_manager.rs`.
- Sessions and messages are persisted locally.
- Schema versioning and migrations are implemented in code rather than via a separate migration directory.
- Some desktop settings are file-backed JSON in Electron main process code.

## Extension architecture
- Built-in platform tools/extensions live under `crates/goose/src/agents/platform_extensions/`.
- MCP integrations live in `crates/goose-mcp/` and core extension manager code.
- Desktop can expose richer capabilities like MCP UI through capability flags passed to `ExtensionManager` in `crates/goose/src/agents/agent.rs`.

## Security architecture
Security is integrated into the agent pipeline, not bolted on afterward.

Key modules:
- `crates/goose/src/security/adversary_inspector.rs`
- `crates/goose/src/security/egress_inspector.rs`
- `crates/goose/src/security/security_inspector.rs`
- `crates/goose/src/permission/permission_judge.rs`
- `crates/goose/src/permission/permission_inspector.rs`

Desktop also applies:
- localhost certificate pinning in `ui/desktop/src/main.ts`
- CSP construction in `ui/desktop/src/utils/csp.ts`
- protocol blocking in `ui/desktop/src/utils/urlSecurity.ts`

## Code generation boundaries
### OpenAPI boundary
- Server generates schema via `crates/goose-server/src/bin/generate_schema.rs`.
- Schema output feeds `ui/desktop/openapi.json`.
- TypeScript client code is generated into `ui/desktop/src/api/`.

This is an important contract boundary; server route changes must be reflected in generated client code.

## Testing architecture
- Rust tests are mostly crate-local and integration-style in `crates/*/tests/`.
- UI unit/integration tests use Vitest in `ui/desktop/src/**/*.test.ts(x)` and `ui/desktop/tests/integration/`.
- Some tests exercise the real `goosed` binary through the generated TS client in `ui/desktop/tests/integration/goosed.test.ts`.

## Architectural strengths
- Clear shared-core strategy reduces duplication across CLI and desktop.
- Well-separated delivery surfaces.
- Protocol-driven desktop/server boundary via OpenAPI.
- Security and permission checks are first-class concerns.
- Large provider ecosystem isolated under `providers/`.

## Architectural pressure points
- `crates/goose/src/` is very broad and acts as a large monolith crate.
- Desktop depends on generated API artifacts staying in sync with server changes.
- Core agent surface combines many responsibilities, especially in `crates/goose/src/agents/agent.rs`.
- Feature flags and provider permutations increase complexity across crates.