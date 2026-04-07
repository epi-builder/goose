<!-- GSD:project-start source:PROJECT.md -->
## Project

**goose**

goose is a local, extensible, open-source AI agent for engineering work, delivered as a Rust core with CLI and desktop interfaces. It automates end-to-end developer tasks including coding, tool use, debugging, workflow orchestration, MCP/ACP integrations, and local session management for individual developers and teams adopting OSS agent tooling.

**Core Value:** Developers can reliably run a powerful on-machine AI agent that automates real engineering work while remaining extensible, local-first, and open.

### Constraints

- **Tech stack**: Rust workspace with Electron/React/TypeScript desktop app — existing architecture and contributor workflows depend on it
- **Architecture**: Shared-core / thin-interface model — business logic belongs in `crates/goose/` and surfaces adapt it
- **Compatibility**: Cross-platform CLI and desktop support — project ships packaging/build paths for macOS, Linux, and Windows
- **Security**: Permission checks, egress inspection, CSP, and localhost trust handling are first-class — regressions here have outsized product risk
- **Contract boundary**: Server routes and desktop client must stay synchronized via OpenAPI generation — stale generated artifacts can break product behavior
- **Open source operations**: Planning docs should be git-tracked and comprehensible to outside contributors — this repo relies on transparent collaboration
<!-- GSD:project-end -->

<!-- GSD:stack-start source:codebase/STACK.md -->
## Technology Stack

## Overview
- Primary backend language: Rust 2021 workspace rooted at `Cargo.toml`.
- Primary desktop/frontend language: TypeScript with React/Electron in `ui/desktop/`.
- Documentation site: Docusaurus + React in `documentation/`.
- Supporting services/scripts: Node/Bun/JavaScript/TypeScript, Python, shell scripts.
## Core runtimes
- Rust toolchain pinned via `rust-toolchain.toml`.
- Node.js >= 18 for docs in `documentation/package.json` and Node `^24.10.0` for desktop in `ui/desktop/package.json`.
- pnpm workspace for UI packages in `ui/pnpm-workspace.yaml` and `ui/package.json`.
- Bun runtime used by `services/ask-ai-bot/package.json`.
- Hermit environment bootstrap in `bin/activate-hermit`.
## Rust workspace
- Workspace members defined in `Cargo.toml` as `crates/*` plus `vendor/v8`.
- Main crates:
## Rust libraries and frameworks
- Async/runtime: `tokio`, `futures`, `async-trait`, `async-stream` in `Cargo.toml` and `crates/goose/Cargo.toml`.
- HTTP/server: `axum`, `tower-http`, `reqwest`.
- Serialization/schema: `serde`, `serde_json`, `serde_yaml`, `schemars`, `utoipa`.
- Storage: `sqlx` with SQLite in `crates/goose/Cargo.toml`.
- CLI: `clap`, `cliclack`, `rustyline`, `bat` in `crates/goose-cli/Cargo.toml`.
- Tracing/telemetry: `tracing`, `tracing-subscriber`, optional OpenTelemetry crates in workspace deps.
- Parsing/analysis: `tree-sitter-*` parsers in root `Cargo.toml`; surfaced by `crates/goose/src/bin/analyze_cli.rs`.
- MCP/ACP protocols: `rmcp`, `agent-client-protocol-schema`, `sacp`.
## Rust feature model
- Shared default features across `crates/goose`, `crates/goose-cli`, and `crates/goose-server`:
- TLS is mutually exclusive in `crates/goose/src/lib.rs`: either `rustls-tls` or `native-tls` must be enabled.
- Optional GPU/local inference features include `cuda`, `metal`, `llama-cpp-2`, Candle, tokenizers, and Whisper-related dependencies in `crates/goose/Cargo.toml`.
## Desktop stack
- Electron desktop app entry: `ui/desktop/src/main.ts`.
- Renderer stack: React 19 + TypeScript + React Router in `ui/desktop/src/App.tsx`.
- Build system: Vite + Electron Forge in `ui/desktop/vite*.mts` and `ui/desktop/forge.config.ts`.
- Styling/UI:
- API client generation:
## Docs and auxiliary apps
- Docusaurus docs site in `documentation/` with custom scripts like `documentation/scripts/generate-docs-map.js`.
- ACP UI package in `ui/acp/`.
- Text package in `ui/text/`.
- Install link generator in `ui/install-link-generator/`.
- Discord bot service in `services/ask-ai-bot/`.
- OIDC proxy in `oidc-proxy/`.
## Build and task tooling
- Primary task runner: `Justfile`.
- Key commands from `Justfile`:
- Git hooks/tooling in desktop package: `husky`, `lint-staged`.
## Configuration and environment
- Rust lint/security configs: `clippy.toml`, `deny.toml`.
- Cross-platform/release configs: `Cross.toml`, `flake.nix`, `Dockerfile`.
- App/user configuration paths handled in Rust under `crates/goose/src/config/` and desktop settings in `ui/desktop/src/utils/settings` plus `ui/desktop/src/main.ts`.
## Notable technical themes
- Local-first agent architecture with local session persistence and local desktop backend.
- Strong protocol orientation around MCP/ACP.
- Mixed Rust + TypeScript product boundary where server OpenAPI drives desktop client code.
- Large provider surface area in `crates/goose/src/providers/` supporting multiple LLM backends.
<!-- GSD:stack-end -->

<!-- GSD:conventions-start source:CONVENTIONS.md -->
## Conventions

## Overall coding style
## Rust conventions
### Error handling
- Prefer `anyhow::Result` for application/service flows, explicitly called out in `AGENTS.md`.
- Domain-specific errors also exist, e.g. `SchedulerError` in scheduler-related code.
- Errors are generally propagated with `?` and selective contextualization.
- Repository guidance discourages low-value error context strings that restate obvious failures.
### Naming
- Modules/files use `snake_case`, e.g. `permission_judge.rs`, `session_manager.rs`.
- Types use `PascalCase`, e.g. `Agent`, `AgentConfig`, `GoosePlatform`.
- Constants use `SCREAMING_SNAKE_CASE`, e.g. `DEFAULT_MAX_TURNS` in `crates/goose/src/agents/agent.rs`.
### Architectural conventions
- Core logic belongs in `crates/goose/`.
- CLI is expected to call into core rather than reimplement logic in `crates/goose-cli/`.
- Desktop-facing backend features are expected to surface through `crates/goose-server/src/routes/` and OpenAPI regeneration.
- Provider integrations implement shared abstractions; guidance points to `crates/goose/src/providers/base.rs`.
### Comments
- prefer self-documenting code
- do not comment obvious operations
- only comment non-obvious business logic, why, or complex algorithms
### Feature flags and compile-time guards
- TLS feature selection is enforced with compile errors in `crates/goose/src/lib.rs`.
- Crates consistently mirror shared features like `code-mode`, `local-inference`, and TLS modes.
## TypeScript / React conventions
### Component structure
- React components are predominantly PascalCase filenames, e.g. `SettingsView.tsx`, `ExtensionInstallModal.tsx`.
- Utility/helper modules use camel/snake-ish lowercase names, e.g. `navigationUtils.ts`, `toolIconMapping.tsx`.
- Route/view composition is centralized in `ui/desktop/src/App.tsx`.
### State and hooks
- Shared state via React Contexts in `ui/desktop/src/contexts/` and `ui/desktop/src/components/*Context*.tsx`.
- Custom hooks are placed under `ui/desktop/src/hooks/` and named `useX`, e.g. `useChatStream.ts`, `useSessionEvents.ts`.
### Styling
- Tailwind utility classes are widely used inline in JSX.
- Reusable UI primitives are grouped under `ui/desktop/src/components/ui/`.
- React/Radix composition is preferred over bespoke DOM patterns.
### Generated code separation
- Generated API code is isolated under `ui/desktop/src/api/`.
- Manual edits are expected elsewhere; `ui/desktop/openapi.json` is explicitly not to be hand-edited.
## Testing conventions
- Rust tests often live in `crates/<crate>/tests/` for integration coverage.
- UI tests use `*.test.ts` / `*.test.tsx` naming and are often colocated with source or placed in `__tests__/` directories.
- Integration-style desktop tests live in `ui/desktop/tests/integration/`.
## Tooling conventions
### Formatting and linting
- Rust formatting: `cargo fmt`
- Rust linting: `cargo clippy --all-targets -- -D warnings`
- Desktop lint/typecheck: `cd ui/desktop && pnpm run lint:check`
- Prettier is used for frontend formatting via scripts in `ui/desktop/package.json`.
### Dependency management
- Rust guidance says use `cargo add` instead of editing `Cargo.toml` by hand.
- UI uses pnpm workspaces.
- Generated/OpenAPI flows are handled through `just generate-openapi`.
## Security and permission conventions
- Permission checks are first-class and routed through dedicated modules in `crates/goose/src/permission/`.
- Security inspection modules exist for adversary and egress analysis in `crates/goose/src/security/`.
- Desktop applies CSP/URL restrictions and localhost certificate validation.
## Practical patterns visible in code
### Rust pattern examples
- Builder/config objects are common, e.g. `AgentConfig` in `crates/goose/src/agents/agent.rs`.
- Trait-based abstraction for providers and schedulers, e.g. `SchedulerTrait` implementations in tests.
- Strong use of `Arc`, `Mutex`, async streams, and channel-based coordination in agent runtime code.
### UI pattern examples
- Route wrappers in `ui/desktop/src/App.tsx` encapsulate navigation and side effects.
- Generated API functions are consumed from feature components/hooks instead of raw fetch logic.
- Feature directories group related components and tests together.
## Conventions to preserve when changing code
- Put reusable business logic in `crates/goose/`.
- Keep server routes feature-scoped in `crates/goose-server/src/routes/`.
- Regenerate OpenAPI/client after server contract changes.
- Keep frontend generated code separate from handwritten components.
- Follow no-noise comments and strict linting expectations.
- Prefer extending existing abstractions rather than adding parallel code paths.
<!-- GSD:conventions-end -->

<!-- GSD:architecture-start source:ARCHITECTURE.md -->
## Architecture

## System shape
- CLI via `crates/goose-cli/`
- Desktop app via `ui/desktop/` backed by local Rust server `crates/goose-server/`
## Main layers
### 1. Core domain layer
- Agent orchestration: `crates/goose/src/agents/`
- Provider abstraction and implementations: `crates/goose/src/providers/`
- Session persistence and history: `crates/goose/src/session/`
- Config and permissions: `crates/goose/src/config/`, `crates/goose/src/permission/`
- Security checks: `crates/goose/src/security/`
- Recipes and templates: `crates/goose/src/recipe/`
- Scheduling: `crates/goose/src/scheduler.rs`, `crates/goose/src/scheduler_trait.rs`
- Context/compaction management: `crates/goose/src/context_mgmt/`
### 2. Delivery surfaces
#### CLI surface
- Entry: `crates/goose-cli/src/main.rs`
- Command parsing and user interaction: `crates/goose-cli/src/cli.rs`, `crates/goose-cli/src/commands/`, `crates/goose-cli/src/session/`
- Depends on `goose` and `goose-mcp`.
#### Server surface
- Entry: `crates/goose-server/src/main.rs`
- Runs either:
- HTTP/API routes under `crates/goose-server/src/routes/`
- OpenAPI assembly in `crates/goose-server/src/openapi.rs`
#### Desktop surface
- Electron main process: `ui/desktop/src/main.ts`
- React renderer: `ui/desktop/src/renderer.tsx`, `ui/desktop/src/App.tsx`
- Generated API client to talk to local `goosed`: `ui/desktop/src/api/`
## Key architectural pattern
### Shared-core / thin-interface pattern
- CLI wraps the core for terminal interaction.
- Server wraps the core for HTTP/OpenAPI access.
- Desktop wraps server APIs for graphical interaction.
## Agent execution model
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
### Desktop request flow
### Server route composition
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
- `crates/goose/src/security/adversary_inspector.rs`
- `crates/goose/src/security/egress_inspector.rs`
- `crates/goose/src/security/security_inspector.rs`
- `crates/goose/src/permission/permission_judge.rs`
- `crates/goose/src/permission/permission_inspector.rs`
- localhost certificate pinning in `ui/desktop/src/main.ts`
- CSP construction in `ui/desktop/src/utils/csp.ts`
- protocol blocking in `ui/desktop/src/utils/urlSecurity.ts`
## Code generation boundaries
### OpenAPI boundary
- Server generates schema via `crates/goose-server/src/bin/generate_schema.rs`.
- Schema output feeds `ui/desktop/openapi.json`.
- TypeScript client code is generated into `ui/desktop/src/api/`.
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
<!-- GSD:architecture-end -->

<!-- GSD:skills-start source:skills/ -->
## Project Skills

No project skills found. Add skills to any of: `.claude/skills/`, `.agents/skills/`, `.cursor/skills/`, or `.github/skills/` with a `SKILL.md` index file.
<!-- GSD:skills-end -->

<!-- GSD:workflow-start source:GSD defaults -->
## GSD Workflow Enforcement

Before using Edit, Write, or other file-changing tools, start work through a GSD command so planning artifacts and execution context stay in sync.

Use these entry points:
- `/gsd-quick` for small fixes, doc updates, and ad-hoc tasks
- `/gsd-debug` for investigation and bug fixing
- `/gsd-execute-phase` for planned phase work

Do not make direct repo edits outside a GSD workflow unless the user explicitly asks to bypass it.
<!-- GSD:workflow-end -->



<!-- GSD:profile-start -->
## Developer Profile

> Profile not yet configured. Run `/gsd-profile-user` to generate your developer profile.
> This section is managed by `generate-claude-profile` -- do not edit manually.
<!-- GSD:profile-end -->
