# Omniscope Knowledge Base — Project Guide

## On session start

Immediately start the local Jekyll preview server so the user can browse
at http://localhost:14000 while we work:

```bash
bundle exec jekyll build
bundle exec jekyll serve --host 0.0.0.0 --port 4000 --detach --skip-initial-build
```

Tell the user the preview is ready at **http://localhost:14000**.

---

This is the Omniscope product knowledge base, hosted on GitHub Pages.

- **Repo:** `visokio/gh-pages-test`
- **Live URL:** https://visokio.github.io/gh-pages-test/
- **Source content:** Ported from https://help.visokio.com/support/solutions

## Architecture

Plain Markdown files rendered by GitHub Pages' built-in Jekyll. No custom
static site generator, no npm, no complex build pipeline.

### Theme & styling
- Minima theme (`_config.yml`: `theme: minima`)
- Custom colour scheme in `assets/main.scss` — dark blue header (#1a365d),
  blue links (#2b6cb0), styled code blocks, tables, blockquotes, images
- No JavaScript frameworks

### Navigation
- **Home page (`index.md`)** is the catalogue — all articles listed by category
- **Breadcrumbs** on each article page, rendered by custom `_layouts/page.html`
  using a `breadcrumb` front matter field. Breadcrumb appears above the title
  and is repeated at the bottom of the page
- **No site-wide nav bar** — Minima's auto header nav is suppressed via
  custom `_includes/header.html` (empty nav) and `header_pages: []` in config

### noindex
All pages have `<meta name="robots" content="noindex, nofollow">` injected
via `_includes/custom-head.html`. The site is public but not search-indexed.

## File structure

```
_config.yml                    # Theme, title, description, header_pages
_includes/
  custom-head.html             # noindex meta tag
  header.html                  # Overrides Minima header (no page nav)
_layouts/
  home.html                    # Overrides Minima home (no duplicate title)
  page.html                    # Overrides Minima page (breadcrumb above title + footer)
assets/
  main.scss                    # Custom styles layered on Minima
images/                        # All article images, named descriptively per article
index.md                       # Home page / catalogue
Gemfile                        # Pins github-pages gem for identical local builds
.gitignore                     # Excludes _site/, .jekyll-cache/, Gemfile.lock
```

## Content conventions

### Adding a new article

1. Create `article-slug.md` at the repo root
2. Add front matter with `title` and `breadcrumb` (the category name):
   ```yaml
   ---
   title: My New Article Title
   breadcrumb: Category Name
   ---
   ```
3. Write content starting directly (no `# Title` heading — Minima renders
   the title from front matter via the page layout)
4. Add the article to `index.md` under the appropriate category section
5. Save images to `images/` with descriptive names prefixed by article slug
   (e.g. `my-article-screenshot.png`)

### Current categories (as defined in index.md)
- **Getting Started** — introductory guides
- **Installation & Deployment** — install, config, infrastructure
- **AI** — enabling and configuring AI features
- **Big Data** — large-scale data integrations

New categories can be added as `## Heading` sections in `index.md`.

### Current articles
| File | Title | Category |
|------|-------|----------|
| `getting-started.md` | Getting Started - create a new Omniscope project | Getting Started |
| `linux-installation.md` | Linux Installation | Installation & Deployment |
| `running-in-memory.md` | Running Omniscope Fully In-Memory (Processing + Reporting) | Installation & Deployment |
| `concurrent-workflow-jobs.md` | Concurrent Workflow jobs execution | Installation & Deployment |
| `enable-ai.md` | How to enable AI in Omniscope | AI |
| `local-ai-model.md` | Using a local AI model | AI |
| `omniscope-impala-big-data.md` | Omniscope + Impala = Big Data Live reports on Hadoop. (Step by step guide) | Big Data |

### Image naming convention
Images are stored in `images/` with descriptive names prefixed by article:
- `getting-started-welcome.png`, `getting-started-workflow.png`
- `enable-ai-labs.png`, `enable-ai-settings.png`
- `impala-01.png` through `impala-14.png`
- `local-ai-config.png`, `local-ai-dashboard.png`

### Content rules
- **Real content only** — never use placeholder or faked content
- **Port from the original help site** where articles exist there
- **Faithful porting** — when converting from the original help site, preserve
  the exact words, sentences, and structure. Do not rephrase, summarise, or
  "improve" the content during conversion. The task is to create equivalent
  Markdown from the source HTML, not to rewrite it. Content improvements are
  done deliberately as a separate pass, never during the initial port.
- **How to port:** Fetch the raw HTML of the original page (e.g. `curl -sL`),
  then convert the article body HTML to Markdown manually — do not use an AI
  summariser to "extract" content, as it will rephrase. Preserve paragraph
  breaks, heading levels, bold/italic, lists, code blocks, blockquotes,
  tables, image positions, and link targets exactly as they appear.
- Heading levels in articles start at `##` (h2), since `# Title` (h1) is
  rendered automatically from front matter by the page layout
- Links to other KB articles use relative `.md` paths: `[link](other.md)`

## Workflow

- **Commit freely** to local git during iteration
- **Push in batches** — every push triggers an automatic GH Pages build
  (GitHub soft limit: 10 builds/hour)
- **Local preview** with Jekyll for iteration without burning builds

### Local preview
The container has Ruby, Bundler, Chromium, and all gems baked in. On each session:

```bash
bundle exec jekyll build       # build the site
bundle exec jekyll serve --host 0.0.0.0 --port 4000 --detach --skip-initial-build
```

Preview at **http://localhost:14000** from host (port 4000 + 10000 offset).

To rebuild after changes: kill Jekyll, `jekyll build`, serve again.
Or run without `--detach` for auto-rebuild (blocks terminal).

### Visual review with headless Chrome
```bash
chromium --headless --no-sandbox --disable-gpu \
  --screenshot=/tmp/screenshot.png --window-size=1280,900 \
  http://localhost:4000/page-name.html 2>/dev/null
```
Then read the PNG to visually inspect the rendered page.

### Pushing to live
```bash
git push origin main
```
GitHub Pages auto-builds within 1-2 minutes. If the first build after
enabling Pages doesn't trigger, manually trigger via:
```bash
gh api repos/visokio/gh-pages-test/pages/builds -X POST
```

## GitHub details

- **Org:** visokio
- **Repo:** gh-pages-test (public)
- **Pages source:** deploy from `main` branch (legacy/Jekyll mode)
- **gh CLI** is authenticated as `supersteves` with access to the `visokio` org
- **PAT limitations:** no `workflow` scope (can't push GitHub Actions files),
  no `delete_repo` scope
