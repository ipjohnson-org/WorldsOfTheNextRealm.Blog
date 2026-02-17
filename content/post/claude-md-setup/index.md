---
title: "Structuring CLAUDE.md for a Multi-Repo Project"
description: "How we use a shared claude/ directory with satellite files to give an AI coding assistant consistent rules across 11 repositories."
date: 2026-02-17
categories:
    - ai-development
tags:
    - claude-code
    - workflow
    - lessons-learned
draft: false
---

When you use Claude Code, the first thing it does when you open a workspace is look for a `CLAUDE.md` file. That file is the AI's operating manual — it tells Claude what the project is, what the rules are, and how to behave. For a single-repo project, one file is enough. For a multi-repo project with 11 repositories, one file is not enough and stuffing everything into it would be unreadable.

We needed a system. Here's what we built.

## The Problem

Worlds of the Next Realm has 11 repositories: a Flutter frontend, five .NET backend services, a shared NuGet package library, CDK infrastructure, operational tooling, documentation, game assets, and this blog. Claude Code works in one repository at a time, but the rules that govern how it should behave — branching strategy, PR workflow, permissions, NuGet versioning — are the same across all of them.

We could duplicate a `CLAUDE.md` in every repo, but that creates a maintenance problem. When a rule changes (and they change often — every rule exists because something went wrong), we'd need to update 11 files. We'd forget one. Claude would follow stale rules in that repo. Things would break.

## The Structure

The solution is a `claude/` directory that lives in the Documentation repository — the one repo dedicated to project-wide knowledge — and is symlinked to the workspace root so every repo can see it:

```
~/WorldsOfTheNextRealm/
├── CLAUDE.md -> claude/CLAUDE.md      # Symlink to the hub file
├── claude/ -> WorldsOfTheNextRealm.Documentation/claude/  # Symlink
├── WorldsOfTheNextRealm.Documentation/
│   └── claude/                        # The real files live here (under git)
│       ├── CLAUDE.md                  # Core rules + links to satellites
│       ├── repo-structure.md          # Where code lives across repos
│       ├── build-commands.md          # How to build each repo type
│       ├── cdk-patterns.md            # CDK conventions and gotchas
│       ├── debugging.md              # Known issues and fixes
│       ├── permissions.md             # What Claude can/can't do
│       ├── pr-workflow.md             # PR creation checklist
│       └── memory.md                  # Persistent cross-session context
├── WorldsOfTheNextRealm.BackendApi/
├── WorldsOfTheNextRealm.BackendCommon/
├── WorldsOfTheNextRealm.FrontEndClient/
├── WorldsOfTheNextRealm.Infra/
├── WorldsOfTheNextRealm.Blog/
│   └── CLAUDE.md                      # Repo-specific rules
├── WorldsOfTheNextRealm.OperationalTools/
│   └── CLAUDE.md                      # Repo-specific rules
└── ... (other repos)
```

The key detail is the symlinks. The `claude/` directory at the workspace root is a soft link pointing to `WorldsOfTheNextRealm.Documentation/claude/`. The root `CLAUDE.md` is itself a symlink to `claude/CLAUDE.md`. This means:

1. **The Documentation repo is the single source of truth.** The actual files are committed and maintained under git control in the Documentation repository.
2. **Changes go through PRs.** Updating a rule means editing a file in the Documentation repo, creating a PR, and merging — the same process as any other code change.
3. **Every workspace gets the rules automatically.** When Claude Code opens any repo under this workspace, the symlinked `CLAUDE.md` is picked up as if it were a local file. No duplication, no synchronization scripts, no risk of drift.

The root `CLAUDE.md` is short — it states the core rules and links to the satellite files for details.

## The Root CLAUDE.md

The root file is deliberately concise. It establishes the non-negotiable rules and points elsewhere for details:

```markdown
# WorldsOfTheNextRealm

Multi-repo game project. All repos under `~/<root-directory>/WorldsOfTheNextRealm.<name>`.

## Core Rules
- **Never** look in or modify files above the root directory.
- **Always** create feature branches and PRs. Never push directly to main.
- **Never** make AWS changes via the console. All infrastructure through CDK + PRs.
- **Always** read and understand existing code before modifying it.
- **Always** ask before deleting files, force-pushing, or running `cdk destroy`.
- **NuGet package versions** use `0.1.x`. CI sets the patch number. Never manually bump.

## Before You Start a Task
- Read [repo-structure.md](repo-structure.md) if you need to find where code lives.
- Read [build-commands.md](build-commands.md) before building, testing, or deploying.
- Read [cdk-patterns.md](cdk-patterns.md) before modifying any CDK code.
- Read [debugging.md](debugging.md) when troubleshooting deployment or CI failures.

## Before Creating a PR
- Read [pr-workflow.md](pr-workflow.md) for the full checklist.

## Permissions
- Read [permissions.md](permissions.md) for what commands you're allowed to run.

## Memory
- Read [memory.md](memory.md) for how to handle "remember" requests.
```

The links point to the satellite files in the `claude/` directory. Claude reads them on demand — it doesn't load all of them at startup, only when the linked topic is relevant to its current task.

## The Satellite Files

Each satellite file covers exactly one concern. This matters because context window space is finite. If Claude needs to create a PR, it reads `pr-workflow.md`. If it needs to deploy, it reads `build-commands.md`. It doesn't need to wade through CDK patterns to find the commit message format.

### repo-structure.md

Maps the 11 repos: what language, what runtime, what each one does. Includes the standard directory layout for service repos (`src/`, `cdk/`, `tests/`, `.github/workflows/`). This is the file Claude reads first when asked to work across repos — it answers "where does this code live?"

### build-commands.md

The exact commands for each repo type. .NET services get `dotnet build/test/publish`. TypeScript CDK gets `npm install && npx cdk deploy`. Flutter gets `flutter build web --release`. No ambiguity, no guessing.

### cdk-patterns.md

This one grew the most over the project. It documents specific CDK gotchas that Claude kept hitting:

- **Cross-stack imports**: The `Fn.Split` on `Fn.ImportValue` tokens requires `Fn.Select` for individual elements
- **ALB security groups**: CDK defaults `allowAllOutbound: false`, which silently breaks health checks for cross-app Fargate targets
- **S3 BucketDeployment**: Multiple deployments to the same bucket delete each other's files unless you set `prune: false`
- **Docker builds**: Must have `.dockerignore` excluding `cdk/` to prevent recursive `cdk.out/` copy

Every entry in this file represents a debugging session that took 30+ minutes. Writing it down means Claude (and we) don't repeat it.

### debugging.md

Known issues and their fixes. OIDC token expiry during long CDK deploys. CloudFormation stack states that block redeployment. NuGet authentication configuration for GitHub Packages. Short entries, each one a specific problem with a specific solution.

### permissions.md

What Claude can do without asking, and what requires confirmation. Git operations, dotnet commands, npm installs — all allowed. Deleting files, force-pushing, `cdk destroy` — must ask first. This file exists because Claude's default behavior is to ask permission for everything, which slows down routine work. Explicit permissions let it move fast on safe operations while still pausing for dangerous ones.

### pr-workflow.md

Every PR in this project requires three artifacts:

1. **The PR itself** via `gh pr create`
2. **A review comment** covering reasoning, concerns, and alternatives considered
3. **A session stats comment** with token usage, wait times, and interaction quality

The stats comment exists because we're studying the human-AI collaboration process itself. Losing that data means losing insight. The "never defer" requirement in this file exists because early in the project, stats comments were forgotten when deferred to "after the PR is created."

### memory.md

A versioned scratchpad for things Claude should remember across sessions. When asked to "remember" something globally, it goes here as a committed change via PR. When asked to remember something repo-specific, it goes in that repo's own `CLAUDE.md`.

Current global memories include things like "never add code to the Documentation repo" (use OperationalTools instead) and "AWS SDK v4 requires `AWS_REGION`, not `AWS_DEFAULT_REGION`."

## Repo-Specific Overrides

Some repos have workflows different enough to warrant their own `CLAUDE.md`. These complement the shared rules — they don't replace them.

**The Blog** has authoring workflow rules: the user suggests topics, Claude writes the post, the user fact-checks. It also has content guidelines (categories, tags, front matter format) and the explicit rule that this is a commercial project, not open source. That rule exists because Claude once described the project as open source in a blog post — a plausible-sounding assumption that was wrong.

**OperationalTools** has a full CLI command reference. Every `dotnet run` invocation with its flags and examples. This repo's CLAUDE.md is essentially a man page because the most common task is "run this ops command" and getting the flags wrong wastes time.

The other 9 repos have no `CLAUDE.md` of their own. They inherit everything from the shared root and satellite files, which is sufficient for standard .NET/Flutter/CDK development.

## Why This Works

**Single source of truth.** The files live in the Documentation repo under git control, symlinked to the workspace root. When a rule changes, we update one file via PR. All repos pick it up immediately through the symlink — no synchronization, no copying.

**Lazy loading.** Claude doesn't read all satellite files upfront. It reads the root `CLAUDE.md` (which is short) and follows links only when relevant. This preserves context window space for actual code.

**Separation of concerns.** A developer working on CDK doesn't need to scroll past Flutter build commands. A blog post doesn't need NuGet versioning rules. Each file is small enough to read in full and focused enough to be useful.

**Every rule has a story.** We don't add speculative rules. Every entry in every satellite file exists because something went wrong without it. The ALB outbound rule in `cdk-patterns.md` exists because health checks silently failed. The "never defer stats" rule in `pr-workflow.md` exists because stats were forgotten. This keeps the files lean — no hypothetical guidance, only battle-tested rules.

**It evolves through PRs.** Since the files live in the Documentation repo, changes go through the same PR process as any other code change. This creates a history of *why* rules were added, which is useful when reviewing whether a rule is still relevant.

## Why the Documentation Repo?

The `claude/` directory lives in the Documentation repository rather than in a standalone config repo or as loose files at the workspace root. This was a deliberate choice.

The Documentation repo is already the home for project-wide knowledge — design documents, game data definitions, and technical specs. The AI's operating rules are project-wide knowledge too. Putting them in the same repo means they go through the same PR review process, appear in the same commit history, and are maintained by the same workflow.

The symlink approach means the Documentation repo serves double duty: it's both the canonical source for the files (under git control, with PR history) and the provider of those files to every other workspace via the soft link. When Claude opens the Documentation repo directly, the `claude/` directory is right there. When Claude opens any other repo, the symlink at the workspace root resolves to the same files. One set of files, two access paths, zero duplication.

## What We'd Do Differently

The satellite file names are functional but not discoverable. If you don't read the root `CLAUDE.md` first, you wouldn't know `cdk-patterns.md` exists. This hasn't been a problem for Claude (it reads the root file), but it could confuse a human contributor looking at the directory.

## The Meta Point

The `claude/` directory is, in a sense, the project's institutional knowledge — the rules, patterns, and hard-won lessons that would normally live in a senior developer's head. Externalizing it into structured files means the AI assistant operates with the same constraints and knowledge every session, regardless of context window size or conversation history.

It's also a living document. Nine days in, we have 7 satellite files with dozens of specific rules. Each one represents something we learned. The structure exists to make that knowledge accessible without making it overwhelming.
