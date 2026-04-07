# Phase 1: Imported Workflow Baseline - Context

**Gathered:** 2026-04-08
**Status:** Ready for planning

<domain>
## Phase Boundary

Preserve the successful imported GSD planning baseline and make it the maintained foundation for subsequent feature phases. This phase establishes which existing planning and brownfield analysis artifacts are canonical inputs, how they may be updated without losing provenance, and how downstream phases must reference that baseline. It does not add new product capabilities or replace the imported baseline with a new planning system.

</domain>

<decisions>
## Implementation Decisions

### Canonical baseline scope
- **D-01:** The canonical baseline input set for this phase consists of `PROJECT.md`, `REQUIREMENTS.md`, `ROADMAP.md`, `STATE.md`, `.planning/codebase/`, and `.planning/research/`.
- **D-02:** `.planning/codebase/` and `.planning/research/` remain the canonical imported brownfield artifacts called out by roadmap and project constraints.
- **D-03:** Quick artifacts such as `.planning/quick/` are useful historical references but are not part of the canonical baseline input set unless a later phase explicitly promotes a document.

### Preservation and update rules
- **D-04:** Preserve imported baseline artifacts whenever possible; prefer additive clarification in new phase documents rather than rewriting imported analysis artifacts.
- **D-05:** Corrections to canonical artifacts are allowed only for clear factual errors, stale references, or traceability repairs that improve baseline usability.
- **D-06:** Any direct change to a canonical baseline artifact should make the reason for the change easy to recover from adjacent planning history, commit history, or the updating phase artifact.

### Downstream planning contract
- **D-07:** Subsequent phases should treat the canonical baseline as the default planning input, not merely optional background reading.
- **D-08:** If a downstream phase intentionally diverges from the baseline interpretation, it should record the reason for divergence in that phase’s planning/context artifacts rather than silently redefining the foundation.
- **D-09:** Phase 1 should leave contributors with an explicit understanding of how imported brownfield context connects to feature phases 2–5.

### Contributor-facing explanation
- **D-10:** The connection between the imported baseline and future feature work should be documented directly in Phase 1 artifacts before creating any broader standalone contributor guide.
- **D-11:** A standalone guide may be added later only if repeated cross-phase usage shows the Phase 1 explanation is insufficient.

### Verification expectations
- **D-12:** Phase 1 completion should verify three things: the canonical baseline inventory is explicit, preservation/update rules are explicit, and downstream planning expectations are explicit.
- **D-13:** Verification for this phase is primarily documentation traceability and planning readiness, not code-path behavior.

### Claude's Discretion
- Exact wording and format for the inventory/audit artifact(s)
- Whether baseline readiness is proven through one consolidated verification artifact or multiple phase-local documents
- How much contributor guidance belongs in plan files versus verification output, as long as the Phase 1 contract remains explicit

</decisions>

<specifics>
## Specific Ideas

- Treat the imported baseline as a preserved starting point rather than something to be replaced by fresh meta-documentation.
- Keep roadmap execution feature-centric after this baseline phase: imported workflow baseline → slash-command skills → multi-target execution → UI improvements → dynamic feature expansion.
- Favor a planning model where future phases can diverge when necessary, but only with explicit rationale.

</specifics>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Phase 1 scope and success criteria
- `.planning/ROADMAP.md` — Defines Phase 1 goal, plans, and success criteria for preserving imported workflow artifacts as the maintained baseline.
- `.planning/REQUIREMENTS.md` — Defines `BASE-01`, `BASE-02`, and `BASE-03`, which govern preservation, continuity, and canonical-input clarity.
- `.planning/STATE.md` — Confirms Phase 1 is the current focus and that the planning artifacts were already refocused onto the feature-centric sequence.
- `.planning/PROJECT.md` — Captures project-level preservation constraints, active priorities, and the decision to keep imported artifacts as baseline inputs.

### Canonical imported brownfield artifacts
- `.planning/codebase/CONVENTIONS.md` — Repository conventions that future phases should inherit rather than restate.
- `.planning/codebase/STRUCTURE.md` — Repository structure and cross-surface change paths that anchor later feature planning.
- `.planning/codebase/STACK.md` — Core stack and contract boundaries that should remain consistent in downstream plans.
- `.planning/codebase/ARCHITECTURE.md` — Architectural shape of the current system and core execution flows.
- `.planning/codebase/CONCERNS.md` — Existing codebase risks and complexity areas relevant to planning scope.
- `.planning/codebase/INTEGRATIONS.md` — External/system integration surfaces that future phases may touch.
- `.planning/codebase/TESTING.md` — Existing verification expectations to preserve in later plans.
- `.planning/research/SUMMARY.md` — Imported brownfield synthesis and planning guidance used to shape the current roadmap.
- `.planning/research/FEATURES.md` — Table-stakes and anti-feature framing that informs later feature phases.
- `.planning/research/ARCHITECTURE.md` — Brownfield architecture interpretation complementary to the codebase maps.
- `.planning/research/STACK.md` — Supporting stack synthesis for planning continuity.
- `.planning/research/PITFALLS.md` — Imported planning risks that later phases should continue to respect.

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- `.planning/codebase/` documents: Already provide reusable summaries for architecture, conventions, integrations, structure, stack, concerns, and testing.
- `.planning/research/` documents: Already provide brownfield synthesis that can be referenced by later researchers and planners instead of re-deriving baseline context.
- Core planning docs (`PROJECT.md`, `REQUIREMENTS.md`, `ROADMAP.md`, `STATE.md`): Already encode the feature-centric sequence and should be maintained as the top-level operating contract.

### Established Patterns
- The roadmap explicitly preserves imported `.planning/codebase/` and `.planning/research/` artifacts as canonical brownfield inputs.
- Project constraints require preserving the brownfield/shared-core architecture and avoiding disconnected replacement documentation.
- Planning is expected to remain traceable and phase-driven, with requirements mapped to phases and progress reflected in `STATE.md`.

### Integration Points
- Phase 1 plans should connect imported analysis artifacts to future planning workflows, not to product runtime code.
- Future phase planning should begin from these canonical planning inputs before moving into crate/server/UI-specific design work.
- Any later contributor guidance about planning lineage should be anchored back to the Phase 1 artifacts created in this directory.

</code_context>

<deferred>
## Deferred Ideas

- If contributors repeatedly struggle to understand the baseline, create a dedicated contributor guide for planning lineage in a later phase or quick task.
- Any new user-facing product capabilities remain out of scope for Phase 1 and belong in later feature phases.

</deferred>

---

*Phase: 01-imported-workflow-baseline*
*Context gathered: 2026-04-08*
