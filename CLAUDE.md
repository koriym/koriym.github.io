# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Jekyll-based blog (BEAR Blog) hosted on GitHub Pages at https://koriym.github.io/. The site focuses on PHP development, BEAR.Sunday framework, dependency injection, and software architecture topics. Content is primarily in Japanese with some English posts.

## Development Commands

### Local Development
```bash
bundle exec jekyll serve --watch
```
This starts the Jekyll development server with live reload. The site will be available at http://localhost:4000.

### Installing Dependencies
```bash
bundle install
```
Install Ruby gems and Jekyll dependencies.

## Site Configuration

### Key Settings (_config.yml)
- **Timezone**: Asia/Tokyo
- **Theme**: Minima (~> 2.5)
- **Syntax Highlighter**: Rouge 4.2.1
- **Permalink Structure**: `/:categories/:year/:month/:day/:title/`
- **Default Language**: Japanese (ja)

### Social Integration
The site includes Facebook, Twitter, Disqus comments, and Google Analytics integrations configured in `_config.yml`.

## Content Structure

### Blog Posts (_posts/)
- **Naming Convention**: `YYYY-MM-DD-title.md` or `YYYY-MM-DD-title-{en|ja}.md` for bilingual posts
- **Front Matter Requirements**:
  - `layout: post` (always)
  - `title`: Post title
  - `date`: Publication date with timezone (e.g., `2025-08-10 12:00:00 +0900`)
  - `categories`: Array, typically `["blog"]`
  - `tags`: Array of relevant tags
  - `comments`: Boolean (set to `false` to disable Disqus)
  - `permalink`: Custom URL path (optional but recommended for bilingual posts)
  - `image`: OGP image path (optional)
  - `list_title`: Boolean for title display (optional)

### Bilingual Content Pattern
Two naming conventions exist for bilingual posts:
- Hyphen suffix: `2025-08-10-semantic-method-ja.md` / `2025-08-10-semantic-method-en.md`
- Dot suffix: `2025-04-22-alps-mcp.md` (Japanese) / `2025-04-22-alps-mcp.en.md` (English)

Both require explicit `permalink` in front matter to set the URL (e.g., `/blog/2025/08/10/semantic-method-ja`).

### Layouts
- `base.html`: Base template
- `home.html`: Home page layout
- `post.html`: Blog post layout with social sharing buttons and Disqus integration

### Includes (_includes/)
Reusable components for social features:
- `facebook.html`: Facebook Like button
- `twitter.html`: Twitter share button
- `hatena.html`: Hatena bookmark button
- `disqus.html` / `disqus_comments.html`: Comment system
- `custom-head.html`: Custom head elements
- `archive_post.html`: Archive display

## Important Notes

### Markdown Structure
- Use proper heading levels (`##`, `###`, `####`) for document structure
- NEVER use bold text (`**text**`) as section headings
- Proper semantic heading hierarchy improves accessibility and navigation

### Image References
Images should be placed in the `/images/` directory and referenced with absolute paths starting with `/`:
```markdown
![Alt text](/images/folder/image.png){:width="500px"}
```
Note: Some older posts use `//images/...` (protocol-relative) — new posts should use `/images/...` (site-relative).

### Configuration Restart Required
When modifying `_config.yml`, the Jekyll server must be restarted manually as changes are not auto-reloaded.

## Architecture

This is a standard Jekyll static site with:
- **Content**: Markdown files in `_posts/`
- **Presentation**: Liquid templates in `_layouts/` and `_includes/`
- **Styling**: Minima theme (gem-based)
- **Generation**: Jekyll processes Markdown → HTML with front matter variables
- **Deployment**: GitHub Pages (master branch)