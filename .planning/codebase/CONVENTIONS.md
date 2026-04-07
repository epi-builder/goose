# CONVENTIONS

## Overall coding style
The repository follows idiomatic Rust in backend/core crates and conventional React/TypeScript patterns in the desktop app.

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
Repository guidance in `AGENTS.md` is explicit:
- prefer self-documenting code
- do not comment obvious operations
- only comment non-obvious business logic, why, or complex algorithms

In practice, many files follow this: code is fairly descriptive, with comments mainly around edge cases or environment behavior.

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