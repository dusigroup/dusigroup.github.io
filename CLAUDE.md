# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

A [Hugo](https://gohugo.io/) static site using the [Congo](https://jpanther.github.io/congo/) theme, intended for deployment as a GitHub Pages user site (repo name `dusigroup.github.io`). The theme is vendored as a git submodule at `themes/congo` (pinned to `v2.14.0`, tracking the `stable` branch). 

The site serves as a landing page for three other websites:
- https://pluginpublications.dusigroup.com: a website for wellness within, offering books for adults and children. Its goal is "Plug-In Publications brings you books that inspire growth, self-discovery, and meaningful transformation, for every age and every stage of life. Because transformation doesn’t have an age limit. It starts the moment a story touches your heart."
- https://thelearningspoon.moonsdelizie.com: a website offering classes for young chefs. Its goal is "Where young chefs explore the world through food, culture, and community. At The Learning Spoon Academy, we believe every recipe tells a story. Our kids’ cooking classes bring together hands-on cooking, cultural studies, geography, and teamwork — designed with homeschooling families in mind. Children don’t just learn how to cook; they learn how to connect with the world"
- https://www.iocrafts.com: a website offering software solutions. Its goal is "Crafting intelligent software, bit by bit"

The logo of Dusi Group is under `assets/img/logo.png` and it represents three shelves, one for each of the websites described above.

## Commands

```bash
# Run local dev server with drafts/future content visible, live reload
hugo server -D

# Build the production site into public/
hugo

# Create a new content page from the default archetype
hugo new content posts/my-post.md
```

Requires the **extended** Hugo binary (`hugo version` should show `+extended`) because the theme relies on Sass/SCSS asset pipelines.

After cloning, submodules must be initialized or the theme will be missing:

```bash
git submodule update --init --recursive
```

There is no linter or test suite in this repo; validate changes by running `hugo server -D` and checking the rendered output, and by running a plain `hugo` build to confirm it completes without errors/warnings.

## Configuration architecture

Hugo config is split across `config/_default/*.toml` (Hugo's directory-based config, merged automatically) rather than a single file. `hugo.toml` at the repo root is a leftover/minimal default and is effectively superseded by `config/_default/`:

- `hugo.toml` — top-level site config: `baseURL`, `theme = "congo"`, pagination, output formats, privacy/services settings required by the theme.
- `languages.en.toml` — per-language settings (title, author info, social links) — currently mostly commented-out placeholders from the theme's example config.
- `menus.en.toml` — main/footer navigation menu entries, referencing content by `pageRef`.
- `params.toml` — Congo theme parameters controlling layout/appearance (header/footer layout, homepage layout, article display options, analytics/verification keys). This is the primary file for visual/behavioral customization — see the [Congo configuration docs](https://jpanther.github.io/congo/docs/configuration/) for what each key does.
- `markup.toml` — goldmark/markdown rendering settings required by the theme (unsafe HTML passthrough, math delimiters, syntax highlighting, TOC levels).
- `module.toml` — minimum Hugo version requirement for the theme.

When adding site content or customizing behavior, prefer editing the relevant `config/_default/*.toml` file over `hugo.toml`.

## Content model (once populated)

Congo expects content organized into sections that correspond to the menu entries already configured in `menus.en.toml`:
- `content/posts/` — blog posts (referenced by the "Blog" menu item, `pageRef = "posts"`)
- Taxonomies `categories` and `tags` are enabled by default (Hugo's default taxonomies) and are already wired into the main menu.

Custom layouts/partials overriding the theme go in `layouts/` at the repo root (Hugo's standard override mechanism — any file here takes precedence over the same path in `themes/congo/layouts/`). Static assets that should be copied as-is go in `static/`; assets processed through Hugo Pipes (SCSS, JS bundling) go in `assets/`.

## Notes

- `public/` is the Hugo build output directory. It is currently untracked/uncommitted — decide whether it should be gitignored (typical when deploying via CI) or committed to a `docs/` folder or separate branch (typical for simple GitHub Pages setups without Actions) before setting up deployment.
- `.hugo_build.lock` is a transient lock file created while `hugo server` is running; safe to ignore/delete.
