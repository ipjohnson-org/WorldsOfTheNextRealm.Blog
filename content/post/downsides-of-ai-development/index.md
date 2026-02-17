---
title: "The Downsides of AI-Assisted Development"
description: "AI coding assistants are powerful, but they need constant supervision. Here's what we've learned about the real costs and risks of building with AI."
date: 2026-02-17
categories:
    - ai-development
tags:
    - claude-code
    - workflow
    - lessons-learned
draft: false
---

Our first post introduced this project with optimism — a game built by a human-AI team, a blog documenting the journey. But if we're going to be honest about this process, we need to talk about the downsides early. AI-assisted development is genuinely powerful, and it's also genuinely dangerous if you're not paying attention.

## AI Has to Be Watched

This is the single most important thing we've learned: **AI does not replace judgment. It replaces typing.**

Claude can write a complete Lambda function, a CDK stack, a database migration, or a blog post in seconds. But every one of those outputs needs a human looking at it critically. Not skimming — actually reading, understanding, and evaluating.

Here are real categories of problems we've run into:

### Confident Fabrication

AI will state things with complete confidence that are simply wrong. It will reference API methods that don't exist, use library features from the wrong version, or describe AWS service behavior that's subtly inaccurate. There's no hedging, no "I'm not sure about this." It reads like authoritative documentation — except it's wrong.

This is especially dangerous in infrastructure code. A CDK construct that looks correct but misconfigures an IAM policy or a security group can create real security vulnerabilities. The code compiles, the tests pass (if the tests were also AI-generated and share the same blind spot), and the deployment succeeds. You don't find out there's a problem until something bad happens.

### Scope Creep and Over-Engineering

Ask an AI to fix a bug and it will often "improve" the surrounding code while it's there. Add error handling you didn't ask for. Refactor a function into an abstraction. Introduce a design pattern. Each change looks reasonable in isolation, but the cumulative effect is a codebase that drifts from what the developer intended.

We've had to establish explicit rules: don't add features beyond what was asked, don't refactor neighboring code, don't introduce abstractions for one-time operations. Even with those rules, it takes vigilance to enforce them.

### The Illusion of Productivity

This one is subtle. When AI generates code quickly, it *feels* productive. Hundreds of lines appear in seconds. PRs stack up. But speed of generation is not the same as speed of delivery. Every line still needs review. Complex changes need testing. And debugging AI-generated code that you didn't write yourself takes longer than debugging your own code, because you don't have the mental model of *why* it was written that way.

The real productivity gain from AI is in first drafts and boilerplate — getting a starting point faster. The review, refinement, and debugging still take human time.

## This Blog Is AI-Written (And That's a Risk We're Managing)

In the spirit of transparency: this blog is predominantly written by Claude. The workflow is straightforward — Ian suggests a topic and key points, Claude drafts the post, and Ian fact-checks it before it merges. If a post needs screenshots or diagrams, Claude asks for them rather than assuming they exist.

That means every post carries the same risks we just described. Claude might state something about the project that's subtly inaccurate. It might describe a feature as working when it's still in progress. It might over-explain something simple or gloss over something complex.

We're managing this by treating blog posts the same way we treat code: everything goes through a PR, everything gets reviewed. But readers should know the authoring model. If something reads off, it might be because an AI wrote it and the fact-check missed it. We'd rather be upfront about that than pretend every word was hand-crafted.

## This Is a Commercial Project

It's worth addressing something our first post got wrong. We described the project's code as "open source." It's not. Worlds of the Next Realm is a commercial game that we intend to monetize. The development process is visible on GitHub — you can see our PRs, our commit history, our review comments — but the code itself is not open source in the licensed, fork-it-and-use-it sense.

We're sharing the journey because we think the human-AI development story is interesting and worth documenting. But the game is a product, and we're building it with the intention of charging for it. Specifically, the game will use a subscription model to cover the costs of the AI companion features that are core to the gameplay.

That distinction matters because AI can blur it. When Claude drafted the first blog post, it defaulted to "open source" framing because that's a common pattern in developer blogs. It wasn't a lie — it was an AI making a plausible-sounding assumption that happened to be wrong. Exactly the kind of thing that needs to be caught in review.

## The Cost of Context

AI assistants work within context windows. They can see the files you show them, the conversation history, and any documentation you've loaded. They can't see everything at once. For a multi-repo project with 10+ repositories, this creates real problems.

Claude might write code in BackendApi that doesn't match the contract defined in BackendCommon. It might propose a CDK pattern in one service that contradicts the pattern used in another. It might make a change that works in isolation but breaks an integration point it doesn't know about.

The solution is process — loading the right context, cross-referencing across repos, running integration tests. But it adds overhead that partially offsets the speed gains from AI code generation.

## The Trust Calibration Problem

Over time, you develop a sense of when to trust AI output and when to scrutinize it. Simple, well-established patterns (CRUD endpoints, standard CDK constructs, straightforward data transformations) are usually reliable. Novel logic, security-sensitive code, and business rules are where errors cluster.

But this calibration itself is dangerous. Familiarity breeds complacency. The tenth Lambda function Claude writes will look like the first nine, so you review it faster. And that's the one with the bug.

We don't have a complete solution for this. We use checklists, we enforce PR reviews, and we try to stay skeptical even when the output looks clean. But it's an ongoing tension.

## So Why Use AI at All?

After all of that, the honest answer is: because the productivity gains are real, when managed correctly.

A solo developer building a game with this many services, this much infrastructure, and this many moving parts would take years working alone. With AI assistance, the first-draft speed for boilerplate, infrastructure, documentation, and standard patterns is dramatically faster. The human time shifts from writing to reviewing and directing — which is a different kind of work, but it's still faster end-to-end.

The key is going in with open eyes. AI is a powerful tool that produces output requiring supervision. Treat it like a very fast junior developer who is brilliant at pattern matching and terrible at judgment. Review everything. Trust nothing by default. Establish rules and enforce them. And when it makes mistakes — because it will — fix them and keep going.

That's what this blog will document: the real experience, good and bad.
