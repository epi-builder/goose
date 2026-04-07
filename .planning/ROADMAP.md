# Roadmap: goose

## Overview

This roadmap keeps the successful GSD brownfield import intact but shifts execution priorities toward concrete product capabilities. The sequence now starts by preserving and operationalizing the imported workflow baseline, then enables slash commands to use skills, adds support for multiple remote or external machine targets, and follows with UI improvements. The canonical baseline inherited by later phases includes `.planning/PROJECT.md`, `.planning/REQUIREMENTS.md`, `.planning/ROADMAP.md`, `.planning/STATE.md`, `.planning/codebase/`, and `.planning/research/`. Future feature phases can be added dynamically as new priorities are validated.

## Phases

**Phase Numbering:**
- Integer phases (1, 2, 3): Planned milestone work
- Decimal phases (2.1, 2.2): Urgent insertions (marked with INSERTED)

Decimal phases appear between their surrounding integers in numeric order.

- [ ] **Phase 1: Imported Workflow Baseline** - Preserve and maintain the successful GSD workflow import as the foundation for feature work
- [ ] **Phase 2: Slash Commands Use Skills** - Enable slash commands to discover and invoke skills in a reliable, contributor-friendly way
- [ ] **Phase 3: Multi-Target Remote Execution** - Support multiple remote or external machine targets without regressing local workflows
- [ ] **Phase 4: UI Improvements** - Improve the highest-value user-facing workflows around commands, skills, and targets
- [ ] **Phase 5: Dynamic Feature Expansion** - Keep roadmap growth structured so future feature phases can be added cleanly

## Phase Details

### Phase 1: Imported Workflow Baseline
**Goal**: Preserve the successful import artifacts and make them the maintained planning baseline for subsequent feature execution.
**Baseline inheritance**: Later phases inherit the canonical baseline from Phase 1 and should document any intentional divergence instead of silently redefining the preserved inputs.
**Depends on**: Nothing (first phase)
**Requirements**: [BASE-01, BASE-02, BASE-03]
**Success Criteria** (what must be TRUE):
  1. Existing `.planning/codebase/` and `.planning/research/` artifacts remain intact and are treated as canonical brownfield inputs.
  2. Core planning docs reflect the feature roadmap without losing already-imported context.
  3. Contributors can tell how the imported workflow baseline connects to upcoming feature phases.
**Plans**: 3 plans

Plans:
- [x] 01-01: Audit imported planning artifacts and document which ones must be preserved
- [x] 01-02: Align project, requirements, roadmap, and state docs to the feature-centric sequence
- [x] 01-03: Verify the preserved import baseline is ready to support downstream phase planning

### Phase 2: Slash Commands Use Skills
**Goal**: Make slash commands able to discover, use, and explain skills as a first-class product capability.
**Depends on**: Phase 1
**Requirements**: [SKILL-01, SKILL-02, SKILL-03, SKILL-04]
**Success Criteria** (what must be TRUE):
  1. Relevant slash commands can discover available skills for the current context.
  2. Commands can incorporate skills during execution through supported repository patterns.
  3. Skill usage is understandable to contributors and does not bypass existing safety or local-first guarantees.
  4. The implementation path spans CLI/server/UI boundaries only where necessary and remains traceable.
**Plans**: 3 plans

Plans:
- [ ] 02-01: Map the current slash-command and skill integration points across the codebase
- [ ] 02-02: Plan the command-to-skill invocation flow, guardrails, and contributor-facing behavior
- [ ] 02-03: Define verification coverage for skill-enabled slash command workflows

### Phase 3: Multi-Target Remote Execution
**Goal**: Support multiple remote or external machine targets while preserving trusted local behavior and operational clarity.
**Depends on**: Phase 2
**Requirements**: [TARGET-01, TARGET-02, TARGET-03, TARGET-04]
**Success Criteria** (what must be TRUE):
  1. Users can configure and select among multiple execution targets.
  2. Local and remote/external execution behavior remains understandable and reliable.
  3. Connection, routing, and failure states are visible enough for safe operation.
  4. Security and auth expectations remain explicit and preserved.
**Plans**: 3 plans

Plans:
- [ ] 03-01: Identify current execution-target assumptions and extension points
- [ ] 03-02: Plan target configuration, selection, and routing behavior across surfaces
- [ ] 03-03: Define verification for connection handling, failure modes, and trust boundaries

### Phase 4: UI Improvements
**Goal**: Improve user-facing workflows for commands, skills, and targets without regressing established desktop/web safety patterns.
**Depends on**: Phase 3
**Requirements**: [UI-01, UI-02, UI-03]
**Success Criteria** (what must be TRUE):
  1. High-value UI flows for command, skill, and target usage are clearer and easier to operate.
  2. UI changes align with established component, hook, and generated-client patterns where relevant.
  3. Safety controls such as CSP, localhost trust, and URL restrictions remain intact.
**Plans**: 3 plans

Plans:
- [ ] 04-01: Identify the highest-friction UI workflows around commands, skills, and targets
- [ ] 04-02: Plan UI changes that improve clarity while preserving architecture and safety guardrails
- [ ] 04-03: Define UI verification expectations for the targeted workflows

### Phase 5: Dynamic Feature Expansion
**Goal**: Keep roadmap evolution feature-centric and flexible so future validated work can be added without destabilizing the plan.
**Depends on**: Phase 4
**Requirements**: [ROAD-01, ROAD-02]
**Success Criteria** (what must be TRUE):
  1. The roadmap clearly distinguishes current priorities from future add-on phases.
  2. Future feature phases can be inserted or appended with low ambiguity.
  3. Planning artifacts continue to preserve traceability as new priorities emerge.
**Plans**: 3 plans

Plans:
- [ ] 05-01: Document how future feature phases should be added and linked to requirements
- [ ] 05-02: Keep planning traceability coherent as roadmap scope grows
- [ ] 05-03: Establish a lightweight pattern for dynamic roadmap expansion after phase execution begins

## Progress

**Execution Order:**
Phases execute in numeric order: 1 → 2 → 3 → 4 → 5

| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. Imported Workflow Baseline | 0/3 | Not started | - |
| 2. Slash Commands Use Skills | 0/3 | Not started | - |
| 3. Multi-Target Remote Execution | 0/3 | Not started | - |
| 4. UI Improvements | 0/3 | Not started | - |
| 5. Dynamic Feature Expansion | 0/3 | Not started | - |
