---
title: About
description: About Worlds of the Next Realm and the team behind it.
date: 2026-02-17
menu:
    main:
        weight: -90
        params:
            icon: user
---

## The Game

**Worlds of the Next Realm** is a 2.5D resource gathering and city-building game where players construct cities, build armies, explore lands, gather resources, and battle monsters — all with the help of an AI companion that grows alongside them.

Players start with a single city and progressively expand their influence across multiple worlds, each with unique resources, challenges, and opportunities. The game features dynamic AI-driven world events, cooperative guild gameplay, and deep progression systems including artifacts, gems, spells, and interconnected upgrade paths.

## The Team

This is a family development project. The engineering is led by a senior software engineer and manager, with family members contributing to game assets and storyline. It's a passion project built in the open — and you're reading the dev blog that documents every step.

## The Tech Stack

- **Backend:** .NET 8, ASP.NET Core, AWS Lambda, Fargate
- **Frontend:** Flutter (iOS, Android, Web), Riverpod, Flame engine
- **Infrastructure:** AWS CDK (TypeScript + .NET), DynamoDB, API Gateway
- **CI/CD:** GitHub Actions with AWS OIDC
- **Shared Libraries:** NuGet packages via GitHub Packages
- **AI Tools:** Claude Code for AI-assisted development

## The Unique Angle

This entire project is being built with significant AI assistance. Claude (Anthropic's AI) is a core member of the development team — writing code, reviewing PRs, authoring this blog, and helping with architecture decisions. We document what works, what doesn't, and what we learn along the way.

## Repositories

The project is split across multiple repositories under [ipjohnson-org](https://github.com/ipjohnson-org):

| Repo | Purpose |
|------|---------|
| BackendApi | Main game API (Lambda) |
| AuthenticationService | JWT auth service |
| NotificationService | Push notifications (Fargate) |
| WorldSimulation | World simulation engine (Fargate) |
| FrontEndClient | Flutter web/mobile client |
| BackendCommon | Shared NuGet packages |
| Infra | Shared AWS infrastructure (CDK) |
| OperationalTools | CLI ops tooling |
| Documentation | Game design & technical docs |
| Assets | Pixel art game assets |
| **Blog** | This site |
