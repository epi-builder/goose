---
phase: 2
reviewers: [codex]
reviewed_at: 2026-04-08T07:36:00Z
plans_reviewed: [02-01-PLAN.md, 02-02-PLAN.md, 02-03-PLAN.md]
---

# Cross-AI Plan Review — Phase 2

## Codex Review

## 02-01-PLAN.md

**Summary**  
This is the strongest of the three plans. It anchors Phase 2 on the right seam by reusing `summon` discovery, extends the server contract instead of inventing a side channel, and explicitly preserves existing builtin/recipe behavior. The main weakness is that it assumes the resolved skill path can be exposed as-is without fully specifying normalization, absent-path behavior for builtin skills, or how CLI completion should represent dynamically discovered skills when it cannot render the richer desktop UX.

**Strengths**
- Reuses `crates/goose/src/agents/platform_extensions/summon.rs` as the canonical discovery source, which directly supports `SKILL-01` and avoids registry drift.
- Correctly treats server/OpenAPI generation as part of the contract boundary instead of letting desktop infer fields ad hoc.
- Keeps CLI in scope early enough to reduce desktop/CLI divergence.
- Threat model is relevant and tied to concrete implementation choices.
- Acceptance criteria are mostly behavior-oriented, not just file-edit-oriented.

**Concerns**
- `HIGH`: The plan does not define how builtin skills vs filesystem skills expose canonical references. If `SourceKind::BuiltinSkill` has no stable filesystem path, `[name](path)` can become ambiguous or inconsistent.
- `HIGH`: The plan says “include canonical reference metadata derived from the resolved `Source` path” but does not specify whether the field is always present, optional, normalized, platform-escaped, or validated for markdown insertion safety.
- `MEDIUM`: CLI completion scope is underspecified. It says CLI can “surface dynamically discovered skills” but does not define whether insertion is `/name`, `[name](path)`, or completion-only display. That leaves room for a user-visible mismatch.
- `MEDIUM`: Verification commands are weakly targeted. `cargo test -p goose summon` and `cargo test -p goose-cli completion` may not map to real test names or guarantee the new behavior is covered.
- `MEDIUM`: No explicit handling for working directory changes, unreadable skill dirs, symlink-filtered skills, or duplicate skill names across sources.
- `LOW`: `ui/desktop/src/api/types.gen.ts` and `sdk.gen.ts` are listed as modified files even though they are generated artifacts; the plan should emphasize they are outputs, not hand-edited inputs.

**Suggestions**
- Define the canonical reference contract precisely:
  - `reference_path` required for filesystem skills.
  - explicit strategy for builtin skills, either a canonical pseudo-path already supported by the agent or exclusion from structured insertion until supported.
- Add acceptance criteria for duplicate-name handling and deterministic ordering of discovered skills.
- Add tests for markdown-unsafe characters in skill names/paths so insertion remains valid.
- Clarify CLI behavior explicitly: completion display only, or insertion of canonical `[name](path)` text if supported.
- Replace loose verification commands with concrete test targets or add a requirement to create them.

**Risk Assessment**  
**MEDIUM**. The architecture is sound, but the canonical reference format is not specified tightly enough yet. If builtin skill identity and path encoding are not resolved up front, downstream desktop work will either block or paper over the mismatch.

---

## 02-02-PLAN.md

**Summary**  
This plan targets the core user-visible behavior and is directionally correct, but it carries the most delivery risk. Replacing skill prose insertion is straightforward; adding inline markdown-link rendering in the desktop composer is not. The plan acknowledges the danger of a full editor rewrite, but it still leaves too much ambiguity around the current `ChatInput` architecture, editing semantics, cursor behavior, IME behavior, and send-path round-trip guarantees.

**Strengths**
- Directly addresses the main product inconsistency: skills currently insert prose instead of canonical references.
- Preserves plain-text transport to the agent, which aligns well with `D-09`, `D-12`, and `SKILL-04`.
- Explicitly supports arbitrary `[name](path)` rendering, not just skill links, which matches the phase decisions.
- Keeps builtin and recipe insertion behavior unchanged.
- Includes regression tests for both selection and round-trip behavior.

**Concerns**
- `HIGH`: “Implement the smallest viable rendering/parsing layer in `ChatInput.tsx`” is too vague for a potentially difficult editor/input problem. This is the plan most likely to expand unexpectedly.
- `HIGH`: No explicit treatment of cursor movement, deletion, copy/paste, partial edits inside `[name](path)`, undo/redo, or IME composition. Those are common failure modes for tokenized input UIs.
- `HIGH`: The plan does not define what happens when a generic markdown link is malformed, partially typed, or only temporarily valid during editing.
- `MEDIUM`: It assumes `MentionPopover` can build insertion text directly from generated metadata, but does not require defensive handling when `referencePath` is absent or stale.
- `MEDIUM`: Testing focuses on insertion and rendering, but not on actual send serialization or state restoration after edits.
- `MEDIUM`: `ItemIcon.tsx` is included, but the plan does not justify whether icon changes are necessary or just visual churn.
- `LOW`: The verify regexes are not strong evidence of behavioral correctness.

**Suggestions**
- Split the rendering task into explicit sub-behaviors:
  - parsing/display of valid markdown links,
  - fallback behavior for malformed/partial links,
  - preservation of raw textarea value,
  - send-path serialization.
- Add acceptance criteria for editing behavior:
  - backspace across token boundaries,
  - inserting text before/after a rendered token,
  - pasting canonical `[name](path)` text,
  - sending after manual edits.
- Add a specific non-goal: no full editor replacement in this phase.
- Require a failure-safe fallback: if rich rendering cannot be applied, the composer must still behave correctly with raw canonical markdown text.
- Add tests for malformed links, duplicate skill names, and references with spaces or parentheses in paths.

**Risk Assessment**  
**HIGH**. The intended outcome is correct, but this plan is where scope creep and UX fragility are most likely. Without tighter constraints on editor behavior and fallback semantics, implementation could either overrun or ship with subtle input bugs.

---

## 02-03-PLAN.md

**Summary**  
This plan is useful as readiness/documentation closure, but it is the weakest in terms of phase-critical impact. The verification artifact and docs are worthwhile, and aligning `/skills` language with slash behavior helps `SKILL-03`. But this plan risks spending execution time on planning-state polish before the hardest implementation uncertainties are resolved. It should remain lightweight and should not block shipping if wording-level changes lag behind code.

**Strengths**
- Gives the phase an explicit verification artifact tied to requirements, which improves auditability.
- Keeps contributor-facing `/skills` language aligned with implementation, reducing conceptual drift.
- Updates `.planning/STATE.md` to make execution status explicit.
- Threat model is relevant to traceability and safety claims.

**Concerns**
- `MEDIUM`: This plan is partially documentation/process work rather than implementation work. For an execution phase, it may be better positioned as closure after code lands rather than a separate wave.
- `MEDIUM`: Updating `crates/goose/src/agents/execute_commands.rs` for wording may create unnecessary churn in a core behavior file if the actual `/skills` UX does not need functional changes.
- `MEDIUM`: The plan assumes docs can describe final behavior before Plan 02 implementation details are proven feasible.
- `LOW`: `.planning/STATE.md` readiness updates are operationally useful but not directly tied to product behavior.
- `LOW`: Verification artifact sections are overly prescribed in format; that can encourage checklist completion over useful evidence.

**Suggestions**
- Reorder this plan to happen after implementation is complete, or treat it as closeout criteria rather than its own execution wave.
- Narrow `execute_commands.rs` changes to wording only if there is a real mismatch after implementation; otherwise prefer docs-only updates.
- Make `02-VERIFICATION.md` evidence-driven:
  - exact automated tests added,
  - manual steps tied to known UI risks,
  - explicit unresolved gaps if Plan 02 falls back to raw markdown rendering.
- Add a requirement that documentation reflect actual shipped behavior, including any scoped limitations in CLI parity or composer editing.

**Risk Assessment**  
**LOW-MEDIUM**. This plan is unlikely to break the product, but it is not on the critical path the way Plans 01 and 02 are. The main risk is sequencing inefficiency and documenting behavior before implementation realities are settled.

---

## Consensus Summary

Single-reviewer synthesis from Codex.

### Agreed Strengths
- Phase 2 is decomposed sensibly across shared discovery contract, desktop UX, and closeout/verification work.
- The plans are well aligned to the phase requirements and user decisions.
- Reusing `summon` discovery and OpenAPI-generated contracts is the right architectural seam.
- The plans preserve existing builtin and recipe behavior while moving toward canonical skill references.

### Agreed Concerns
- **Highest priority:** canonical `[name](path)` identity is underspecified, especially for builtin skills and path normalization/escaping.
- **Highest priority:** desktop composer rendering/editing behavior is underplanned relative to implementation risk.
- CLI parity expectations remain ambiguous, especially around completion vs insertion behavior.
- Verification is too dependent on loose commands or regex/file checks rather than behavior-focused tests.
- Plan 03 may be sequenced too early and should likely be treated as closeout work.

### Divergent Views
- No cross-reviewer divergence available because only one external reviewer was installed and ran successfully.
