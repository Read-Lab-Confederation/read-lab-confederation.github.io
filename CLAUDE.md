# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Hugo-based static website for EMERGENT (EMory bactERial GEnomics aNd meTagenomics), a research group at Emory University. The site uses the Ananke theme and is automatically deployed to GitHub Pages via GitHub Actions.

## Key Architecture Concepts

### Dual Content System for Group Members

Group member profiles use a **two-file system**:

1. **JSON data file** (`data/group/${firstname}-${lastname}.json`): Contains structured metadata
   - Display name, position, weight (for sorting)
   - Social links (github, google_scholar, ncbi_bibliography, orcid, twitter, bluesky, website)
   - Template available at `data/group/template.json`

2. **Markdown content file** (`content/group/${firstname}-${lastname}.md`): Contains biographical text
   - YAML frontmatter with author and date
   - Biography content in markdown

3. **Image file** (`static/images/group/${firstname}-${lastname}.jpg` or `.png`): Profile photo

The Hugo templates automatically link these together using the `${firstname}-${lastname}` slug.

### Custom Hugo Shortcodes

The site uses a multi-function shortcode system via `layouts/shortcodes/group.html`:

- `{{< group "group_picture" "/images/path.jpg" "Caption text" >}}` - Display group photos with captions
- `{{< group "current_members" >}}` - Auto-generates grid of current members from `data/group/*.json`, sorted by `weight` field

### Group Member Display Flow

1. Main group listing at `/group/` renders from `content/group.md` using `layouts/group/list.html`
2. Individual member pages at `/group/${firstname}-${lastname}/` render using `layouts/group/single.html` which includes `layouts/partials/group-partial.html`
3. The partial reads BOTH the JSON data file AND the markdown content file to create the complete profile
4. Member photos are looked up in order: `.jpg` → `.png` → `blank.png` fallback

### Social Sharing System

Share buttons are implemented in two locations:
- `layouts/partials/share-buttons-for-posts.html` - Blog post sharing
- `layouts/partials/site-footer.html` - Site-wide footer sharing

Both use custom SVG icons and the `static/css/share-button.css` for styling.

## Build and Deployment

**Local development:**
```bash
hugo server  # Run local dev server with live reload
```

**Build:**
```bash
hugo --minify  # Generate static site in public/ directory
```

**Deployment:** Automatic via GitHub Actions on push to `master` branch. See `.github/workflows/` for configuration. Uses Hugo version 0.79.1 (extended).

## Common Tasks

### Adding a New Group Member

1. Create JSON file: `data/group/${firstname}-${lastname}.json` (use `template.json` as base)
   - Set `weight` field to control display order (lower numbers appear first)
   - Add social links including new `bluesky` field if applicable
2. Create markdown file: `content/group/${firstname}-${lastname}.md`
3. Add profile image: `static/images/group/${firstname}-${lastname}.jpg` or `.png`

### Updating Group Photos

Main group photo and historical photos are managed in `content/group.md`. Images should be placed in `static/images/` with naming convention `Group-YYYY-MM-DD.jpg` or `Group-YYYY.jpg`.

### Navigation Menu

Configured in `config.toml` under `[[menu.main]]` sections. Social icons in header controlled by `[params]` section (twitter, facebook, github, etc.). Empty string values hide the icon.

## Important Files

- `config.toml` - Site configuration, navigation menu, social links, theme settings
- `content/group.md` - Group page content including current/historical group photos
- `layouts/partials/group-partial.html` - Individual member profile display logic
- `layouts/shortcodes/group.html` - Multi-function shortcode for group photos and member grids
- `static/css/share-button.css` - Social sharing button styles (includes custom bluesky styling)
