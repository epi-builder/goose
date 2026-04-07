# goose

## What This Is

goose is a local, extensible, open-source AI agent for engineering work, delivered as a Rust core with CLI and desktop interfaces. It automates end-to-end developer tasks including coding, tool use, debugging, workflow orchestration, MCP/ACP integrations, and local session management for individual developers and teams adopting OSS agent tooling.

## Core Value

Developers can reliably run a powerful on-machine AI agent that automates real engineering work while remaining extensible, local-first, and open.

## Requirements

### Validated

- ✓ Users can run goose through a CLI for interactive agent workflows — existing
- ✓ Users can run goose through a desktop application backed by a local server — existing
- ✓ Users can connect goose to multiple model providers and auth flows — existing
- ✓ Users can use extensions, MCP/ACP integrations, and platform tools — existing
- ✓ Users can persist local sessions, history, and configuration — existing
- ✓ Contributors can build, test, package, and release the project across platforms — existing
- ✓ The project ships public documentation, recipes, governance, and contributor workflows — existing

### Active

- [ ] Improve maintainability and contributor clarity in the shared Rust core and multi-surface architecture
- [ ] Strengthen reliability across CLI, server, desktop, generated OpenAPI contracts, and provider integrations
- [ ] Preserve security, permissions, and local-first guarantees as the platform evolves
- [ ] Keep OSS contributor workflows, documentation, and release automation aligned with the product surface

### Out of Scope

- Native mobile apps — current product investment is CLI + desktop + docs, and mobile would expand scope significantly
- Replacing the shared-core architecture with separate per-surface implementations — the codebase is already organized around a reusable Rust core
- Cloud-only hosted execution as the primary product model — the repo and product positioning are explicitly local/on-machine first

## Context

This is a mature brownfield monorepo centered on `crates/goose/`, with CLI (`crates/goose-cli`), local server (`crates/goose-server`), and Electron desktop UI (`ui/desktop`) all layered around a common Rust runtime. The repository also contains documentation, recipes, supporting services, evaluation assets, packaging infrastructure, and release automation. The existing codebase map highlights major strengths—shared core, protocol-driven desktop/server boundary, strong provider support, and security-first design—alongside pressure points such as core-crate breadth, provider matrix complexity, generated contract sync, and Electron main-process complexity.

The project is open source under Apache-2.0 and already has established CI, governance, contribution docs, and release workflows. Planning should therefore assume ongoing brownfield evolution rather than greenfield invention: preserve existing interfaces, respect checked-in generated artifacts, and prioritize contributor-safe changes with clear traceability.

## Constraints

- **Tech stack**: Rust workspace with Electron/React/TypeScript desktop app — existing architecture and contributor workflows depend on it
- **Architecture**: Shared-core / thin-interface model — business logic belongs in `crates/goose/` and surfaces adapt it
- **Compatibility**: Cross-platform CLI and desktop support — project ships packaging/build paths for macOS, Linux, and Windows
- **Security**: Permission checks, egress inspection, CSP, and localhost trust handling are first-class — regressions here have outsized product risk
- **Contract boundary**: Server routes and desktop client must stay synchronized via OpenAPI generation — stale generated artifacts can break product behavior
- **Open source operations**: Planning docs should be git-tracked and comprehensible to outside contributors — this repo relies on transparent collaboration

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Initialize as a brownfield project using the existing codebase map | The repository already contains substantial code and `.planning/codebase/` artifacts | ✓ Good |
| Run new-project in automatic mode with YOLO-style approvals | User requested non-interactive end-to-end initialization | ✓ Good |
| Use standard granularity with parallel planning enabled | Large OSS brownfield monorepo benefits from moderate decomposition without over-fragmentation | — Pending |
| Include research artifacts even though the repo is brownfield | Research is required by auto mode and helps future planning align with current architecture | — Pending |
| Track planning docs in git | Open-source repository benefits from visible planning context and workflow continuity | ✓ Good |

## Evolution

This document evolves at phase transitions and milestone boundaries.

**After each phase transition** (via `/gsd-transition`):
1. Requirements invalidated? → Move to Out of Scope with reason
2. Requirements validated? → Move to Validated with phase reference
3. New requirements emerged? → Add to Active
4. Decisions to log? → Add to Key Decisions
5. "What This Is" still accurate? → Update if drifted

**After each milestone** (via `/gsd-complete-milestone`):
1. Full review of all sections
2. Core Value check — still the right priority?
3. Audit Out of Scope — reasons still valid?
4. Update Context with current state

---
*Last updated: 2026-04-07 after initialization*