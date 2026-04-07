# Requirements: goose

**Defined:** 2026-04-07
**Core Value:** Developers can reliably run a powerful on-machine AI agent that automates real engineering work while remaining extensible, local-first, and open.

## v1 Requirements

### Imported Workflow Baseline

- [ ] **BASE-01**: Existing GSD import artifacts remain present, understandable, and usable as the planning baseline
- [ ] **BASE-02**: Planning updates preserve successful brownfield import context instead of replacing it with disconnected meta documentation
- [ ] **BASE-03**: Contributors can identify which imported workflow artifacts are canonical inputs for future planning and execution

### Slash Commands + Skills

- [ ] **SKILL-01**: Slash commands can discover available skills relevant to the invoked command or task
- [ ] **SKILL-02**: Slash commands can invoke or incorporate skills during execution using project-supported patterns
- [ ] **SKILL-03**: Skill usage through slash commands is understandable to contributors and traceable in planning and implementation artifacts
- [ ] **SKILL-04**: Skill-enabled command flows preserve existing safety, permissions, and local-first execution guarantees

### Multi-Target / Remote Execution

- [ ] **TARGET-01**: Users can configure and select from multiple remote or external machine targets
- [ ] **TARGET-02**: Execution workflows can route work to the intended target without breaking existing local behavior
- [ ] **TARGET-03**: Target selection, connection state, and failure modes are visible enough for reliable operation
- [ ] **TARGET-04**: Multi-target support preserves security boundaries, auth expectations, and operator trust

### UI Improvements

- [ ] **UI-01**: UI changes improve clarity of the most important command, skill, and target workflows
- [ ] **UI-02**: UI improvements align with established React/Electron patterns and existing generated-client or backend boundaries where relevant
- [ ] **UI-03**: UI changes preserve existing safety controls such as CSP, localhost trust, and URL/protocol restrictions

### Roadmap Extensibility

- [ ] **ROAD-01**: The roadmap can accept additional future feature phases without disrupting completed or planned work
- [ ] **ROAD-02**: Planning artifacts clearly distinguish current prioritized phases from future dynamic additions

## v2 Requirements

### Future Expansion

- **FUTURE-01**: Additional feature phases can be appended for newly validated product priorities
- **FUTURE-02**: Broader architectural cleanup can be planned after the feature roadmap produces implementation feedback

## Out of Scope

| Feature | Reason |
|---------|--------|
| Native iOS/Android apps | Not aligned with current brownfield repo focus or validated product surface |
| Replacing Rust core with separate per-surface logic | Conflicts with established architecture and would increase duplication risk |
| Cloud-first product repositioning | Current repository and product positioning are explicitly local/on-machine first |
| Large meta-roadmap/infrastructure-first reshaping before feature delivery | Conflicts with the requested feature-centric execution order |

## Traceability

| Requirement | Phase | Status |
|-------------|-------|--------|
| BASE-01 | Phase 1 | Pending |
| BASE-02 | Phase 1 | Pending |
| BASE-03 | Phase 1 | Pending |
| SKILL-01 | Phase 2 | Pending |
| SKILL-02 | Phase 2 | Pending |
| SKILL-03 | Phase 2 | Pending |
| SKILL-04 | Phase 2 | Pending |
| TARGET-01 | Phase 3 | Pending |
| TARGET-02 | Phase 3 | Pending |
| TARGET-03 | Phase 3 | Pending |
| TARGET-04 | Phase 3 | Pending |
| UI-01 | Phase 4 | Pending |
| UI-02 | Phase 4 | Pending |
| UI-03 | Phase 4 | Pending |
| ROAD-01 | Phase 5 | Pending |
| ROAD-02 | Phase 5 | Pending |

**Coverage:**
- v1 requirements: 16 total
- Mapped to phases: 16
- Unmapped: 0 ✓

---
*Requirements defined: 2026-04-07*
*Last updated: 2026-04-07 after roadmap refocus*
