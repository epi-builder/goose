# Requirements: goose

**Defined:** 2026-04-07
**Core Value:** Developers can reliably run a powerful on-machine AI agent that automates real engineering work while remaining extensible, local-first, and open.

## v1 Requirements

### Architecture

- [ ] **ARCH-01**: Contributors can identify the correct implementation boundary for a feature change among core, CLI, server, and desktop surfaces
- [ ] **ARCH-02**: Non-trivial product behavior is implemented in `crates/goose/` and reused by surface layers rather than duplicated
- [ ] **ARCH-03**: Cross-surface changes document and preserve the shared-core / thin-interface architecture

### Reliability

- [ ] **RELY-01**: Core agent workflows behave consistently across CLI and desktop entry points for the same capability
- [ ] **RELY-02**: Session, persistence, and local execution paths remain reliable as features evolve
- [ ] **RELY-03**: Security- and permission-sensitive execution paths remain active for new or modified capabilities
- [ ] **RELY-04**: Provider and auth integrations preserve intended behavior across the supported compatibility surface

### API Contract

- [ ] **API-01**: Desktop-facing backend changes are exposed through `crates/goose-server/src/routes/` rather than ad hoc side channels
- [ ] **API-02**: OpenAPI artifacts and generated TypeScript clients stay synchronized with backend contract changes
- [ ] **API-03**: Contract-sensitive changes are verifiable by contributors before merge

### Desktop Experience

- [ ] **UX-01**: Desktop features that rely on backend capabilities use the generated API client and existing React/Electron patterns
- [ ] **UX-02**: Desktop behavior preserves existing safety controls such as localhost trust, CSP, and URL/protocol restrictions
- [ ] **UX-03**: UI-facing changes remain consistent with established component, hook, and testing patterns in `ui/desktop`

### Contributor Workflow

- [ ] **FLOW-01**: Contributors can discover the correct build, test, lint, and generation commands for the areas they change
- [ ] **FLOW-02**: Planning and execution traceability clearly connect requirements, phases, and repository boundaries
- [ ] **FLOW-03**: Generated artifacts, docs, and release-related workflows remain understandable and maintainable for OSS contributors

## v2 Requirements

### Platform Expansion

- **PLAT-01**: Users can access goose through additional native mobile clients
- **PLAT-02**: Users can rely on a hosted cloud-first execution model as the primary product surface

### Broader Refactors

- **REF-01**: The core crate is split into smaller independently evolvable domain crates
- **REF-02**: Provider support is reorganized into separately versioned plugin packages

## Out of Scope

| Feature | Reason |
|---------|--------|
| Native iOS/Android apps | Not aligned with current brownfield repo focus or validated product surface |
| Replacing Rust core with separate per-surface logic | Conflicts with established architecture and would increase duplication risk |
| Cloud-first product repositioning | Current repository and product positioning are explicitly local/on-machine first |
| Large-scale provider/plugin replatforming in initial roadmap | Too broad for initialization roadmap; better handled after focused brownfield execution learns more |

## Traceability

| Requirement | Phase | Status |
|-------------|-------|--------|
| ARCH-01 | Phase 1 | Pending |
| ARCH-02 | Phase 1 | Pending |
| ARCH-03 | Phase 1 | Pending |
| RELY-01 | Phase 2 | Pending |
| RELY-02 | Phase 2 | Pending |
| RELY-03 | Phase 2 | Pending |
| RELY-04 | Phase 2 | Pending |
| API-01 | Phase 3 | Pending |
| API-02 | Phase 3 | Pending |
| API-03 | Phase 3 | Pending |
| UX-01 | Phase 4 | Pending |
| UX-02 | Phase 4 | Pending |
| UX-03 | Phase 4 | Pending |
| FLOW-01 | Phase 5 | Pending |
| FLOW-02 | Phase 5 | Pending |
| FLOW-03 | Phase 5 | Pending |

**Coverage:**
- v1 requirements: 16 total
- Mapped to phases: 16
- Unmapped: 0 ✓

---
*Requirements defined: 2026-04-07*
*Last updated: 2026-04-07 after initial definition*