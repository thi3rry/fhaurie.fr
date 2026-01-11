# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Professional portfolio website for François Haurie, an Agent de Protection Rapprochée International (close protection specialist) based in Biarritz. Built with Hugo SSG using the Terminal theme, hosted on GitHub Pages.

## Development Commands

**Local Development:**
```bash
hugo serve                    # Start development server with hot reload at http://localhost:1313
```

**Production Build:**
```bash
hugo --gc --minify --cacheDir # Build with garbage collection and minification
```

**Module Management:**
```bash
hugo mod get -u              # Update Hugo modules (theme)
hugo mod tidy                # Clean up module dependencies
```

## Hugo Configuration

**Key Settings (hugo.toml):**
- Site: `https://fhaurie.fr/`
- Language: French (fr-FR)
- Theme: Terminal v4 (imported as Hugo module via `github.com/panr/hugo-theme-terminal/v4`)
- Content displayed in center with limited width (`centerTheme = true`, `fullWidthTheme = false`)

**Menu System:**
The main navigation is configured in `hugo.toml` under `[[languages.fr.menu.main]]` and links directly to PDF documents and certification images in `/static/docs/`. Each menu item has a `weight` property for ordering.

## Architecture

### Content Structure

- **Homepage** (`content/_index.md`): Professional profile, expertise areas, contact information
- **Documents** (`content/docs/_index.md`): List of certifications and downloadable documents
- **Gallery** (`content/gallerie-photo.md`): Photo gallery using custom shortcode

Content files use TOML front matter (`+++` delimiters).

### Custom Layouts

**Partials** (`layouts/partials/`):
- `footer.html`: Displays professional card number (064-2030-12-30-20250990473) and copyright
- `header.html`: Logo, mobile menu toggle, language selector
- `menu.html`: Desktop navigation with nested menu support and configurable item limit
- `mobile-menu.html`: Responsive mobile navigation

**Shortcodes** (`layouts/shortcodes/`):
- `gallery.html`: Responsive photo grid with hover zoom effect. Usage: `{{< gallery images="/path1,/path2,/path3" >}}`
  - Grid layout: auto-fill with minmax(250px, 1fr)
  - Mobile: minmax(150px, 1fr)
  - Includes inline CSS for styling
- `links.html`: Renders links from front matter (currently unused)

### Static Assets

- `/static/docs/`: PDF certifications and credential images (~1.4 MB)
- `/static/gallerie-photos/`: Professional portfolio photos (~2.5 MB)
- `/static/style.css`: Custom CSS overrides for Terminal theme (defines `--accent: #b9975c` gold color)

### Theme Integration

The Terminal theme is loaded as a Hugo module (not a git submodule). The theme provides the base layout, and custom partials in `layouts/partials/` override the theme's defaults. The commented-out `replacements` line in `hugo.toml` shows the alternative local theme approach.

## Deployment

**Automated via GitHub Actions** (`.github/workflows/hugo.yaml`):
1. Triggered on push to `main` branch or manual dispatch
2. Build environment: Ubuntu latest with specific versions:
   - Hugo: 0.154.2 (extended)
   - Go: 1.25.5
   - Dart Sass: 1.97.1
   - Node.js: 24.12.0
3. Checks out code with submodules recursively
4. Restores Hugo build cache from previous runs
5. Builds with `hugo --gc --minify --cacheDir`
6. Deploys to GitHub Pages via `actions/deploy-pages@v4`

**Important:** Git is configured with `core.quotepath false` to properly handle French accented filenames in static assets.

## Common Issues

**Gallery Shortcode:**
- Ensure `layouts/shortcodes/gallery.html` exists before using `{{< gallery >}}` in content
- Images parameter must be comma-separated absolute paths starting with `/`
- Files must exist in `/static/` directory (Hugo serves them from root)

**Menu Links:**
- Menu URLs in `hugo.toml` must use URL encoding for spaces (`%20`)
- Files referenced in menu must exist in `/static/docs/` directory
- Menu items are ordered by `weight` property (lower numbers appear first)

**Content Files:**
- Use TOML front matter with `+++` delimiters (not YAML `---`)
- Content pages need `draft = false` in front matter to be published
- Page titles come from `title` parameter in front matter, not from `# Heading`

## File Naming Conventions

- Content files: Lowercase with hyphens (e.g., `gallerie-photos.md`)
- Static assets: May contain spaces and French characters (properly encoded in URLs)
- Layout files: Lowercase with hyphens (e.g., `mobile-menu.html`)
