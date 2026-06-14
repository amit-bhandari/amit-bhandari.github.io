# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a personal blog built with **Hugo** (v0.143.1) using the **PaperMod** theme. It's hosted on GitHub Pages at `https://amit-bhandari.github.io/` and deployed automatically via GitHub Actions when pushing to the `main` branch.

## Directory Structure

- **`hugo.yaml`** — Site configuration: base URL, theme, navigation menu, social links, Google Analytics, syntax highlighting settings
- **`content/posts/`** — Blog posts in Markdown; each post has TOML front matter with title, date, tags, description, and optional `metaImage`
- **`content/images/`** — WebP images grouped by post folder (e.g., `jetpack-compose-custom-view/`, `wave-progress-bar-compose/`)
- **`content/privacy/`** — Privacy policy pages
- **`layouts/`** — Custom HTML templates that override PaperMod defaults:
  - `_default/list.html`, `single.html` — Page templates
  - `partials/` — Reusable template components (head, header, OpenGraph, Twitter Cards, schema JSON)
  - `shortcodes/newtabref.html` — Custom shortcode for links opening in new tab
- **`themes/PaperMod/`** — Git submodule for the PaperMod theme; do NOT edit directly
- **`public/`** — Build output (ignored in git, generated during build)
- **`static/`** — Favicon and web manifest files

## Common Commands

### Local Development
```bash
# Start the Hugo development server with live reload
hugo server

# Build the site to public/ directory
hugo

# Build with production settings (minification, garbage collection)
hugo --gc --minify
```

### Theme Management
Since PaperMod is a git submodule, you must initialize and update it:
```bash
# After cloning or if submodule is missing
git submodule init
git submodule update

# To pull the latest PaperMod updates
git submodule update --remote themes/PaperMod
```

### Creating New Posts
1. Create a new `.md` file in `content/posts/`
2. Add TOML front matter with required fields:
   ```toml
   +++
   title = 'Post Title'
   date = 2025-06-13T10:00:00+05:30
   draft = false
   description = "Brief description for metadata"
   tags = ['Tag1', 'Tag2']
   metaImage = "/images/folder-name/image.webp"  # Optional
   +++
   ```
3. Write content in Markdown below the front matter
4. Place images in `content/images/[post-folder]/` with WebP format

## Build & Deployment

The site automatically builds and deploys to GitHub Pages when you push to `main`. The GitHub Actions workflow (`hugo.yaml`):
- Installs Hugo CLI and Dart Sass
- Checks out code with git submodules (critical for theme)
- Builds Hugo with `--gc --minify` flags for production
- Deploys the `public/` directory to GitHub Pages

**Note:** The workflow sets `TZ: America/Los_Angeles` and `HUGO_ENVIRONMENT: production`. Local builds won't have these defaults.

## Key Configuration Details

- **`hugo.yaml` highlights:**
  - `baseURL: https://amit-bhandari.github.io/` — Must match your GitHub Pages domain
  - `theme: PaperMod` — References the submodule in `themes/`
  - `mainsections: ["posts", "papermod"]` — Controls which sections appear on homepage
  - `params.ShowReadingTime`, `ShowToc`, `ShowCodeCopyButtons` — Enabled features
  - Social icons configured for GitHub, X, StackOverflow, LinkedIn
  - Google Analytics tracking enabled with site verification tag

- **Custom shortcode:** `{{< newtabref href="url" title="text" >}}` opens links in new tabs (defined in `layouts/shortcodes/newtabref.html`)

## Testing Before Publish

1. Run `hugo server` and preview at `http://localhost:1313`
2. Verify post metadata renders correctly in OpenGraph/Twitter Cards
3. Check that custom layouts apply properly
4. Ensure images load with correct paths
5. Test archive and tag pages work as expected

## Git Workflow

- Always work on the `main` branch or create feature branches and PR before merging
- When pulling updates, ensure submodules are synced: `git submodule update`
- Avoid editing files in `themes/PaperMod/` directly; override via `layouts/` instead
- Images should be WebP format for performance
