# Phase 2 Verification

## Checklist

- [x] Shared skill discoverability is explicitly planned across the same `summon`-backed availability rules used by agent-side discovery (`SKILL-01`)
- [x] Supported command incorporation is explicitly planned through canonical `[name](path)` insertion and bounded UI execution behavior (`SKILL-02`)
- [x] Contributor understanding and traceability are explicitly planned through generated API contracts, `/skills` alignment, docs updates, and phase-local verification artifacts (`SKILL-03`)
- [x] Safety and local-first guarantees are explicitly preserved through reuse of existing discovery/path-safety seams and explicit verification steps (`SKILL-04`)

## Automated Evidence

### SKILL-01 — slash commands can discover available skills relevant to the invoked command or task
- `02-01-PLAN.md` Task 1 requires `crates/goose-server/src/routes/config_management.rs` to keep skill discovery anchored to `crates/goose/src/agents/platform_extensions/summon.rs`
- `02-01-PLAN.md` Task 3 requires `crates/goose-cli/src/session/completion.rs` to stop relying only on a fixed slash-command list for skill completion
- Validation commands:
  - `cargo test -p goose summon`
  - `cargo test -p goose-server config_management`
  - `cargo test -p goose-cli completion`

### SKILL-02 — slash commands can invoke or incorporate skills during execution using project-supported patterns
- `02-02-PLAN.md` Task 1 requires `ui/desktop/src/components/MentionPopover.tsx` and `ui/desktop/src/components/ChatInput.tsx` to insert canonical `[name](path)` text for skill selection
- `02-02-PLAN.md` Task 2 requires bounded markdown-link rendering in `ChatInput.tsx` while preserving canonical text transport
- Validation commands:
  - `cd ui/desktop && pnpm test -- --run MentionPopover ChatInput`
  - `rg -n "\[.*\]\(.*\)" ui/desktop/src/components/MentionPopover.tsx ui/desktop/src/components/ChatInput.tsx`

### SKILL-03 — skill usage through slash commands is understandable to contributors and traceable in planning and implementation artifacts
- `02-01-PLAN.md` Task 2 requires generated API contract updates in `ui/desktop/src/api/types.gen.ts` and `ui/desktop/src/api/sdk.gen.ts`
- `02-03-PLAN.md` Task 2 requires contributor-facing alignment in `crates/goose/src/agents/execute_commands.rs` and `documentation/docs/guides/goose-cli-commands.md`
- Phase artifacts present:
  - `.planning/phases/02-slash-commands-use-skills/2-CONTEXT.md`
  - `.planning/phases/02-slash-commands-use-skills/02-RESEARCH.md`
  - `.planning/phases/02-slash-commands-use-skills/02-VALIDATION.md`
  - `.planning/phases/02-slash-commands-use-skills/02-01-PLAN.md`
  - `.planning/phases/02-slash-commands-use-skills/02-02-PLAN.md`
  - `.planning/phases/02-slash-commands-use-skills/02-03-PLAN.md`

### SKILL-04 — skill-enabled command flows preserve existing safety, permissions, and local-first execution guarantees
- `02-01-PLAN.md` requires server/CLI discovery to reuse `summon` rather than adding a new registry
- `02-02-PLAN.md` requires canonical plain-text transport instead of a hidden metadata-only skill payload
- `02-03-PLAN.md` requires verification and docs to explicitly call out local-first behavior and safety constraints
- Validation evidence paths:
  - `crates/goose/src/agents/platform_extensions/summon.rs`
  - `crates/goose-server/src/routes/config_management.rs`
  - `ui/desktop/src/components/ChatInput.tsx`
  - `.planning/phases/02-slash-commands-use-skills/02-VALIDATION.md`

## Manual Evidence

- Open the desktop app in a repo with known available skills, type `/`, and confirm the slash popover includes skills that match the current working directory and `/skills` output
- Select a skill from the slash popover and confirm the composer inserts canonical markdown-link text in the form `[name](path)` rather than a prose stub
- Confirm the composer renders the selected skill as a visually distinct chip/tag-like token while still preserving the literal markdown-link text under edit/send behavior
- Paste or type a non-skill markdown link `[name](path)` and confirm generic markdown-link rendering still works without being misrepresented as a skill
- Send a message containing a selected skill reference and confirm the message content reaching the agent remains canonical markdown-link text, not a hidden structured payload

## Gaps

None in planning artifacts. Execution still needs to prove the automated and manual evidence above during `/gsd-execute-phase 2`.

## Readiness Decision

Ready to execute. Phase 2 planning now includes explicit research, a validation strategy, and three executable plans covering shared discovery, canonical desktop insertion/rendering, and verification/documentation closure. All four mapped requirements (`SKILL-01` through `SKILL-04`) are assigned to at least one plan, and the highest-risk area — composer markdown-link rendering with canonical text round-trip — is explicitly isolated and test-backed rather than left implicit.
