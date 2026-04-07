# Research: Architecture

**Project:** goose
**Date:** 2026-04-07
**Mode:** Brownfield repository synthesis

## Summary

goose follows a strong brownfield architecture pattern for agent products: a shared Rust domain core, interface adapters for CLI and server, and a desktop client that talks to a local server through a generated API contract. Future roadmap work should preserve this separation and sequence changes from core behavior outward to interfaces.

## Major Components

1. **Core runtime (`crates/goose/`)**
   - Agent execution
   - Providers
   - Sessions and persistence
   - Permissions and security
   - Recipes, scheduling, context management

2. **CLI surface (`crates/goose-cli/`)**
   - Terminal UX and command orchestration
   - Thin wrapper over core capabilities

3. **Server surface (`crates/goose-server/`)**
   - HTTP routes / OpenAPI boundary for desktop-backed functionality
   - Main integration seam for desktop-visible backend features

4. **Desktop app (`ui/desktop/`)**
   - Electron main process + React renderer
   - Generated API client consumption

5. **Auxiliary product/supporting areas**
   - Docs site
   - MCP/ACP packages
   - Services, evals, examples, release automation

## Data Flow
- CLI path: terminal command → CLI adapter → core runtime → local persistence / providers / tools → terminal output
- Desktop path: React UI → generated API client → local Axum server → core runtime → streamed/stateful response back to UI
- Planning implication: implement reusable behavior at the core, then expose it outward by contract where needed

## Suggested Build Order for Future Changes
1. Stabilize or extend behavior in `crates/goose/`
2. Expose backend capability in CLI and/or server route layer
3. Regenerate OpenAPI when server contracts change
4. Wire UI usage in Electron/React
5. Add/update tests closest to each touched boundary

## Critical Boundaries
- **Core vs surface logic** — business behavior belongs in core
- **Server vs desktop contract** — route changes require regeneration
- **Generated vs handwritten UI code** — generated API client remains authoritative for contract usage
- **Security/permission layers** — must remain in the execution path for any risky capability

## Architecture Risks to Address in Planning
- Core crate breadth and concentration of complexity
- Electron main-process responsibilities
- Provider matrix maintenance burden
- Contract drift between server and desktop
