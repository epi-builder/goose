# Phase 2 Research: Slash Commands Use Skills

**Phase:** 2 — Slash Commands Use Skills
**Date:** 2026-04-08
**Question:** What do we need to know to plan Phase 2 well?

## Summary

Phase 2 should treat `summon` source discovery as the canonical skill-availability mechanism and then align slash-command discovery, selection, and execution surfaces to that shared rule. The current codebase already exposes the right seam on the server side: `crates/goose-server/src/routes/config_management.rs` returns slash-command suggestions by combining recipe commands, builtin commands, and skills discovered by `goose::agents::platform_extensions::summon::list_installed_sources(working_dir.as_deref())`.

The main inconsistency is at the UX and execution boundary:
- desktop slash suggestions already include skills, but selecting one inserts prose (`Use the {name} skill to `) instead of a canonical structured reference
- CLI slash completion is still a static builtin list and does not include dynamic skills
- recipe slash commands are persisted via `crates/goose/src/slash_commands.rs`, while skills are discovered dynamically from filesystem and builtin sources
- contributor-facing `/skills` output already uses the same `summon` discovery path, so Phase 2 can reuse existing naming and availability behavior instead of inventing a parallel registry

The implementation should therefore plan around one source-of-truth discovery layer, explicit serialization of skill references as `[name](path)`, and targeted UI/editor work to preserve canonical text while rendering richer inline tokens.

## Code Findings

### 1. Canonical skill discovery already exists

`crates/goose/src/agents/platform_extensions/summon.rs` is the strongest source of truth for skill availability.

Relevant findings:
- `list_installed_sources(working_dir: Option<&Path>) -> Vec<Source>` returns sources for the current context
- skill discovery includes both filesystem skills and builtin skills
- discovery already handles working-directory-sensitive visibility, recursion, supporting files, path-safety checks, and symlink protections
- the server route and `/skills` command both already use this seam

Implication: Phase 2 should not create a new skill catalog for slash commands. It should either directly reuse `list_installed_sources` or extract a narrower shared helper from the same logic if sorting, filtering, or presentation metadata need standardization.

### 2. Server slash-command API already exposes skills

`crates/goose-server/src/routes/config_management.rs:get_slash_commands` currently builds slash suggestions from three sources:
1. recipe slash commands from `slash_commands::list_commands()`
2. builtin commands from `execute_commands::list_commands()`
3. skills from `summon::list_installed_sources(working_dir)` filtered to `Skill | BuiltinSkill`

This is important because it means the desktop does not need a new endpoint just to discover skills. The current gap is not discovery availability; it is representation and consistency.

Risk: because the route currently serializes only `command`, `help`, and `command_type`, it does not expose the canonical skill path required by Phase 2 decision D-07/D-08. The plan should explicitly evaluate whether to:
- extend the existing `SlashCommand` payload with a canonical `reference_path` or equivalent field, or
- introduce a shared resolver path after selection

The simpler and more traceable route is to extend the existing slash-command response to include the resolved source path for skills while keeping builtin and recipe fields stable.

### 3. Desktop already distinguishes skill items, but inserts the wrong text

`ui/desktop/src/components/MentionPopover.tsx` currently maps `CommandType::Skill` into display items and uses `getSelectionText()` to decide what is inserted.

Current behavior:
- skill → `Use the ${item.name} skill to `
- builtin/recipe → `/${item.name}`

This is the central Phase 2 product bug relative to the captured context. The desktop already knows an item is a skill; it just does not have the metadata or insertion rule to emit `[name](path)`.

Implication: a large part of Phase 2 is likely a targeted desktop change, not a new UI architecture. The popover contract and insertion flow already exist and can be updated to use canonical selection text for skills.

### 4. Composer rendering work is the highest implementation uncertainty

The user decisions require the desktop composer to:
- accept slash-triggered insertion of `[name](path)` references
- render arbitrary markdown links as richer inline UI
- visually distinguish skill links from generic links
- preserve round-trip stability so sent text remains canonical markdown-link text

The current available evidence shows `ChatInput.tsx` owns mention popover integration, but the research performed here did not establish that the composer already has a markdown tokenization layer suited for editable inline chips.

This is the phase’s main technical risk. The plan should explicitly require reading and testing the `ChatInput.tsx` insertion and editing flow before implementation choices are finalized.

Likely implementation options:
1. lightweight parser/renderer overlay that preserves underlying textarea text
2. token-aware contenteditable/editor approach
3. constrained inline decoration model in the current input architecture

Planning recommendation: start with the least invasive architecture that preserves plain-text transport and does not require replacing the editor. The phase should avoid broad editor rewrites.

### 5. CLI parity is currently behind desktop

`crates/goose-cli/src/session/completion.rs` still provides a static slash command array:
- `/exit`
- `/quit`
- `/help`
- `/?`
- `/t`
- `/extension`
- `/builtin`
- `/prompts`
- `/prompt`
- `/mode`
- `/recipe`

This does not reflect dynamic skills or recipe slash mappings beyond the static list. Meanwhile `crates/goose-cli/src/session/input.rs` forwards unknown slash input as a raw message if not handled by builtin slash parsing.

Implication: the phase must decide whether CLI only needs discovery/listing parity in this pass or whether it must also support structured skill insertion. The context explicitly allows desktop to lead while shared discovery rules are established first, so plans can scope CLI work to shared discovery logic and basic completion parity without forcing a full CLI rich-token UX.

### 6. Contributor-facing behavior already has a traceable baseline

`crates/goose/src/agents/execute_commands.rs:handle_skills_command` already lists installed skills using the same discovery seam. This provides:
- an existing contributor-facing explanation surface
- existing wording around where skills come from
- a natural place to align docs and traceability messaging

This matters for `SKILL-03`: contributor understanding and traceability do not need a net-new concept. They need alignment across `/skills`, slash suggestions, plan docs, and any UI copy.

## Constraints and Design Guidance

### Reuse shared-core patterns
- If discovery logic changes, it belongs in `crates/goose/`, not reimplemented separately in desktop or CLI.
- If server payloads change, route changes belong in `crates/goose-server/src/routes/` and should remain OpenAPI-driven for desktop.
- Desktop should consume generated API types rather than bespoke fetch payloads.

### Preserve safety and local-first behavior
- Skill discovery must remain filesystem/local-builtin based and respect current working directory boundaries.
- Path exposure in `[name](path)` must not relax existing path traversal or symlink protections already enforced in `summon.rs`.
- Rendering richer skill references must not change what the agent receives; canonical plain text should remain the transport format.

### Keep UI scope bounded
- Phase 2 is not a full composer rewrite.
- Generic markdown-link rendering support is required, but only insofar as it enables reliable insertion/editing/display of `[name](path)` references.
- Avoid introducing a parallel metadata channel for skill references in this phase.

## Recommended Planning Shape

Phase 2 should likely break into three executable plans:
1. shared discovery and API contract alignment across core/server/CLI seams
2. desktop selection and composer behavior for canonical `[name](path)` skill references plus generic markdown-link rendering contract
3. verification coverage and contributor-facing traceability proving consistency, safety, and canonical text transport

This matches the roadmap’s existing three-plan structure.

## Validation Architecture

### High-value automated verification targets
- Rust unit/integration tests proving skill discovery for slash surfaces uses the same working-dir-aware rules as `summon`
- server route tests proving `/config/slash_commands` includes skills with canonical path/reference data when available
- desktop component/unit tests proving selecting a `Skill` item inserts canonical markdown-link text rather than prose
- desktop tests proving generic markdown links and skill links render with distinct presentation while preserving underlying send text
- CLI completion tests proving slash completion can surface dynamic skills if Phase 2 includes CLI parity

### Manual verification likely still required
- end-to-end desktop UX: open slash popover, inspect ordering/labels, choose a skill, verify rendered chip-like UI, edit around the token, send the message, and confirm raw outgoing text remains `[name](path)`
- cross-surface consistency checks between `/skills`, server slash suggestions, and desktop display labels/descriptions

### Likely commands
- `cargo test -p goose`
- `cargo test -p goose-server`
- `cd ui/desktop && pnpm test`
- if server contract changes: `just generate-openapi`

## Risks

| Risk | Why it matters | Planning response |
|---|---|---|
| Composer token rendering is harder than expected | Could expand into a broad editor rewrite | Keep a bounded rendering contract and prefer minimal invasive insertion/rendering path |
| Slash API lacks canonical path metadata | Desktop cannot insert `[name](path)` deterministically | Add canonical skill path/reference field through shared server/OpenAPI contract |
| CLI and desktop drift again | Violates D-01/D-02 and `SKILL-01` | Centralize discovery logic in core and test parity from shared seams |
| Path-based references leak unsafe or unstable behavior | Impacts `SKILL-04` and local-first guarantees | Reuse resolved safe paths from `summon`, do not invent ad hoc path handling in UI |

## Recommendation

Proceed with research-backed planning that treats `summon` discovery as canonical, extends the slash-command contract with canonical skill reference metadata, scopes desktop work to canonical insertion plus bounded markdown-link rendering, and treats verification as a first-class deliverable rather than an afterthought.

## RESEARCH COMPLETE
