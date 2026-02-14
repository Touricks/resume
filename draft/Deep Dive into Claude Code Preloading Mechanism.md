---
title: "Deep Dive into Claude Code's Preloading Mechanism"
date: 2026-02-14
tags:
  - claude-code
  - AI-tooling
  - dev-workflow
status: draft
lang: en
---

# Deep Dive into Claude Code's Preloading Mechanism: How Your AI Coding Assistant "Sees" Your Project

> What exactly gets loaded when Claude Code starts? Why aren't your subdirectory instructions taking effect? Can team agents automatically read local rules? This post covers it all.

## Why Should You Care About Preloading?

When working with Claude Code, you've probably written a `CLAUDE.md` to inject project context, configured `.claude/rules/`, or set up custom Agents and Skills. But have you ever wondered — **when and how are these configurations actually loaded?**

Understanding the preloading mechanism helps you:
- Avoid the frustration of "I wrote instructions but they're not working"
- Properly organize context for multi-directory projects
- Correctly inject information in team agent workflows

## The Five Core Components at a Glance

Claude Code's context system is built on 5 core components:

| Component | Location | Loading | Trigger |
|-----------|----------|---------|---------|
| **CLAUDE.md** | Project root & ancestors | Auto | Session start |
| **CLAUDE.md (subdirectory)** | Child directories | On-demand | When files in that subtree are accessed |
| **.claude/rules/\*.md** | Project root `.claude/rules/` | Auto | Session start |
| **.claude/agents/\*.md** | `.claude/agents/` | On-demand | `Task` tool with `subagent_type` |
| **SKILL.md** | `~/.claude/skills/` or project-level | On-demand | `/command` invocation |

The key takeaway: only **CLAUDE.md (root)** and **Rules** are auto-loaded at startup. Everything else is triggered on demand.

## CLAUDE.md: Bidirectional Directory Traversal

### Upward Walk (Ancestors) — Auto-Loaded

At session launch, Claude Code walks **UP** from the current working directory (CWD) to the filesystem root `/`:

```
/
└── Users/carrick/
    └── .claude/CLAUDE.md        ← User-level (if exists)
    └── myproject/
        ├── CLAUDE.md            ← ✅ Auto-loaded (project root)
        └── src/components/      ← CWD
```

All `CLAUDE.md` files found in ancestor directories are automatically loaded.

### Downward Walk (Children) — On-Demand

Subdirectory `CLAUDE.md` files are **discovered but NOT loaded** at launch. They only get loaded into context when Claude reads, searches, or operates on files within that subtree.

This makes sense — a large monorepo might have dozens of subdirectories each with their own `CLAUDE.md`. Loading all of them upfront would waste precious context window space.

### Priority (Low → High)

1. Managed policy (`/Library/Application Support/ClaudeCode/CLAUDE.md`)
2. User-level `~/.claude/CLAUDE.md`
3. Ancestor directories (walking UP)
4. Current directory `./CLAUDE.md`
5. **`./CLAUDE.local.md`** — Highest priority, ideal for personal overrides, gitignored by default

## Rules: Auto-Loaded from Project Root

All `.md` files under `.claude/rules/` are **recursively auto-loaded** at session start:

```
.claude/rules/
├── git-workflow.md      ← ✅ Auto-loaded
├── documentation.md     ← ✅ Auto-loaded
└── frontend/
    └── react.md         ← ✅ Also auto-loaded (recursive)
```

**Critical limitation**: Only `.claude/rules/` at the **project root** (git root) is recognized. Subdirectory `.claude/rules/` directories are **never loaded** — even with `alwaysApply: true` in the frontmatter.

## Agents & Skills: Invoked On Demand

- **Agents** (`.claude/agents/*.md`): Triggered via the `Task` tool's `subagent_type` parameter. Each agent runs in its own sub-session with an isolated system prompt.
- **Skills** (`SKILL.md`): Triggered by `/command` slash commands. Available globally (`~/.claude/skills/`) or at the project level.

Neither is loaded at startup — they're injected on demand, preserving context window space.

## Team Agent Context Isolation

This is where most people get tripped up. Team agents (teammates) spawned via the `Task` tool:

- Have their **own isolated context window**
- Can **only see** the prompt injected by the leader
- Do **NOT** auto-load the target directory's `CLAUDE.md`
- Do **NOT** see other agents' conversations

Empirical testing confirmed the following behavior comparison:

| Behavior | Main Session | Teammate |
|----------|:------------:|:--------:|
| Project root CLAUDE.md | ✅ Auto | ✅ Auto |
| Project root .claude/rules/ | ✅ Auto | ✅ Auto |
| Subdirectory CLAUDE.md | ⏳ On-demand | ⏳ On-demand |
| Subdirectory .claude/rules/ | ❌ Never | ❌ Never |

### The Workaround: Manual Prompt Injection

If a teammate needs context from a specific subdirectory, the leader must:

1. **Read** the target directory's `claude.md` / rules content
2. **Inject** that content into the teammate's spawn prompt
3. The teammate then works with the injected context in isolation

## Practical Recommendations

1. **Persist your config**: Put critical agent behavior instructions in `CLAUDE.md` or `.claude/rules/` so they survive `/compact` and persist across new sessions.
2. **Leverage `CLAUDE.local.md`**: Use it for personal preferences and overrides without polluting the team's git repository.
3. **Organize rules with subdirectories**: Feel free to use folders within `.claude/rules/` for categorization — everything is loaded recursively.
4. **Don't assume agents auto-discover context**: For team agent collaboration, the leader is responsible for reading and injecting subdirectory context.
5. **Be intentional with model selection**: The `Task` tool's `model` parameter (`haiku` / `sonnet` / `opus`) is a hard setting, not a suggestion. Choose wisely based on task complexity.

## Conclusion

Claude Code's preloading mechanism follows a clear design philosophy: **auto-load core configurations, lazy-load extensions, and strictly isolate agent contexts.** Once you understand this system, you can organize your project instructions more effectively, avoid the "configured but not working" trap, and navigate multi-agent collaboration with confidence.

---

*This post is based on hands-on testing conducted in February 2026. Claude Code's mechanisms may evolve with future updates.*
