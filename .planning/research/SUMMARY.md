# Research Summary

**Project:** goose
**Date:** 2026-04-07

## Key Findings

**Stack:** Continue the current shared Rust core + thin CLI/server + Electron/React desktop architecture; future work should extend, not replace, this stack.

**Table Stakes:** Multi-provider agent execution, tool use, local sessions, extensibility, CLI + desktop surfaces, permissions/security, and strong OSS contributor workflows are all baseline expectations and already substantially present.

**Watch Out For:** Core-crate complexity, provider matrix drift, server/desktop OpenAPI contract sync, and security regressions in agentic execution paths are the main planning risks.

## Planning Guidance
- Treat existing product capabilities as validated brownfield scope.
- Prioritize roadmap phases that improve maintainability, reliability, and safe extensibility.
- Preserve the architectural rule that business logic lives in `crates/goose/` and desktop-visible backend changes flow through server routes and generated clients.
- Make traceability explicit for cross-surface work so contributors can reason about core, server, UI, docs, and release impacts.

## Recommended Phase Shape
1. Foundation and architectural guardrails
2. Core/runtime reliability improvements
3. Server/API and contract integrity
4. Desktop UX / parity / safety refinements
5. Contributor workflow, docs, and release confidence
