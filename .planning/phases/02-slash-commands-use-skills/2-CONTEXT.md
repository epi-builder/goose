# Phase 2: Slash Commands Use Skills - Context

**Gathered:** 2026-04-08
**Status:** Ready for planning

<domain>
## Phase Boundary

Make slash commands able to discover, reference, and use skills as a first-class product capability. This phase should align slash-command completion with the same skill-availability rules the agent uses, support selection of discovered skills during slash input, and define how selected skill references are represented and rendered in the desktop composer. This phase does not add unrelated new capabilities beyond slash-command skill usage, and it should preserve existing safety and local-first behavior.

</domain>

<decisions>
## Implementation Decisions

### Skill discovery consistency
- **D-01:** The set of skills surfaced during slash-command completion must use the same availability rules as the agent-side skill/source discovery logic for the current session and working directory.
- **D-02:** The product should avoid any state where a skill is usable by the agent but absent from slash completion, or visible in slash completion but unavailable for actual use.
- **D-03:** Contributor-facing and user-facing skill discovery should stay consistent across agent context, slash dropdowns, and related skill-listing surfaces wherever practical.

### Slash completion behavior
- **D-04:** Slash-command UX should support completion for discovered skills during slash input, not only builtin commands or recipe slash commands.
- **D-05:** Choosing a skill from the slash dropdown should insert a structured skill reference into the composer instead of inserting a natural-language helper phrase.
- **D-06:** The slash dropdown should present skills as first-class selectable items alongside other slash-eligible entities while preserving understandable distinctions between item types.

### Skill reference representation
- **D-07:** The initial canonical text representation for an inserted skill reference is `[name](path)`.
- **D-08:** For this phase, the canonical identifier should be the resolved skill path, while keeping the design open to a future stable skill ID if one is introduced later.
- **D-09:** The agent should receive the original markdown-link-form skill reference as message text; no separate metadata payload is required for the initial implementation.

### Desktop composer rendering
- **D-10:** The desktop interface should visually render `[name](path)` references as inline tag/chip-like elements rather than exposing only raw markdown syntax.
- **D-11:** The desired visual model is link-aware and should distinguish parts of the reference in the interface, including path-oriented styling and skill-oriented token styling similar to the provided reference UX.
- **D-12:** The composer must preserve round-trip stability between editing, display, and send behavior so the user-visible rich rendering still maps back to the canonical `[name](path)` text form.

### Markdown-link rendering scope
- **D-13:** The interface support should cover arbitrary `[name](path)` markdown-link structures, not only skill-specific links.
- **D-14:** Skill references may receive specialized presentation within that broader markdown-link rendering model, but the underlying input/rendering capability should not be limited to skill links alone.

### Research and validation expectations
- **D-15:** Planning for this phase should include research into whether the current desktop composer/input architecture can support slash-triggered insertion plus inline rendering/editing of markdown-link-based tokens.
- **D-16:** Verification should prove both discoverability consistency and end-user behavior: skill completion appears when expected, selected references render correctly, and sent text remains in canonical markdown-link form.

### Claude's Discretion
- Exact ranking, grouping, and labeling details within the slash dropdown
- Exact visual treatment of generic markdown links versus skill-specific references, as long as both are supported and skill references remain easy to distinguish
- Whether the initial implementation uses lightweight inline rendering, richer token components, or another editor-compatible mechanism, provided canonical text round-trip is preserved
- Whether CLI and desktop reach full parity in one pass or desktop leads while shared discovery rules are established first

</decisions>

<specifics>
## Specific Ideas

- The target experience is similar to Codex-style slash UX where available skills appear inside the slash dropdown during input.
- Selecting a skill from the dropdown should insert a structured reference rather than a prose prompt stub.
- The canonical inserted form should look like `[gsd-add-backlog](/Users/epikem/.codex/skills/gsd-add-backlog/SKILL.md)` or equivalent resolved-skill-path form.
- In the interface, path-oriented content should read visually like a link treatment, while the skill itself should read visually like a purple chip/tag.
- Even though the interface renders rich tokens, the sent message body may remain plain markdown-link text because the agent can already interpret that form adequately.

</specifics>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Phase scope and requirements
- `.planning/ROADMAP.md` — Defines Phase 2 goal, plans, and success criteria for slash-command skill discovery, invocation, traceability, and safety.
- `.planning/REQUIREMENTS.md` — Defines `SKILL-01`, `SKILL-02`, `SKILL-03`, and `SKILL-04`, which govern discoverability, invocation, contributor understanding, and safety guarantees.
- `.planning/PROJECT.md` — Confirms Phase 2 as the next feature priority after preserving the planning baseline.
- `.planning/STATE.md` — Captures current planning state and notes that skill-aware slash commands may cross CLI, server, and UI seams.

### Existing skill/slash implementation seams
- `crates/goose/src/agents/platform_extensions/summon.rs` — Current source and skill discovery logic that should anchor slash-completion consistency.
- `crates/goose/src/agents/execute_commands.rs` — Existing builtin slash command execution path, including `/skills` behavior.
- `crates/goose/src/slash_commands.rs` — Current recipe-oriented slash-command mapping behavior and normalization rules.
- `crates/goose-cli/src/session/input.rs` — CLI slash-command parsing path and current command handling behavior.
- `crates/goose-cli/src/session/completion.rs` — Current slash completion behavior, which is presently limited and relevant to parity decisions.
- `crates/goose-server/src/routes/config_management.rs` — Server endpoint that returns slash commands and already mixes builtin, recipe, and skill command types.
- `ui/desktop/src/components/MentionPopover.tsx` — Desktop slash dropdown behavior, selection behavior, and current item rendering/selection contract.

### Baseline planning context
- `.planning/phases/01-imported-workflow-baseline/1-CONTEXT.md` — Phase 1 downstream planning contract and canonical baseline expectations that Phase 2 should inherit.
- `.planning/phases/01-imported-workflow-baseline/01-VERIFICATION.md` — Confirms the preserved planning baseline is ready for downstream phase work.

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- `crates/goose/src/agents/platform_extensions/summon.rs`: Already discovers filesystem and builtin skills from multiple local/global directories; likely the strongest starting point for a single skill-availability rule.
- `crates/goose-server/src/routes/config_management.rs`: Already returns slash command entries for builtin commands, recipes, and skills to the desktop client.
- `ui/desktop/src/components/MentionPopover.tsx`: Already fetches slash-command suggestions and has a display-item abstraction that can distinguish `Builtin`, `Skill`, and `Recipe` types.
- `crates/goose/src/agents/execute_commands.rs`: Already has builtin command definitions and user-facing skill listing behavior, which may help align terminology.

### Established Patterns
- Recipe slash commands currently resolve through dedicated stored mappings in `crates/goose/src/slash_commands.rs`, while skills are discovered dynamically from source directories.
- The desktop mention popover already treats slash-mode suggestions differently from file-path suggestions, so this is a natural place to evolve skill completion UX.
- Current desktop selection behavior inserts `/name` for builtin/recipe items but inserts a prose stub for skills, which is the exact inconsistency this phase should remove.
- CLI completion is currently based on a fixed slash-command list and does not yet reflect dynamic skills.

### Integration Points
- Shared skill discovery rules likely need to be centralized or reused across agent prompt loading, server slash-command listing, and any future CLI/desktop completion behavior.
- Desktop implementation will likely span server response shape, client dropdown presentation, composer insertion logic, and inline markdown-link rendering in the input experience.
- If generic markdown-link rendering is introduced in the composer, the same parsing/rendering path may later support more than just skills.

</code_context>

<deferred>
## Deferred Ideas

- Stable non-path skill identifiers can be introduced in a later phase if path-based identity becomes too fragile.
- Broader rich-text or structured-message composer upgrades beyond markdown-link rendering are out of scope unless required to support this phase’s `[name](path)` token behavior.
- Larger redesigns of all slash-command semantics beyond skill completion and structured insertion remain out of scope for this phase.

</deferred>

---

*Phase: 02-slash-commands-use-skills*
*Context gathered: 2026-04-08*
