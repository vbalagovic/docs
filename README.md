# Neural Draft docs site

The public documentation for the Neural Draft v1 Project API and MCP server.
Powered by [Mintlify](https://mintlify.com).

Live at: `docs.neuraldraft.io` (after deploy)

## Local development

You need Node 18+ and the Mintlify CLI.

```bash
# 1. Install the CLI globally (one-time)
npm i -g mintlify

# 2. From this directory
cd docs-site
mintlify dev
```

The dev server runs at `http://localhost:3000` and live-reloads on every save.

The OpenAPI spec is symlinked from the repo root (`../openapi.yaml`). Edits to
that file are reflected on the next page reload.

## Project layout

```
docs-site/
├── docs.json                 # Mintlify config (theme, navigation, navbar, footer, OpenAPI)
├── openapi.yaml              # symlink to /openapi.yaml at repo root
├── introduction.mdx          # landing page
├── quickstart.mdx            # 5-minute getting started
├── authentication.mdx        # API key, scopes, rotation
├── pricing-and-credits.mdx   # credit model
├── errors.mdx                # error catalog and retry guidance
├── rate-limits.mdx           # rate limit headers and backoff
├── webhooks.mdx              # event topics and signature verification
├── migration-from-saas.mdx   # for existing tenants
├── changelog.mdx             # release notes
├── mcp/                      # MCP server docs
│   ├── overview.mdx
│   ├── setup.mdx
│   └── tools-and-resources.mdx
├── pillars/                  # one page per pillar
│   ├── cms.mdx
│   ├── blog.mdx
│   ├── social.mdx
│   ├── booking.mdx
│   └── commerce.mdx
├── snippets/                 # framework-specific integrations
│   ├── nextjs.mdx
│   ├── astro.mdx
│   ├── nuxt.mdx
│   ├── vanilla.mdx
│   ├── lovable.mdx           # system prompt for Lovable / v0 / Bolt
│   └── cursor.mdx            # rules templates for Cursor / Claude Code
├── logo/
│   ├── light.svg             # light-mode wordmark
│   └── dark.svg              # dark-mode wordmark
├── favicon.svg
├── images/                   # hero, OG, diagrams (placeholders for now)
├── .github/workflows/
│   └── sync-openapi.yml      # CI to keep openapi.yaml in sync
└── README.md
```

## Editing content

Each `.mdx` file uses Mintlify's component set: `Card`, `CardGroup`, `Steps`,
`AccordionGroup`, `Accordion`, `Tabs`, `CodeGroup`, `Note`, `Tip`, `Warning`,
`Info`. The full reference is at https://mintlify.com/docs.

Frontmatter on every page:

```mdx
---
title: "Page title"
description: "One-line meta description (used for SEO and og:description)."
---
```

Optional: `mode: "wide"`, `sidebarTitle`, `icon`.

## Editing navigation

`docs.json` → `navigation.tabs` controls the left sidebar groups and the top
tabs. Mintlify validates this on every save; check the dev server output for
errors.

## Editing the API reference

Don't edit it directly — it auto-renders from `openapi.yaml`. To change a
request/response shape, header, example, or a tag description, edit the spec
and the reference rebuilds automatically.

The spec lives at the **repo root** (`../openapi.yaml`), and the docs site
symlinks it. CI keeps the symlink resolvable on every push (see below).

## CI

`.github/workflows/sync-openapi.yml` runs on every push to `main` /
`production` that touches `openapi.yaml`, the v1 routes, or the v1
controllers. It:

1. Lints the spec with `@redocly/cli`.
2. Re-creates the symlink (`docs-site/openapi.yaml -> ../openapi.yaml`).
3. Commits if the symlink target moved.

Mintlify watches the docs branch and re-deploys automatically.

## Deploying

This repo is set up to be hosted on Mintlify's free Hobby tier:

1. Push this directory to the GitHub repository connected to your Mintlify
   workspace (we'll likely extract this directory to a separate
   `neural-draft-docs` repo before launch — Mintlify only watches one
   subdirectory or repo at a time).
2. In the Mintlify dashboard, click **New Deployment** → connect the repo →
   point at `docs-site/` (or the repo root if extracted).
3. Mintlify gives you a `*.mintlify.app` preview URL within ~60 seconds.
4. Add `docs.neuraldraft.io` as a custom domain (free on Hobby). DNS:
   `CNAME docs cname.vercel-dns.com` per Mintlify's instructions.

## Brand colors

The Mintlify theme reads three colors from `docs.json`:

| Token   | Value     | Usage                                 |
| ------- | --------- | ------------------------------------- |
| primary | `#7C3AED` | Primary CTAs, accent links            |
| light   | `#A78BFA` | Hover, light-mode highlights          |
| dark    | `#5B21B6` | Dark-mode emphasis, focus rings       |

Match the marketing site's purple gradient. If those change, update
`docs.json` and the logos in `logo/`.

## Free-tier constraints

We use Mintlify's free **Hobby** plan. That gives us:

- Custom domain (`docs.neuraldraft.io`)
- MDX + custom components
- API playground
- MCP server endpoint
- Mintlify-built search

It does **not** give us:

- Multi-seat editing (1 editor seat on Hobby)
- AI assistant ("Ask AI")
- Preview deploys per PR
- Password-protected staging
- Per-page analytics

If/when we want any of those, upgrading to Pro is one click.

## Open questions

- Logos here are simple placeholders (purple square + Inter wordmark). Swap
  for the marketing-site logos once finalised.
- `images/` is a placeholder directory; add hero/OG images before launch.
- The CI workflow assumes `docs-site/` lives in the same repo as
  `openapi.yaml`. If we extract to `neural-draft-docs`, switch the workflow
  to copy via a deploy key instead of a symlink.
