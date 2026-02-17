# WorldsOfTheNextRealm.Blog

Hugo static site deployed via GitHub Pages. Part of the WorldsOfTheNextRealm multi-repo project.

## Core Rules
- **Always** create feature branches and PRs. Never push directly to main.
- **Always** read and understand existing content before modifying it.
- **Always** ask before deleting files or force-pushing.
- **Never** look in or modify files/directories above the root directory.

## Project Context
- **This is a commercial project, not open source.** The game will be monetized. Never describe the project or its code as "open source" in blog posts or documentation.
- The blog itself is public, but it documents a commercial game development effort.

## Blog Authoring Workflow
The blog follows a human-directed, AI-authored workflow:
1. **The user suggests a topic** — they provide the subject, key points, and any specific angles to cover.
2. **Claude writes the post** — drafting the full content based on the user's direction and knowledge of the project.
3. **The user fact-checks** — the user reviews the post for accuracy before it merges. Claude should write honestly and flag anything it's uncertain about rather than fabricating details.
4. **Screenshots** — if a post would benefit from screenshots, diagrams, or images, Claude must **ask the user to provide them** rather than assuming they exist. Never reference images that haven't been provided.
5. **Claude handles the PR workflow** — commit, push, create PR, review comment, and stats comment.

## Content Authoring

### Creating a New Post
1. Create a directory under `content/post/<slug>/`
2. Add an `index.md` file with proper front matter
3. Place any images for the post in the same directory

### Front Matter Template
```yaml
---
title: "Post Title Here"
description: "A brief description for SEO and previews."
date: YYYY-MM-DD
categories:
    - category-name
tags:
    - tag1
    - tag2
draft: false
---
```

### Categories (use exactly these names)
- `architecture` — design decisions, HLD/LLD discussions
- `infrastructure` — CDK, AWS, deployment
- `backend` — .NET services, APIs, game logic
- `frontend` — Flutter client, UI/UX
- `game-design` — gameplay mechanics, balancing
- `ai-development` — human + AI collaboration, Claude Code usage
- `devops` — CI/CD, operational tooling

### Tags
Use lowercase, hyphenated tags. Examples: `dotnet`, `cdk`, `flutter`, `dynamodb`, `lambda`, `claude-code`, `github-actions`, `nuget`, `fargate`, `jwt`, `riverpod`

### Writing Style
- Write in first person plural ("we") to reflect the human + AI team
- Be honest about what worked and what didn't
- Include code snippets where relevant (use fenced code blocks with language)
- Link to relevant PRs and commits when discussing specific changes
- Keep posts focused — one topic per post

## Building Locally
```bash
hugo server --buildDrafts    # Dev server with drafts
hugo server                  # Dev server without drafts
hugo --gc --minify           # Production build
```

## Deployment
- Merging to `main` triggers GitHub Actions → builds Hugo → deploys to GitHub Pages
- The site is served at: https://ipjohnson-org.github.io/WorldsOfTheNextRealm.Blog/

## Before Creating a PR
Follow the full PR workflow: create PR, review comment, and stats comment.
