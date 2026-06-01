# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A personal tech blog (Korean) built on **Gatsby v2**, forked from `gatsby-starter-hoodie`. Deploys to GitHub Pages as a user page (`logantect.github.io`). Content is Markdown; the theme is React + styled-components.

## Commands

```bash
npm run develop        # local dev server at http://localhost:8000 (also `npm start`)
npm run build          # production build into public/
npm run serve          # serve the built public/ dir
npm run clean          # clear Gatsby cache + public/ (run this first when builds act stale)
npm run check:lint     # eslint . --ext .js,.jsx   (CI gate)
npm run fix:lint       # eslint --fix
npm run check:prettier # prettier -c .              (CI gate)
npm run fix:prettier   # prettier --write .
npm run deploy-gh      # gatsby build && gh-pages -d public  (publish to gh-pages branch)
```

There are **no unit tests** — `npm test` is a placeholder that intentionally exits 1. Don't treat it as a gate.

### Node version caveat

CI (`.github/workflows/ci.yml`) pins **Node 14**, and Gatsby v2 + `sharp@0.23` are built for that era. A modern local Node (v18+) will likely fail `npm install`/`build` with native-module errors. Use Node 14 (e.g. `nvm use 14`) when reproducing CI or debugging build failures. CI runs on PRs to `master`/`develop`, but the working branch here is `main` — pushes to `main` do not trigger the existing workflow.

## Content model — how a Markdown file becomes a page

This is the core thing to understand before touching content or `gatsby-node.js`.

1. **Only `contents/posts/` is sourced.** `gatsby-config.js` points `gatsby-source-filesystem` at `contents/posts` *only*. Files under `contents/TIL/` and `contents/_posts/` exist in the repo but are **not** picked up by Gatsby — they won't appear on the site. To publish, a post must live under `contents/posts/`.

2. **Slug = the post's own folder name, not its path.** In `gatsby-node.js`, `onCreateNode` computes the slug as `/${slug.split("/").reverse()[1]}/` — i.e. the immediate parent directory of the Markdown file. So `contents/posts/java/java-thread-1-executor-framework/index.md` becomes URL `/java-thread-1-executor-framework/`. The category folder (`java/`) is purely for repo organization and is invisible in the URL. **Folder names must therefore be globally unique** across all categories, or pages collide.

3. **Each post is a folder with `index.md` + co-located images.** `gatsby-remark-images` / `gatsby-remark-static-images` resolve relative image paths from that folder.

### Frontmatter schema

Enforced by `createSchemaCustomization` in `gatsby-node.js`:

```yaml
---
title: "..."        # String!  required
description:        # String   optional
date: 2022-07-29 20:00:00
tags:               # [String!]! required (drives /tags and SideTagList)
  - git
series: "..."       # String   optional
---
```

A non-empty `series` value auto-generates a series page at `/series/<series-name-with-spaces-as-dashes>` (see `createPages` + `src/templates/Series.jsx`). Posts in the same series are linked via prev/next navigation, ordered by `date` ASC.

## Code structure

- `gatsby-node.js` — page generation (posts + series pages), slug derivation, frontmatter type enforcement. **The file to read first** for any routing/content behavior.
- `gatsby-config.js` — plugin pipeline. The remark chain order matters: prismjs (syntax highlight), katex (math), mermaid (diagrams), images. Google Fonts + manifest configured here.
- `blog-config.js` — site-level identity (title, author, social links, utterances comment repo). Edit here to change site metadata, not `gatsby-config.js` directly (it imports from this).
- `src/templates/` — `Post.jsx`, `Series.jsx` (rendered by `gatsby-node.js`).
- `src/pages/` — static routes: `index.jsx` (post list), `tags.jsx`, `series.jsx`, `search.jsx`, `404.jsx`.
- `src/components/` — theme UI. `Article/` (Header/Body/Footer/Series, with Body/Toc + StyledMarkdown) is where post rendering lives.
- `src/reducers/` — Redux store (`createStore`), wired via `gatsby-plugin-react-redux` for SSR-safe state (theme toggle, etc.).
- `src/assets/theme/` — design tokens.

## Conventions

- JSX components use `.jsx`; Gatsby/config files use `.js`. ESLint extends `react-app`; Prettier config in `.prettierrc`. Run `fix:prettier` + `fix:lint` before committing — both are CI gates.
- Commit messages follow `type(scope): subject` in Korean (e.g. `docs(dns): ...`, `add: ...`).
