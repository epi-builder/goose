# Research: Stack

**Project:** goose
**Date:** 2026-04-07
**Mode:** Brownfield repository synthesis

## Summary

goose already uses a pragmatic 2025-era stack for a local-first AI agent product: a shared Rust core for agent runtime and integrations, thin Rust CLI/server delivery layers, and an Electron + React + TypeScript desktop app generated against an OpenAPI contract. For future work, the strongest stack recommendation is not replacement but disciplined continuation of the current architecture, with new non-trivial features landing in `crates/goose/`, server-backed desktop changes flowing through `crates/goose-server` + OpenAPI generation, and UI work remaining in `ui/desktop`.

## Current Standard Stack in Use

### Core runtime
- **Rust 2021 workspace** — primary implementation language for agent runtime, providers, sessions, security, recipes, and orchestration
- **Tokio + async ecosystem** — async runtime and coordination model across core/server features
- **SQLite via sqlx** — local persistence for sessions/history
- **Axum + utoipa/OpenAPI** — local HTTP server and typed API contract generation

### Delivery surfaces
- **CLI:** `clap`, `cliclack`, `rustyline`
- **Desktop:** Electron 41, React 19, TypeScript 5.9, Vite, Vitest, Playwright
- **Generated contract boundary:** `just generate-openapi` updates `ui/desktop/openapi.json` and generated TS client code

### Integration substrate
- **MCP/ACP protocols:** `rmcp`, ACP crates and UI package
- **Provider abstraction:** broad provider catalog implemented behind shared interfaces in `crates/goose/src/providers/`
- **Security and permissions:** first-class runtime inspection and permission systems in Rust, plus Electron CSP/URL protections

## Recommendations

### Keep
- **Shared Rust core as architectural center** — highest leverage for correctness and reuse
- **Thin CLI/server wrappers** — avoids duplicated business logic
- **Desktop via server API contract** — preserves clear boundary and testability
- **SQLite local persistence** — fits local-first product model

### Prefer for new work
- Add domain logic in `crates/goose/`
- Add desktop/backend features through `crates/goose-server/src/routes/` and regenerate OpenAPI
- Keep UI composition in React feature areas and hooks rather than bespoke data-fetch layers
- Extend existing provider / permission / extension abstractions instead of adding parallel execution paths

### Avoid
- Reimplementing product logic in CLI or Electron layers
- Manual edits to generated API artifacts
- Introducing alternate backend patterns that bypass current route/OpenAPI flow
- Large cross-cutting changes without explicit traceability and tests in touched subsystems

## Why this stack fits
- Strong alignment with the product promise: local, extensible, developer-facing, multi-surface
- Good separation between core orchestration and user-facing shells
- Mature test/tooling coverage already exists across Rust and desktop surfaces
- Broad provider and protocol support would be expensive to duplicate elsewhere

## Watchouts
- The stack is powerful but operationally heavy; generated artifacts and feature flags require discipline
- The core crate is broad, so additions should respect modular boundaries where possible
- Electron main-process and server/desktop contract changes need extra care

## Confidence
- **Current stack fit:** High
- **Recommendation to continue current architecture:** High
- **Need for major stack replacement:** Low
