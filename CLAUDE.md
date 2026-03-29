# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Jekyll-based personal blog using the Hydejack v9 theme, hosted on GitHub Pages at https://xiaosean.github.io. Content is primarily technical blog posts about deep learning, computer vision, NLP, and software development, written in Chinese and English.

## Build & Development Commands

```bash
# Install dependencies
bundle install

# Local development server (with live reload)
bundle exec jekyll serve

# Build for production
bundle exec jekyll build
```

Output goes to `_site/`. The site is also configured for Netlify deployment (see `netlify.toml`).

## Content Authoring

### Blog Posts

Create files in `_posts/` with format `YYYY-MM-DD-title.md`. Required frontmatter:

```yaml
---
layout: post
title: Post Title
description: ~150 character description
categories: [CategoryName]
tags: [tag1, tag2]
comments: true
---
```

- `categories` must match a slug in `_featured_categories/` to appear under that category
- Permalink structure is `/:categories/:year-:month-:day-:title/`

### Categories and Tags

- Featured categories live in `_featured_categories/` with `layout: list` and a `slug` matching what posts reference
- Featured tags live in `_featured_tags/` with the same pattern

## Architecture

- **Theme**: Remote theme `hydecorp/hydejack@v9` — most layout/styling comes from the gem, not local files
- **Customization layers**: `_sass/my-style.scss` and `_sass/my-inline.scss` for CSS overrides; `_includes/my-head.html` and `_includes/my-body.html` for injecting HTML
- **Data files**: `_data/authors.yml` (author metadata), `_data/social.yml` (social links), `_data/variables.yml` (design tokens)
- **Math rendering**: KaTeX via `kramdown-math-katex`
- **Comments**: Disqus integration
- **Custom plugin**: `_plugins/jekyll-replace-imgs/` for image processing
