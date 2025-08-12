# GitHub Copilot Instructions for My Jekyll Static Blog

## Project Context
This repository hosts a Jekyll-powered blog, using Markdown for content and Liquid for templating. The site is built via GitHub Actions and hosted on GitHub Pages.

---

## Jekyll Configuration (must-have)

- Use only GitHub Pages–approved plugins. In `_config.yml` set:
  - `url` and `baseurl` correctly (no trailing slash in `url`).
  - `permalink:`   descriptive URLs.
  - `paginate` (e.g., 10) and configure `_pages/blog.html` to use pagination.
  - `plugins:`
    - `jekyll-sitemap`
    - `jekyll-paginate`
  - `defaults:` to set sensible metadata (examples below) and to avoid repetitive front matter.
- Include `title`, `description`, `lang`, and `timezone` at site level.
- Ensure `feed.xml`, `sitemap.xml`, and `robots.txt` are present (jekyll-sitemap handle the first two).

Example `_config.yml` block:
```
url: https://hella.website
baseurl: ""
lang: en
paginate: 10
plugins:
  - jekyll-sitemap
  - jekyll-paginate

# Useful defaults
defaults:
  - scope:
      path: ""
      type: posts
    values:
      layout: post
      sitemap: true
      author: site.author
      image: 
      last_modified_at: ""
      # Set to true on drafts or announcements you don't want indexed
      noindex: false
  - scope:
      path: "_pages"
    values:
      layout: page
      sitemap: true
```

---

## Content Guidelines

- Posts:
  - All posts must be Markdown (`.md`) in `_posts` with filenames `YYYY-MM-DD-slug.md`.
  - YAML front matter must include at minimum: `title`, `date`, `description`, `categories` (string or list), `tags` (list).
  - Keep titles under ~60 characters and descriptions ~155 characters.
  - Use descriptive, keyword-rich slugs (lowercase, hyphenated). Avoid stop words.
  - Add `last_modified_at` when updating a post.
- Pages:
  - Place in `_pages/` where possible; give each a clear `permalink`.
- Internal links:
  - Use relative links (`/category/post-slug/`) and prefer Liquid `link`/`post_url` tags where applicable.
- External links:
  - Add `rel="noopener noreferrer"` and `target="_blank"` only when necessary.
- Images & Media:
  - Store in `assets/images/` (or `images/` if already configured); use descriptive file names.
  - Always provide meaningful `alt` text; if decorative, use `alt=""`.
  - Prefer modern formats (WebP/AVIF) with JPEG/PNG fallbacks using `<picture>`.
  - Include explicit `width` and `height` attributes to reserve layout space.
  - Use captions where helpful for context.
- Code Blocks:
  - Use fenced code blocks with language identifiers (```js, ```rb, etc.) to enable syntax highlighting.

---

## HTML Head Integration (required)

In `_includes/head.html` ensure the following (adjust paths as needed):
```
{%- seo -%}
<link rel="canonical" href="{{ page.canonical_url | default: page.url | absolute_url }}">
<meta name="robots" content="{% if page.noindex %}noindex, nofollow{% else %}index, follow{% endif %}">
<link rel="sitemap" type="application/xml" title="Sitemap" href="{{ "/sitemap.xml" | absolute_url }}">
<link rel="alternate" type="application/atom+xml" href="{{ "/feed.xml" | absolute_url }}" title="{{ site.title }}">
<meta name="theme-color" content="#000000">
```
- Include social preview tags via `jekyll-seo-tag` (`{%- seo -%}`) and ensure `site.title`, `site.description`, and `page.image` are set.
- Add `<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>` if loading remote fonts; set `font-display: swap`.
- Preload critical fonts and hero image if appropriate.

---

## Performance Best Practices

- Images:
  - Compress assets before committing. Prefer AVIF/WebP; provide fallbacks with `<picture>`.
  - Use responsive images (`srcset`/`sizes`) and include `loading="lazy"` and `decoding="async"` on non-critical images.
  - Always set `width` and `height` attributes to avoid layout shift (CLS).
- CSS:
  - Keep CSS minimal; avoid large frameworks. Inline critical CSS; load the main stylesheet with `media="print" onload="this.media='all'"` pattern if necessary.
  - Purge unused CSS during build if using a pipeline; otherwise keep CSS lean manually.
- JavaScript:
  - Load scripts with `defer` (preferred) or `async` where safe. Avoid render-blocking JS.
  - Avoid heavy client-side libraries; prefer native browser features.
- Fonts:
  - Self-host fonts when possible; use `font-display: swap`; subset character ranges.
- Caching/Fingerprinting:
  - GitHub Pages adds caching headers; prefer versioned asset filenames (e.g., `app.20250812.css`) or query strings on links when updating.
- Third Parties:
  - Minimize third-party scripts. Load analytics with `defer` and respect user consent.
- Pagination & Excerpts:
  - Use Jekyll excerpts on listing pages to avoid rendering full posts; paginate listing pages.
- Favicon & Icons:
  - Provide multiple sizes and a web app manifest if needed.

---

## SEO Best Practices

- Metadata:
  - Every page/post must include unique `title` and `description` in front matter.
  - Include Open Graph and Twitter Card tags. Set `image` in front matter for rich previews.
  - Add `canonical_url` in front matter if the canonical differs from the generated URL.
  - Set `last_modified_at` for updated content; include `dateModified` via `jekyll-seo-tag` support.
- URL Structure:
  - Use descriptive permalinks; avoid changing URLs. If changed, add `redirect_from:` to preserve SEO.
- Structured Data:
  - Include JSON-LD where beneficial (e.g., `BlogPosting`, `BreadcrumbList`). You can add a small JSON-LD block in `head.html` using page variables when `page.schema` is present.
- Indexing Controls:
  - Use `noindex: true` on thin or duplicate pages (e.g., tag listings if desired). Keep paginated pages indexable or set `noindex` based on strategy.
- Internal Linking:
  - Link related posts; include previous/next navigation and related posts sections.
- Sitemap & Robots:
  - Ensure `sitemap.xml` is generated and `robots.txt` allows crawling of needed paths.

---

## Accessibility (A11y)

- Headings in logical order (one `h1` per page); do not skip levels.
- Landmarks: use `<header>`, `<nav>`, `<main>`, `<article>`, `<aside>`, `<footer>` appropriately.
- Keyboard: ensure focus styles are visible; avoid keyboard traps.
- Motion: respect `prefers-reduced-motion`; avoid autoplaying media.
- Color contrast: follow WCAG AA at minimum.
- Forms: associate labels and inputs; provide error messages tied to inputs.

---

## CI and Quality Gates

- Add a CI step to run `htmlproofer` (or similar) to catch broken links and HTML issues.
- Periodically run Lighthouse (manually or via CI) and track scores for Performance, SEO, Best Practices, and Accessibility.
- Set a simple performance budget (e.g., `< 200KB CSS`, `< 50KB JS`, Largest Contentful Paint < 2.5s on 4G).

---

## Example Post Front Matter

```
---
title: "How to Optimize Jekyll Blogs for SEO"
date: 2025-08-12
last_modified_at: 2025-08-12
categories: [jekyll]
tags: [jekyll, seo, performance]
description: "Best practices for making your Jekyll blog fast, accessible, and SEO-friendly."
canonical_url: "https://yourblog.com/2025/08/12/optimize-jekyll-seo/"
image: /assets/images/optimize-jekyll-seo-hero.webp
noindex: false
sitemap: true
---
```

---

## Recommended Image Markup Example

```
<picture>
  <source type="image/avif" srcset="/assets/images/hero.avif 1x, /assets/images/hero@2x.avif 2x">
  <source type="image/webp" srcset="/assets/images/hero.webp 1x, /assets/images/hero@2x.webp 2x">
  <img src="/assets/images/hero.jpg" width="1600" height="900" alt="Descriptive alt text" loading="lazy" decoding="async">
</picture>
```

---

## Snippet for JSON-LD (optional, per-page)

```
{% if page.schema %}
<script type="application/ld+json">{{ page.schema | jsonify }}</script>
{% endif %}
```

---

**When generating or editing files, always follow these instructions for structure, performance, SEO, and accessibility.**