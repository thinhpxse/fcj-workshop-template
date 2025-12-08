<!-- Copilot / AI agent instructions for fcj-workshop-template -->
# Copilot instructions

Purpose: Help AI coding agents be productive in this Hugo-based workshop template.

1) Big picture
- **Site type**: Hugo static site using the `hugo-theme-learn` theme (`config.toml` sets `theme = "hugo-theme-learn"`).
- **Source vs generated**: Edit source files under `content/`, `layouts/`, `themes/` (overrides) and `static/`. Do NOT edit `public/` (it's the generated output) except when intentionally updating the published bundle—always regenerate from source.

2) Key files and examples
- **Site config**: `config.toml` — contains language configuration, `themeVariant`, `relativeURLs`, and `markup.goldmark.renderer.unsafe = true` (allows raw HTML in Markdown). Example: `Languages.en` and `Languages.vi` sections for bilingual site.
- **Content pattern**: Content uses Hugo conventions with `*_index.md` and `index.vi.md` translation files. Example: `content/1-Worklog/_index.md` and `content/1-Worklog/1.5-Week5/_index.md`.
- **Layout overrides**: Customizations are in `layouts/` (e.g. `layouts/partials/custom-footer.html`) and `layouts/shortcodes/` (e.g. `tabs.html`, `ghcontributors.html`). Prefer adding overrides in `layouts/` over editing `themes/hugo-theme-learn` directly.
- **Theme**: `themes/hugo-theme-learn/` contains upstream templates—read before overriding.

3) Common developer workflows (commands)
- **Local preview (recommended)**: run Hugo server to preview changes locally:

```powershell
hugo server -D
```

- **Build production site (generates `public/`)**:

```powershell
hugo --minify
```

- **Quick checks**: `hugo version` and `hugo --debug` for troubleshooting.

4) Project-specific conventions
- **Numeric folders**: Content folders start with numbers (e.g. `1-Worklog`, `3-BlogsTranslated`) to control organization and nav ordering—maintain those prefixes.
- **Translations**: Vietnamese pages use `.vi.md` (e.g. `_index.vi.md`) alongside English pages; follow filename parity for translations.
- **Front matter**: Content files use YAML front matter (`---`) with `title`, `weight`, `chapter`, etc. Keep `weight` consistent for ordering.
- **Raw HTML allowed**: Because `unsafe = true` is set, inline HTML appears in Markdown (see `content/1-Worklog/_index.md` which uses Hugo shortcodes and raw HTML).

5) Integration points & external deps
- **Analytics**: `layouts/partials/custom-footer.html` includes Google Analytics snippet — update GA ID there when needed.
- **Menus/shortcuts**: `config.toml` defines menu shortcut items under each language (`Languages.en.menu.shortcuts`).

6) Editing guidance for AI agents
- Edit content under `content/`. For layout or component changes, put overrides under `layouts/` (not `public/`).
- When adding features that affect theme behavior, prefer adding an override file in `layouts/` that only replaces the minimal template fragment.
- When modifying config, run `hugo server -D` to validate rendering and check `public/` changes after `hugo --minify`.

7) PR / commit checklist for agents
- **Source edits only**: Ensure changes are made in `content/`, `layouts/`, `static/`, or `config.toml`.
- **Local validation**: Run `hugo server -D` and spot-check pages mentioned in the change.
- **Regenerate site**: Optionally run `hugo --minify` and include `public/` updates only if the project expects generated assets to be committed.

If anything here is unclear or you want more examples (e.g. a step-by-step for adding a translated page), tell me which area to expand.
