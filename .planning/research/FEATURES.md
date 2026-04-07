# Research: Features

**Project:** goose
**Date:** 2026-04-07
**Mode:** Brownfield repository synthesis

## Summary

For a 2025/2026 open-source local AI agent, the table stakes are already largely present in goose: multi-provider model access, tool execution, session persistence, extensibility, CLI/desktop interfaces, and strong developer ergonomics. The next planning horizon should treat these as baseline expectations and focus roadmap work on reliability, maintainability, safety, contributor experience, and high-confidence feature evolution.

## Table Stakes

### Agent core
- Multi-turn agent execution with tool calling
- Local session history and resumability
- Strong prompt / context / memory handling
- Support for complex engineering workflows, not just chat

### Surface area
- Usable CLI experience for developers
- Desktop UI for broader accessibility
- Local backend for desktop orchestration

### Ecosystem integration
- Multiple model providers and auth flows
- MCP/ACP and extension support
- Recipes / automation / reusable workflows

### Trust and safety
- Permission gating for dangerous actions
- Security inspection around prompts, tools, and egress
- Predictable local-first behavior

### OSS product expectations
- Install/build/test docs
- Cross-platform packaging and release automation
- Contributor-friendly repository structure and CI

## Differentiators
- Broad provider matrix under one product surface
- Local-first engineering automation positioning
- Shared-core architecture across CLI and desktop
- Integrated protocol support (MCP/ACP)
- Strong workflow orientation for real development tasks

## Anti-features / Things to Avoid
- Feature sprawl that weakens reliability of core execution paths
- Surface-specific behavior drift between CLI and desktop
- Hidden coupling between handwritten and generated API artifacts
- “Magic” behavior that bypasses permission/security review
- Contributor-hostile complexity without docs or tests

## Dependencies / Feature relationships
- Desktop features that require backend state or execution should map through server routes and generated client updates
- Provider-facing features often imply auth, config, UI onboarding, and testing impacts together
- Security-sensitive features must be designed alongside permission and inspection layers, not after implementation

## Planning Implications
- Treat existing shipped capabilities as validated, not speculative
- Focus new requirements on improving maintainability, reliability, and user trust while extending capability safely
- Use roadmap phases that reduce risk early: architecture clarity, contract integrity, surface parity, and contributor workflows
