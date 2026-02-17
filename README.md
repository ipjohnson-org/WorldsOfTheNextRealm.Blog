# WorldsOfTheNextRealm.Blog

Development blog for [Worlds of the Next Realm](https://github.com/ipjohnson-org) — documenting the AI-assisted game development journey.

Built with [Hugo](https://gohugo.io/) and the [Stack](https://github.com/CaiJimmy/hugo-theme-stack) theme. Deployed via GitHub Pages.

## Local Development

```bash
# Start dev server with drafts
hugo server --buildDrafts

# Production build
hugo --gc --minify
```

## Deployment

Merging to `main` triggers the GitHub Actions workflow which builds and deploys to GitHub Pages.

**Live site:** https://ipjohnson-org.github.io/WorldsOfTheNextRealm.Blog/
