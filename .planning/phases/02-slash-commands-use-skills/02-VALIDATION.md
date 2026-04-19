---
phase: 2
slug: slash-commands-use-skills
status: draft
nyquist_compliant: false
wave_0_complete: false
created: 2026-04-08
---

# Phase 2 — Validation Strategy

> Per-phase validation contract for feedback sampling during execution.

---

## Test Infrastructure

| Property | Value |
|----------|-------|
| **Framework** | Rust `cargo test` + desktop `vitest` |
| **Config file** | `ui/desktop/vitest.config.ts` |
| **Quick run command** | `cargo test -p goose --lib slash_commands execute_commands && cargo test -p goose-server config_management && cd ui/desktop && pnpm test -- --run MentionPopover ChatInput` |
| **Full suite command** | `cargo test -p goose && cargo test -p goose-server && cd ui/desktop && pnpm test -- --run` |
| **Estimated runtime** | ~180 seconds |

---

## Sampling Rate

- **After every task commit:** Run `cargo test -p goose --lib slash_commands execute_commands && cargo test -p goose-server config_management && cd ui/desktop && pnpm test -- --run MentionPopover ChatInput`
- **After every plan wave:** Run `cargo test -p goose && cargo test -p goose-server && cd ui/desktop && pnpm test -- --run`
- **Before `/gsd-verify-work`:** Full suite must be green
- **Max feedback latency:** 180 seconds

---

## Per-Task Verification Map

| Task ID | Plan | Wave | Requirement | Threat Ref | Secure Behavior | Test Type | Automated Command | File Exists | Status |
|---------|------|------|-------------|------------|-----------------|-----------|-------------------|-------------|--------|
| 2-01-01 | 01 | 1 | SKILL-01 | T-02-01 | Slash discovery reuses working-dir-aware skill availability rules | unit/integration | `cargo test -p goose summon` | ✅ | ⬜ pending |
| 2-01-02 | 01 | 1 | SKILL-01, SKILL-04 | T-02-02 | Server slash payload returns safe canonical skill reference data without bypassing local discovery rules | route/unit | `cargo test -p goose-server config_management` | ✅ | ⬜ pending |
| 2-01-03 | 01 | 1 | SKILL-01 | T-02-03 | CLI completion uses shared discovery rather than a divergent static-only list for skills | unit | `cargo test -p goose-cli completion` | ✅ | ⬜ pending |
| 2-02-01 | 02 | 2 | SKILL-02, SKILL-03 | T-02-04 | Desktop slash selection inserts canonical `[name](path)` text for skills | component | `cd ui/desktop && pnpm test -- --run MentionPopover` | ✅ | ⬜ pending |
| 2-02-02 | 02 | 2 | SKILL-02, SKILL-03 | T-02-05 | Composer renders markdown-link references with stable round-trip text transport | component/integration | `cd ui/desktop && pnpm test -- --run ChatInput` | ✅ | ⬜ pending |
| 2-02-03 | 02 | 2 | SKILL-04 | T-02-06 | UI rendering does not alter sent message text or create out-of-band metadata-only skill payloads | integration | `cd ui/desktop && pnpm test -- --run ChatInput MentionPopover` | ✅ | ⬜ pending |
| 2-03-01 | 03 | 3 | SKILL-03, SKILL-04 | T-02-07 | Contributor-visible docs and verification prove consistency across `/skills`, slash suggestions, and sent text | docs/manual+auto | `rg -n "\[name\]\(path\)|slash|skill|local-first|safety" .planning/phases/02-slash-commands-use-skills ui/desktop/src crates/goose crates/goose-server` | ✅ | ⬜ pending |

*Status: ⬜ pending · ✅ green · ❌ red · ⚠️ flaky*

---

## Wave 0 Requirements

- [ ] `crates/goose-server/tests/` coverage for slash command response shape if no route test exists yet
- [ ] `ui/desktop/src/components/__tests__/` or colocated component tests for `MentionPopover` and `ChatInput` slash skill selection behavior
- [ ] `crates/goose-cli/src/session/completion.rs` test fixture updates for dynamic skill completion if shared discovery is introduced

---

## Manual-Only Verifications

| Behavior | Requirement | Why Manual | Test Instructions |
|----------|-------------|------------|-------------------|
| Slash popover shows skills when expected for the current working directory | SKILL-01 | Requires real working-dir + UI interaction context | Launch desktop, open a session in a repo with known skills, type `/`, confirm skills visible in the popover align with `/skills` output |
| Selected skill renders as visually distinct token/chip but sends canonical markdown-link text | SKILL-02, SKILL-03 | Requires UI appearance and real send-path confirmation | Select a skill from slash suggestions, inspect composer rendering, send the message, confirm stored/sent text is `[name](path)` |
| Generic markdown links render safely without weakening trust or local-only semantics | SKILL-04 | Requires judgment about visual treatment and no hidden metadata channel | Paste a non-skill `[name](path)` link and a skill link, verify both render, skill remains distinguishable, and neither introduces hidden execution behavior |

---

## Validation Sign-Off

- [ ] All tasks have `<automated>` verify or Wave 0 dependencies
- [ ] Sampling continuity: no 3 consecutive tasks without automated verify
- [ ] Wave 0 covers all MISSING references
- [ ] No watch-mode flags
- [ ] Feedback latency < 180s
- [ ] `nyquist_compliant: true` set in frontmatter

**Approval:** pending
