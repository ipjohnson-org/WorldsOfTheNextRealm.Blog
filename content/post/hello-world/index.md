---
title: "Hello World: Building a Game with AI"
description: "Introducing Worlds of the Next Realm — a city-building game developed by a human-AI team, and the blog that documents the journey."
date: 2026-02-17
categories:
    - ai-development
tags:
    - claude-code
    - dotnet
    - flutter
    - aws
    - cdk
draft: false
---

This is the first post on the Worlds of the Next Realm dev blog. We're here to document something a bit unusual: a full-scale game being built by a human developer working side-by-side with an AI coding partner.

## What Is Worlds of the Next Realm?

Worlds of the Next Realm is a 2.5D city-building and resource management game. Players construct cities, raise armies, explore lands, gather resources, and battle monsters — all with the help of an AI companion that grows alongside them.

The game features:
- **Multi-world expansion** — start with one city, grow across multiple distinct worlds
- **AI companions** — an in-game assistant that manages your empire while you're away
- **Cooperative guilds** — team up for raids, expeditions, and resource sharing
- **Dynamic world events** — AI-driven events that keep gameplay unpredictable

It's designed for players ages 8 and up, running on iOS, Android, and the web.

## The Tech Stack

This isn't a small project. Here's what we're working with:

| Layer | Technology |
|-------|-----------|
| Backend APIs | .NET 8, ASP.NET Core, AWS Lambda |
| World Simulation | .NET 8, AWS Fargate |
| Authentication | .NET 8, JWT, Argon2, Lambda |
| Notifications | .NET 8, Fargate |
| Frontend | Flutter, Riverpod, Flame engine |
| Infrastructure | AWS CDK (TypeScript + .NET) |
| Shared Libraries | NuGet packages via GitHub Packages |
| CI/CD | GitHub Actions with AWS OIDC |
| Database | DynamoDB |

The project is split across 10+ repositories, each with its own CI/CD pipeline. Every service deploys independently. Infrastructure is managed entirely through CDK — no console clicks allowed.

## The AI Development Angle

Here's what makes this project different, and why this blog exists.

A significant portion of the code, infrastructure, documentation, and even this blog post is written with the help of Claude Code — Anthropic's AI coding assistant. The human developer (Ian) provides direction, reviews everything, and makes the final calls. Claude writes code, reviews PRs, proposes architecture, debugs issues, and authors content.

We follow a strict PR-based workflow. Every change — whether written by a human or an AI — goes through a feature branch, gets a pull request, receives a review comment, and is tracked with session statistics. Nothing lands on `main` without review.

This blog will be honest about the experience:
- What kinds of tasks AI handles well
- Where human judgment is essential
- How we structure the collaboration
- The real productivity impact (with data)
- Mistakes and lessons learned

## What to Expect

We'll be publishing posts across several categories:

- **Architecture** — design decisions and trade-offs
- **Infrastructure** — CDK patterns, AWS services, deployment
- **Backend** — .NET services, API design, game logic
- **Frontend** — Flutter UI, game rendering with Flame
- **Game Design** — mechanics, balancing, player progression
- **AI Development** — the human + AI workflow itself
- **DevOps** — CI/CD, tooling, operational concerns

Posts will typically be tied to real work — when we ship a feature, solve a hard problem, or learn something worth sharing, we'll write about it.

## The Meta Layer

There's an intentional meta quality to this project. The game itself features AI companions that help players manage their empires. And the game is being built by a human-AI team. We're living the thing we're building.

That parallel isn't accidental. Working with AI tools every day gives us direct insight into what makes AI assistance useful versus frustrating — insights that feed directly into the game's AI companion design.

## Follow Along

All of our code is open source under the [ipjohnson-org](https://github.com/ipjohnson-org) GitHub organization. You can see every PR, every commit, every review comment.

This blog is itself a GitHub Pages site, built with Hugo and the Stack theme, deployed via GitHub Actions. The source is in the [WorldsOfTheNextRealm.Blog](https://github.com/ipjohnson-org/WorldsOfTheNextRealm.Blog) repo. Even the blog posts go through PRs.

Welcome to the journey. Let's build something.
