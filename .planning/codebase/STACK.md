# STACK

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
  - `crates/goose` — core agent logic, providers, sessions, security, recipes, scheduling.
  - `crates/goose-cli` — CLI binary `goose` in `crates/goose-cli/src/main.rs`.
  - `crates/goose-server` — backend/server binary `goosed` in `crates/goose-server/src/main.rs`.
  - `crates/goose-mcp` — MCP servers/extensions.
  - `crates/goose-acp`, `crates/goose-acp-macros`, `crates/goose-sdk`, `crates/goose-test`, `crates/goose-test-support`.

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
  - `code-mode`
  - `local-inference`
  - `aws-providers`
  - `telemetry`
  - `otel`
  - `rustls-tls`
- TLS is mutually exclusive in `crates/goose/src/lib.rs`: either `rustls-tls` or `native-tls` must be enabled.
- Optional GPU/local inference features include `cuda`, `metal`, `llama-cpp-2`, Candle, tokenizers, and Whisper-related dependencies in `crates/goose/Cargo.toml`.

## Desktop stack
- Electron desktop app entry: `ui/desktop/src/main.ts`.
- Renderer stack: React 19 + TypeScript + React Router in `ui/desktop/src/App.tsx`.
- Build system: Vite + Electron Forge in `ui/desktop/vite*.mts` and `ui/desktop/forge.config.ts`.
- Styling/UI:
  - Tailwind CSS in `ui/desktop/package.json`
  - Radix UI components
  - `react-toastify`, `framer-motion`, `lucide-react`
- API client generation:
  - OpenAPI generated from server via `just generate-openapi`
  - TS client generated into `ui/desktop/src/api/`
  - OpenAPI input file at `ui/desktop/openapi.json`

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
  - `cargo build`
  - `cargo fmt --all`
  - `cargo clippy --all-targets -- -D warnings`
  - `just generate-openapi`
  - `just run-ui`
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