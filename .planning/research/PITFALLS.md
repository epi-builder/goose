# Research: Pitfalls

**Project:** goose
**Date:** 2026-04-07
**Mode:** Brownfield repository synthesis

## Key Pitfalls

### 1. Bypassing the shared core
**Risk:** New logic lands in CLI/server/UI layers and duplicates behavior.
**Warning signs:** Similar logic appears across crates; feature behavior differs by surface.
**Prevention:** Put non-trivial behavior in `crates/goose/` first.
**Phase fit:** Early architecture / implementation phases.

### 2. Server/Desktop contract drift
**Risk:** Route changes are not reflected in generated OpenAPI artifacts and TS client code.
**Warning signs:** UI compile/runtime breakage after server edits; stale generated diffs.
**Prevention:** Treat `just generate-openapi` as mandatory for contract changes; verify generated outputs in the same phase.
**Phase fit:** Any desktop-backed backend phase.

### 3. Security regressions in agentic paths
**Risk:** New capabilities bypass permission, egress, or prompt inspection layers.
**Warning signs:** Direct tool execution or network behavior added without touching security/permission code.
**Prevention:** Explicitly review security-sensitive flows in planning and verification criteria.
**Phase fit:** Every phase touching execution, providers, tools, or desktop shell behavior.

### 4. Provider parity and auth edge cases
**Risk:** Features work for one provider/auth path but not others.
**Warning signs:** Changes only tested against a single provider path; config UI drift.
**Prevention:** Keep provider abstractions central; verify intended compatibility surface.
**Phase fit:** Provider/config-focused phases.

### 5. Monorepo sprawl harming contributor clarity
**Risk:** Contributors cannot find the right place to work or the right checks to run.
**Warning signs:** Repeated confusion about ownership, build steps, or generated files.
**Prevention:** Make roadmap phases include documentation and workflow clarity where architecture touches multiple surfaces.
**Phase fit:** Foundation / contributor-experience phases.

### 6. Over-broad roadmap phases
**Risk:** A large brownfield repo gets phases too wide to plan or verify effectively.
**Warning signs:** Phases combine core, server, UI, docs, and release work without a coherent goal.
**Prevention:** Use standard granularity and phase by user-visible outcome plus technical boundary.
**Phase fit:** Roadmap structure itself.
