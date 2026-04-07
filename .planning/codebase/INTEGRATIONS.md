# INTEGRATIONS

## Overview
This repository integrates with LLM providers, MCP/ACP servers, desktop update/distribution services, local SQLite storage, GitHub Actions, and a handful of supporting external services.

## LLM and model providers
Provider implementations live primarily in `crates/goose/src/providers/`.

### Direct/hosted providers
- OpenAI: `crates/goose/src/providers/openai.rs`
- Anthropic: `crates/goose/src/providers/anthropic.rs`
- OpenRouter: `crates/goose/src/providers/openrouter.rs`
- Google / Vertex AI: `crates/goose/src/providers/google.rs`, `crates/goose/src/providers/gcpvertexai.rs`
- Azure / Azure auth: `crates/goose/src/providers/azure.rs`, `crates/goose/src/providers/azureauth.rs`
- AWS Bedrock / SageMaker: `crates/goose/src/providers/bedrock.rs`, `crates/goose/src/providers/sagemaker_tgi.rs`
- Databricks: `crates/goose/src/providers/databricks.rs`
- Ollama: `crates/goose/src/providers/ollama.rs`
- LiteLLM: `crates/goose/src/providers/litellm.rs`
- Venice, xAI, NanoGPT, Tetrate, GitHub Copilot, ChatGPT Codex, Claude Code, Gemini CLI, Codex ACP, Cursor Agent, etc. in sibling files under `crates/goose/src/providers/`.

### Declarative provider catalog
- JSON-defined providers in `crates/goose/src/providers/declarative/`.
- Canonical model registry generation in `crates/goose/src/providers/canonical/`.

### Auth/OAuth integrations
- OAuth persistence/callback logic in `crates/goose/src/oauth/`.
- Provider-specific auth helpers in `crates/goose/src/providers/gcpauth.rs`, `crates/goose/src/providers/gemini_oauth.rs`, `crates/goose/src/providers/azureauth.rs`.
- Signup integrations for OpenRouter, NanoGPT, and Tetrate under `crates/goose/src/config/signup_*`.

## MCP / ACP integrations
- MCP client/server protocol library: `rmcp` used across `crates/goose`, `crates/goose-mcp`, and `crates/goose-server`.
- ACP support in `crates/goose-acp/` and `ui/acp/`.
- MCP server binaries exposed by `goosed mcp` in `crates/goose-server/src/main.rs`:
  - `AutoVisualiserRouter`
  - `ComputerControllerServer`
  - `MemoryServer`
  - `TutorialServer`
- Extension and MCP orchestration code in `crates/goose/src/agents/extension_manager.rs`, `crates/goose/src/agents/mcp_client.rs`, and `crates/goose/src/agents/platform_extensions/`.

## Desktop ↔ server integration
- Desktop app launches and checks local `goosed` server in `ui/desktop/src/goosed.ts` and `ui/desktop/src/main.ts`.
- Server routes registered in `crates/goose-server/src/routes/mod.rs`.
- OpenAPI schema generation via `crates/goose-server/src/bin/generate_schema.rs` and `just generate-openapi`.
- Generated client consumed in `ui/desktop/src/api/`.
- This is the main product integration seam for adding backend-powered UI features.

## Local storage and state
### SQLite
- Session persistence uses SQLite through `sqlx` in `crates/goose/src/session/session_manager.rs`.
- Chat history search also hits SQLite in `crates/goose/src/session/chat_history_search.rs`.
- WAL mode and schema migrations are managed in session manager code.

### File-based config/state
- Goose config and extension settings under `crates/goose/src/config/`.
- Desktop settings JSON stored from `ui/desktop/src/main.ts`.
- Recipes stored/read through `crates/goose/src/recipe/` and desktop recipe management in `ui/desktop/src/recipe/`.

## Security, telemetry, and analytics integrations
- Telemetry feature hooks in `crates/goose/src/posthog.rs`.
- OpenTelemetry exporters and OTLP integration in `crates/goose/src/otel/otlp.rs` and `crates/goose/src/tracing/`.
- Security inspection modules in `crates/goose/src/security/` including adversary and egress inspection.
- Desktop CSP and URL hardening in `ui/desktop/src/utils/csp.ts`, `ui/desktop/src/utils/urlSecurity.ts`, and certificate pinning logic in `ui/desktop/src/main.ts`.

## Desktop distribution and update services
- Auto-updates wired through `electron-updater` in `ui/desktop/src/utils/autoUpdater.ts`.
- Packaging via Electron Forge in `ui/desktop/forge.config.ts`.
- GitHub Actions bundle workflows: `.github/workflows/bundle-desktop.yml`, `.github/workflows/bundle-desktop-intel.yml`, `.github/workflows/bundle-desktop-linux.yml`, `.github/workflows/bundle-desktop-windows.yml`.

## External web/service integrations outside core product
### Discord bot service
- `services/ask-ai-bot/` integrates with:
  - Discord via `discord.js`
  - Anthropic via `@ai-sdk/anthropic`
- Main entry: `services/ask-ai-bot/index.ts`

### OIDC proxy
- `oidc-proxy/` is a separate Node service used for auth proxying/testing workflows.

## CI/CD and repository integrations
- GitHub Actions is the primary automation system in `.github/workflows/`.
- Key repository/service integrations include:
  - GitHub releases and packaging workflows
  - cargo-deny via `.github/workflows/cargo-deny.yml`
  - OSSF Scorecard via `.github/workflows/scorecard.yml`
  - docs deploy and preview workflows
  - PR review and automation workflows using Goose itself

## Networking patterns
- Localhost HTTPS trust/pinning between desktop and `goosed` in `ui/desktop/src/main.ts`.
- Proxy environment support in desktop main process via `HTTPS_PROXY`, `HTTP_PROXY`, `NO_PROXY` handling in `ui/desktop/src/main.ts`.
- HTTP APIs are primarily implemented with Axum in server routes and consumed via generated TypeScript client code.

## Integration hotspots to know
- Add a new backend capability:
  - implement in `crates/goose/` if core logic
  - expose route in `crates/goose-server/src/routes/`
  - run `just generate-openapi`
  - consume via `ui/desktop/src/api/` and renderer components
- Add provider/auth integration:
  - provider module under `crates/goose/src/providers/`
  - config wiring under `crates/goose/src/config/`
- Add MCP integration:
  - extension/server work in `crates/goose-mcp/` or `crates/goose/src/agents/platform_extensions/`.