# AGENTS.md

Instructions for coding agents working in this repo. For how the site is put
together and how to do common tasks, read [DOCS.md](DOCS.md).

## What this is

Atreya Ghosal's personal site and blog: a Jekyll site served by GitHub Pages
from the `master` branch of `atreyaghosal/atreyaghosal.github.io`. It started
as a fork of the [contrast](https://github.com/niklasbuschmann/contrast) theme
(remote `upstream`), and the theme has since been rebuilt on Material 3. Don't
merge upstream changes without being asked to.

## Build and check

```sh
bundle install                      # gems go to vendor/bundle (see .bundle/config)
bundle exec jekyll serve            # http://localhost:4000, rebuilds on change
bundle exec jekyll build            # one-off build into _site/
```

There are no tests. `bundle exec jekyll build` is the check: run it after any
change to `_layouts`, `_includes`, `_sass`, `assets/css` or `_config.yml`, and
make sure it finishes without errors. Pushing to `master` deploys the site, so
a broken build means a broken live site.

## The toolchain is old, on purpose

GitHub Pages builds with the `github-pages` gem: **Jekyll 3.10 and Ruby Sass
3.7.4**, not Jekyll 4 or Dart Sass. The Gemfile depends on the same gem so the
local build matches the deploy. Don't swap in Jekyll 4, `jekyll-sass-converter`
2+ or a GitHub Actions workflow unless asked to.

Ruby Sass 3.7 gets these wrong:

- **No trailing `//` comment on a variable line.** `$measure: 45rem !default // note`
  breaks the build. Put the comment on its own line above.
- No `@use`, `@forward`, `math.div` or other Dart-Sass-only module syntax. Use
  `@import` and `/`.
- Stylesheets are the indented `.sass` syntax, not `.scss`.
- Plugins are limited to the
  [Pages allowlist](https://pages.github.com/versions/). Only `jekyll-feed` is
  enabled.

## Conventions

- **Colours go through tokens.** Set the value in `_sass/index.sass` (light
  `$l-*` and dark `$d-*`), expose it as a custom property in
  `_sass/tokens.sass`, and use `var(--role)` everywhere else. Don't hard-code
  a colour in a rule. The one exception is the syntax colours in
  `_sass/classes.sass`, which were picked to clear 4.5:1 contrast on
  `--code-bg`.
- **Dark mode has two triggers.** It applies when the system prefers dark and
  the reader hasn't picked light (`:root:not([data-theme="light"])` inside
  `@media (prefers-color-scheme: dark)`), or when the reader picked dark
  (`:root[data-theme="dark"]`). Any new dark-mode rule needs both. Copy the
  mixin pattern from `tokens.sass` or `illustrated.sass`.
- **Accessibility is a requirement.** Keep visible focus outlines, `aria-*`
  labels on icon-only controls, alt text on every image, and underlines on
  links in `main` (colour is never the only signal). Keep text at 4.5:1
  contrast or better in both schemes.
- **Comments explain why.** Existing comments explain the reasoning behind a
  rule. Match that, and don't add comments that just say what a rule does.
- **URLs use `relative_url`**: `{{ "/assets/..." | relative_url }}`.
- **Browser storage** (`theme`, `tokMode`) is always read and written inside
  `try/catch`.

## Writing posts

- File: `_posts/YYYY-MM-DD-slug.md`. The URL is `/:title/`, so the slug becomes
  the path (`/unequal_voices/`).
- Front matter: `title`, `layout: post`, `categories: misc`. Add `mathjax: true`
  for KaTeX (`$$...$$`) and `illustrated: true` for the coloured-symbol toolkit.
- The **excerpt** on the home page ends at the first **two blank lines in a row**
  (`excerpt_separator: "\n\n\n"`). One blank line doesn't end it.
- Images go in `assets/images/<post-slug>/`, with `srcset`, `width`/`height`
  and alt text. See the first post for the pattern.
- Don't edit the author's prose unless asked to. Fixing markup is fine.

## Don't touch

- `_site/`, `.jekyll-cache/` and `.sass-cache/` are build output.
- `vendor/bundle/` holds installed gems and is gitignored.
- `assets/katex/` and `_data/font-awesome/` are third-party code, vendored
  as-is.
- `.kilo/` holds another tool's worktrees.
- `wiki/` is private notes. It's excluded from the build, so keep it out of
  the site: no links to it and no front matter.

## Docs

- If a change affects anything documented in `DOCS.md` (structure, config keys,
  how-tos), update `DOCS.md` in the same change.
- `AGENTS.md`, `CLAUDE.md` and `DOCS.md` are listed under `exclude:` in
  `_config.yml`. Otherwise Pages would publish them, because it renders `.md`
  files that have no front matter. Any new top-level Markdown file that isn't
  a page goes in that list too.

## Git

Pushing to `master` deploys the site. Commit and push only when asked to.
