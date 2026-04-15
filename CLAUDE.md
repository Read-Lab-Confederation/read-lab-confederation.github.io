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

### Adding News Posts

The News section provides short updates and announcements with automatic RSS feed generation.

1. Create a new markdown file in `content/news/` with naming convention: `YYYY-MM-DD-title.md`
2. Include YAML frontmatter with title, date, and draft status:
   ```yaml
   ---
   title: "Your News Title"
   date: 2026-01-08
   draft: false
   ---
   ```
3. Add your news content in markdown format below the frontmatter

**RSS Feed:** Automatically available at `/news/index.xml`

News posts appear in reverse chronological order on the `/news/` page.

**Publication cross-listing rule:** When a news item mentions a new publication, always look up the full citation (all authors, journal, year, DOI) and add it to the "Recent Publications" section in `content/research.md` under the appropriate year heading. Create a new year heading if needed. Use the same citation format as existing entries.

**PubMed update rule:** Any time the news section is updated (new post added or existing post edited), search PubMed for recent publications by lab members and update the "Recent Publications" section in `content/research.md` with any new entries not already listed. Use the NCBI E-utilities API (e.g., `https://eutils.ncbi.nlm.nih.gov/entrez/eutils/esearch.fcgi?db=pubmed&term=Read+TM[Author]&retmax=10&sort=date`) or search PubMed directly to find new publications. Add any missing entries under the appropriate year heading using the same citation format as existing entries.

**People linking rule:** When mentioning a person by name in a news item, link their name if possible:
- Current members (those with a file in `content/group/`): link to `/group/${firstname}-${lastname}/`
- Former members (listed in `content/group.md` under "### Former Members"): link to their external profile (see Former Members linking rule below), or fall back to `/group/` if no profile is found

**Former Members linking rule:** When adding or updating a former member entry in `content/group.md`, search for their profile in this priority order — stop as soon as one is found:
1. LinkedIn
2. ORCID (search `site:orcid.org "Firstname Lastname"`)
3. Google Scholar (search `site:scholar.google.com "Firstname Lastname"`)
4. GitHub
5. Personal or lab webpage

Only link if confident the profile belongs to the correct person. If none found, leave the name unlinked. Apply this rule when adding any new former member and when updating existing entries.

**Typo checking rule:** After creating or substantially editing any text content (blog posts, news items, group member bios), proofread the text for typos and grammatical errors. Fix clear errors silently. For scientific/technical terms (species names, strain IDs, kit names, numerical measurements, software tool names), flag them to the user for verification rather than correcting them. Report all fixes made and all terms flagged in a single summary.

## File and Image Naming Schema

All filenames must be **lowercase kebab-case** (hyphens only, no underscores, no mixed case). File extensions must be lowercase (`.jpg` not `.JPG`, `.png` not `.PNG`).

**New content rule:** Any new content must follow this schema. When adding any file, verify it conforms before saving.

### Content files

| Type | Pattern | Example |
|---|---|---|
| News post | `content/news/YYYY-MM-DD-kebab-slug.md` | `2026-04-02-tim-read-senior-editor.md` |
| Blog post | `content/blog/posts/YYYY-MM-DD-kebab-slug.md` | `2024-02-09-alex-bog-mrsa-hyperglycemic.md` |
| Group member markdown | `content/group/firstname-lastname.md` | `tim-read.md` |
| Group member JSON | `data/group/firstname-lastname.json` | `tim-read.json` |

### Images

| Type | Path | Example |
|---|---|---|
| Blog post images | `static/images/posts/YYYY-MM-DD-post-slug/description.png` | `posts/2024-02-09-alex-bog-mrsa-hyperglycemic/fig-1-tree.png` |
| News images (if needed) | `static/images/news/YYYY-MM-DD-news-slug/description.png` | `news/2026-04-02-senior-editor/photo.jpg` |
| Group member photo | `static/images/group/firstname-lastname.jpg` | `group/tim-read.jpg` |
| Group photo | `static/images/group/group-YYYY-MM-DD.jpg` | `group/group-2025-12-01.jpg` |
| Lab Pictures gallery image | `static/images/group/group-YYYY-MM-DD.jpg` (same location as group photos) | `group/group-2025-12-01.jpg` |

**Image naming within subdirectories:** Use `fig-N-description.png` for paper/data figures, `image-N.jpg` for generic photos. Keep names short and descriptive.

### Lab Pictures page

`content/lab-pictures.md` is a gallery of group photos and other images not attached to a specific blog/news post. It uses the `{{< gallery-image "/images/..." "caption" >}}` shortcode and the `layouts/labpictures/single.html` layout.

**Lab Pictures rule:** Any image that is not the current group photo on the Group page and is not directly linked to a blog post or news item should be added to `content/lab-pictures.md` using the `gallery-image` shortcode. Keep entries ordered newest first (by the date the photo was taken). When a group photo is replaced as the current photo on the Group page, add the old photo to `content/lab-pictures.md`.

## Important Files

- `config.toml` - Site configuration, navigation menu, social links, theme settings
- `content/group.md` - Group page content (current group photo, member grid, former members)
- `content/lab-pictures.md` - Gallery of group photos and other images, newest first
- `content/news/_index.md` - News section landing page
- `layouts/labpictures/single.html` - Lab Pictures page layout (2-column flex grid)
- `layouts/partials/group-partial.html` - Individual member profile display logic
- `layouts/shortcodes/group.html` - Multi-function shortcode for group photos and member grids
- `layouts/shortcodes/gallery-image.html` - Shortcode for Lab Pictures gallery entries
- `static/css/share-button.css` - Social sharing button styles (includes custom bluesky styling)
