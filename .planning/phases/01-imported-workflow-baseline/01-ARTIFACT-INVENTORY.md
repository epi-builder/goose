# Phase 1 Canonical Artifact Inventory

Phase 1 preserves the imported brownfield planning baseline as the canonical input set for downstream feature phases. Contributors should use this inventory as the first stop for understanding what must be retained, how it may be updated, and how later phases inherit the baseline.

## Canonical Input Set

The canonical baseline input set for Phase 1 is exactly:

- `.planning/PROJECT.md`
- `.planning/REQUIREMENTS.md`
- `.planning/ROADMAP.md`
- `.planning/STATE.md`
- `.planning/codebase/`
- `.planning/research/`

## Preservation Rules

- Imported artifacts are preserved whenever possible.
- Additive clarification is preferred over rewriting imported analysis.
- direct edits are limited to factual corrections, stale references, or traceability repairs.
- Any direct change to a canonical artifact should leave recoverable rationale in adjacent planning history, phase artifacts, or commit history.
- Future phases must treat this baseline as the default planning input set unless they document a justified exception.

## Artifact Rationale

- `.planning/PROJECT.md` — establishes the project-level contract, active priorities, constraints, and the canonical baseline narrative that later phases inherit.
- `.planning/REQUIREMENTS.md` — defines the requirement IDs and makes downstream planning traceable to preserved brownfield intent.
- `.planning/ROADMAP.md` — defines the ordered feature roadmap and shows how Phase 1 operationalizes the preserved baseline for later phases.
- `.planning/STATE.md` — records the active planning position, readiness narrative, and current focus so contributors can see whether the baseline is ready to use.
- `.planning/codebase/` — provides the canonical imported brownfield codebase analysis covering structure, architecture, conventions, integrations, concerns, stack, and testing expectations.
- `.planning/research/` — provides the canonical imported research synthesis that explains product context, planning implications, and brownfield pitfalls future phases must keep in view.

Later phases must document any intentional divergence from this baseline instead of silently redefining it.

## Non-Canonical Historical References

- `.planning/quick/` remains useful history for understanding how earlier planning changed over time, but it is not part of the canonical baseline unless a later phase explicitly promotes a document.
- Other historical artifacts may inform context, but they do not override the canonical input set listed above.
