# Roadmap: goose

## Overview

This roadmap initializes goose as a managed brownfield project for GSD execution. It starts by locking in architectural guardrails for contributors, then focuses on runtime reliability, server/desktop contract integrity, desktop safety and UX consistency, and finally contributor workflow clarity so future execution can proceed phase-by-phase with low ambiguity.

## Phases

**Phase Numbering:**
- Integer phases (1, 2, 3): Planned milestone work
- Decimal phases (2.1, 2.2): Urgent insertions (marked with INSERTED)

Decimal phases appear between their surrounding integers in numeric order.

- [ ] **Phase 1: Architectural Guardrails** - Align contributors around implementation boundaries and preserved core patterns
- [ ] **Phase 2: Runtime Reliability** - Protect core execution, persistence, provider, and security-sensitive behavior
- [ ] **Phase 3: Contract Integrity** - Keep server routes, generated OpenAPI, and desktop API usage in sync
- [ ] **Phase 4: Desktop Safety and UX Consistency** - Improve desktop-facing behavior within established Electron/React safety patterns
- [ ] **Phase 5: Contributor Workflow Confidence** - Strengthen docs, traceability, and OSS execution ergonomics

## Phase Details

### Phase 1: Architectural Guardrails
**Goal**: Make the repository's intended implementation boundaries explicit so contributors extend goose through the shared core and established surface adapters.
**Depends on**: Nothing (first phase)
**Requirements**: [ARCH-01, ARCH-02, ARCH-03]
**Success Criteria** (what must be TRUE):
  1. Contributors can identify where core logic, CLI logic, server routes, and desktop UI changes belong.
  2. Planned feature work routes non-trivial behavior into `crates/goose/` rather than duplicating it in surfaces.
  3. Architectural expectations are reflected in planning artifacts and ready for phase execution.
**Plans**: 3 plans

Plans:
- [ ] 01-01: Audit and codify implementation boundaries across core, CLI, server, and desktop
- [ ] 01-02: Capture architectural guardrails in planning and instruction artifacts
- [ ] 01-03: Validate that future brownfield work can be routed through the shared-core model

### Phase 2: Runtime Reliability
**Goal**: Preserve reliable core behavior across agent execution, persistence, provider compatibility, and security/permission-sensitive paths.
**Depends on**: Phase 1
**Requirements**: [RELY-01, RELY-02, RELY-03, RELY-04]
**Success Criteria** (what must be TRUE):
  1. Core workflows remain coherent across CLI and desktop entry points.
  2. Session and local persistence flows remain dependable for ongoing work.
  3. Security and permission checks remain active for modified execution paths.
  4. Provider-facing changes preserve intended compatibility expectations.
**Plans**: 3 plans

Plans:
- [ ] 02-01: Identify and prioritize core reliability hotspots in agent, session, and provider layers
- [ ] 02-02: Plan safety-preserving changes for permission and security-sensitive execution paths
- [ ] 02-03: Define verification coverage for runtime and provider behavior

### Phase 3: Contract Integrity
**Goal**: Keep backend routes, generated contracts, and desktop consumption aligned so cross-surface features remain dependable.
**Depends on**: Phase 2
**Requirements**: [API-01, API-02, API-03]
**Success Criteria** (what must be TRUE):
  1. Desktop-backed backend capabilities are exposed through feature-scoped server routes.
  2. Contract changes regenerate OpenAPI artifacts and generated TypeScript client code consistently.
  3. Contributors have a clear verification path for contract-sensitive changes before merge.
**Plans**: 3 plans

Plans:
- [ ] 03-01: Map the backend-to-desktop contract flow and common drift points
- [ ] 03-02: Plan contract-safe changes and generation steps for server-backed features
- [ ] 03-03: Define verification and review checks for generated artifacts and consumers

### Phase 4: Desktop Safety and UX Consistency
**Goal**: Improve desktop-facing behavior while preserving existing Electron safety controls and established React implementation patterns.
**Depends on**: Phase 3
**Requirements**: [UX-01, UX-02, UX-03]
**Success Criteria** (what must be TRUE):
  1. Desktop changes use generated APIs and established component/hook patterns.
  2. Safety controls such as CSP, localhost trust, and URL restrictions remain intact.
  3. UI-facing work can be tested and reviewed using existing desktop workflows.
**Plans**: 3 plans

Plans:
- [ ] 04-01: Identify desktop UX and safety-sensitive implementation seams
- [ ] 04-02: Plan UI changes that preserve generated-client and main-process guardrails
- [ ] 04-03: Define desktop-specific verification expectations for future execution

### Phase 5: Contributor Workflow Confidence
**Goal**: Make the brownfield repo easier to navigate and safer to evolve through clearer workflows, traceability, and documentation.
**Depends on**: Phase 4
**Requirements**: [FLOW-01, FLOW-02, FLOW-03]
**Success Criteria** (what must be TRUE):
  1. Contributors can find the right build, lint, test, and generation steps for their changes.
  2. Planning artifacts clearly map requirements to execution phases and repository boundaries.
  3. Generated artifacts, docs, and release-related workflows are understandable enough to support confident OSS contribution.
**Plans**: 3 plans

Plans:
- [ ] 05-01: Improve contributor-facing navigation across code, docs, and generated artifacts
- [ ] 05-02: Strengthen planning traceability for brownfield execution work
- [ ] 05-03: Capture workflow guidance for docs, release, and generated-file maintenance

## Progress

**Execution Order:**
Phases execute in numeric order: 1 → 2 → 3 → 4 → 5

| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. Architectural Guardrails | 0/3 | Not started | - |
| 2. Runtime Reliability | 0/3 | Not started | - |
| 3. Contract Integrity | 0/3 | Not started | - |
| 4. Desktop Safety and UX Consistency | 0/3 | Not started | - |
| 5. Contributor Workflow Confidence | 0/3 | Not started | - |