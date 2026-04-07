# goose

## What This Is

goose is a local, extensible, open-source AI agent for engineering work, delivered as a Rust core with CLI and desktop interfaces. It automates real engineering tasks including coding, tool use, debugging, workflow orchestration, MCP/ACP integrations, local session management, and user-facing command workflows for individual developers and teams.

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
- ✓ GSD import artifacts and brownfield planning context are already present and should be preserved — existing

### Active

- [ ] Keep the GSD workflow import complete, usable, and maintainable as the planning baseline
- [ ] Enable slash commands to discover and use skills as a first-class capability
- [ ] Support multiple remote or external machine targets for execution and orchestration workflows
- [ ] Improve UI clarity and polish across the relevant user surfaces without regressing safety or local-first behavior
- [ ] Keep roadmap structure extensible so future feature phases can be appended dynamically as new priorities emerge

### Out of Scope

- Native mobile apps — current product investment is CLI + desktop + docs, and mobile would expand scope significantly
- Replacing the shared-core architecture with separate per-surface implementations — the codebase is already organized around a reusable Rust core
- Cloud-only hosted execution as the primary product model — the repo and product positioning are explicitly local/on-machine first
- Re-centering the roadmap on meta infrastructure work before user-visible capabilities — the immediate intent is feature delivery grounded in existing artifacts

## Context

This is a mature brownfield monorepo centered on `crates/goose/`, with CLI (`crates/goose-cli`), local server (`crates/goose-server`), and Electron desktop UI (`ui/desktop`) layered around a common Rust runtime. The repository also contains documentation, recipes, workflow infrastructure, and GSD planning/import artifacts that already succeeded and should be maintained rather than replaced.

Planning should therefore assume brownfield evolution around concrete product capabilities. The roadmap should start from the already-imported GSD workflow baseline, then prioritize enabling slash commands to use skills, supporting multiple remote/external machine targets, and improving the UI. Additional phases should remain easy to add as new user-facing priorities emerge.

## Constraints

- **Tech stack**: Rust workspace with Electron/React/TypeScript desktop app — existing architecture and contributor workflows depend on it
- **Architecture**: Shared-core / thin-interface model — business logic belongs in `crates/goose/` and surfaces adapt it
- **Compatibility**: Cross-platform CLI and desktop support — project ships packaging/build paths for macOS, Linux, and Windows
- **Security**: Permission checks, egress inspection, CSP, and localhost trust handling are first-class — regressions here have outsized product risk
- **Contract boundary**: Server routes and desktop client must stay synchronized via OpenAPI generation where applicable
- **Preservation**: Existing `.planning/codebase/` and `.planning/research/` import artifacts are canonical brownfield inputs and should be retained
- **Roadmap flexibility**: Future phases may be inserted or appended as new feature work is clarified

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Initialize as a brownfield project using the existing codebase map | The repository already contains substantial code and `.planning/codebase/` artifacts | ✓ Good |
| Run new-project in automatic mode with YOLO-style approvals | User requested non-interactive end-to-end initialization | ✓ Good |
| Track planning docs in git | Open-source repository benefits from visible planning context and workflow continuity | ✓ Good |
| Preserve successful GSD import artifacts as baseline inputs | Existing import/codebase/research docs already capture useful brownfield context | ✓ Good |
| Refocus the roadmap on feature delivery rather than meta infrastructure | The user's intended execution order is feature-centric and capability-driven | ✓ Good |
| Prioritize slash-command skill usage ahead of multi-target execution and UI polish | This is the clearest next user-facing capability after maintaining import fidelity | ✓ Good |

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
*Last updated: 2026-04-07 after roadmap refocus*
