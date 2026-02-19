---
title: "Teaching an AI to Take Notes"
description: "How we built a system for capturing what happens during development sessions — and why it matters for AI-assisted projects."
date: 2026-02-19
categories:
    - ai-development
tags:
    - claude-code
    - workflow
    - lessons-learned
draft: false
---

We're 11 days into building Worlds of the Next Realm with Claude Code. Eleven repositories, hundreds of PRs, and enough debugging stories to fill a book. But until today, most of that institutional knowledge lived in one place: our heads. Or more accurately, in Claude's context window — which evaporates at the end of every session.

This post is about the system we built to fix that.

## The Problem

Every development session produces knowledge. Some of it is code — that gets committed. Some of it is process — why we chose approach A over approach B, what broke, what we'd do differently. That knowledge is valuable for blog posts, for onboarding new workspaces, for not repeating mistakes. But without a deliberate capture mechanism, it disappears.

We had pieces in place. The `WorldsOfTheNextRealm.Blog` repo has a `CLAUDE.md` file specifying how to write posts — front matter format, categories, writing style, the "human-directed, AI-authored" workflow. The `WorldsOfTheNextRealm.BlogNotes` repo had a README describing a `sessions/` and `topics/` directory structure. But there was no formal instruction telling Claude *when* to capture notes, *what format* to use, or *how* to coordinate writes across workspaces.

The result: session notes were written sometimes, in varying formats, with varying levels of detail. The CI pipeline session from earlier today produced excellent notes — 164 lines covering the goal, what went right, a detailed chain of six misses, technical details, and an emotional arc. The variable chunk size session produced 47 lines. Some sessions produced nothing.

## The Solution: A Satellite File

The project's `claude/` directory follows a pattern we've developed over the past week. A central `CLAUDE.md` links to satellite files, each owning one concern:

- `pr-workflow.md` — PR checklists, review comments, session stats
- `build-commands.md` — how to build, test, deploy each repo
- `cdk-patterns.md` — AWS CDK conventions
- `debugging.md` — common failure modes and fixes
- `permissions.md` — what commands Claude is allowed to run
- `memory.md` — how to handle "remember this" requests
- `repo-structure.md` — where code lives across repos

The pattern works because each file is independently readable, small enough to fit in context, and referenced by name from `CLAUDE.md` with a one-line description. Claude reads the relevant satellite file before starting a task — the PR workflow file before creating a PR, the build commands file before running tests.

Adding `blog-workflow.md` was the natural next step. After creating a PR, Claude should also capture session notes. The file specifies:

**When**: After any session that creates PRs.

**Where**: `WorldsOfTheNextRealm.BlogNotes/sessions/YYYY-MM-DD-<topic>/notes.md`

**What format**: A template with sections for date, repos touched, PRs created, outcome, what went right, what went wrong, technical details, and takeaways.

**Coordination**: Each session writes to its own uniquely-named directory. If the same date+topic exists, append a sequence number. Always `git pull` before committing. Direct-to-main is fine for BlogNotes — it's a private workspace.

**Blog posts**: Drafts in BlogNotes, published posts in the Blog repo via full PR workflow. User directs the topic, Claude writes from session notes, never fabricates details.

## Why This Matters

### Consistency

The CI pipeline session notes were great because the session was dramatic — six PRs, compounding errors, a frustrated user. The variable chunk size session notes were sparse because it went smoothly. But "smooth" sessions often contain the most useful patterns. A clean four-repo refactoring where every caller was identified upfront and nothing broke? That's worth documenting precisely *because* it was unremarkable. The template ensures minimum coverage regardless of how exciting the session was.

### Accumulation

Individual session notes are moderately useful. A year's worth of session notes — searchable, cross-referenced, organized by topic — is a goldmine. They're the raw material for blog posts, the evidence for process improvements, and the institutional memory that survives context window resets.

The `topics/` directory handles the cross-referencing. When a pattern appears in multiple sessions (like the content-hash blind spot, which has now bitten us three times), it gets extracted into a topic file that links back to the originating sessions. Over time, topics become the project's real documentation — not what we planned to do, but what actually happened.

### Bootstrapping

Here's the part that amuses me: this blog post exists because of the system it describes. The blog-workflow documentation session produced session notes (using the template from the file it just created), and those notes are one of the inputs to this post.

It's a feedback loop: sessions generate notes, notes become posts, posts document the process, the process improves future sessions. The loop only works if the capture step is reliable and consistent — which is exactly what the satellite file ensures.

## The Satellite File Pattern

Zooming out, the `claude/` directory has become something interesting. It started as a single `CLAUDE.md` with a few rules. Over eleven days it grew into a structured knowledge base:

```
claude/
├── CLAUDE.md              # Hub — links to everything
├── blog-workflow.md       # Session notes, blog writing
├── build-commands.md      # Build, test, deploy per repo
├── cdk-patterns.md        # AWS CDK conventions
├── debugging.md           # Common failures and fixes
├── memory.md              # Cross-session memory rules
├── permissions.md         # Allowed commands
├── pr-workflow.md         # PR checklists, review format
└── repo-structure.md      # Where code lives
```

Every file exists because something went wrong or something was inconsistent. `pr-workflow.md` exists because PRs were created without stats comments. `debugging.md` exists because the same CDK errors kept appearing. `permissions.md` exists because Claude ran commands it shouldn't have. `blog-workflow.md` exists because session notes were inconsistent.

The pattern is reactive: notice a problem, document the fix, reference it from the hub. Over time, the reactive additions form a proactive system — new sessions start with better instructions because previous sessions identified the gaps.

This is, I think, one of the underappreciated aspects of working with AI coding assistants. The AI doesn't carry context between sessions (beyond whatever fits in memory files). It needs explicit written instructions. The discipline of writing those instructions — clearly, precisely, with examples — is itself valuable. It forces you to articulate things that experienced developers "just know" and rarely write down.

## What's Next

The system is in place. The question now is whether it produces enough raw material for regular blog posts. The CI pipeline session alone generated enough for a full post. The mini-map and chunk size sessions combined into another. If we're creating 5-10 PRs per session and capturing notes each time, there should be plenty of material.

The other question is whether the notes get *better* over time. The template provides a floor, but the CI pipeline notes were good because the session was well-understood by the time they were written — the debugging was done, the mistakes were analyzed, the lessons were clear. Sessions that end in a "ship it and move on" state may produce thinner notes. We'll see.

For now, the loop is closed: code → PRs → session notes → blog posts → published. Every step has instructions. Every instruction exists because something went wrong without it.

That's the whole game with AI-assisted development. Not building the perfect system on day one. Building the system that improves itself every time something breaks.

---

*This post is part of a series about building Worlds of the Next Realm with Claude Code. The system described in this post was used to write this post. We're aware of the irony.*
