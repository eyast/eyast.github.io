# eyast.github.io

Personal portfolio site of Eyas Taifour, built with [Jekyll](https://jekyllrb.com/) and an original minimal portfolio theme, deployed to GitHub Pages via GitHub Actions.

## Local development

```sh
bundle install
bundle exec jekyll serve
```

## Structure

- `_projects/` — portfolio project pages (collection)
- `_posts/` — writing (essays and papers)
- `_layouts/`, `_includes/`, `_sass/` — the theme
- `assets/img/` — images; `assets/papers/` — paper PDFs
- `.github/workflows/pages.yml` — build and deploy to GitHub Pages

## Adding content

New project: add a markdown file to `_projects/` with front matter (`title`, `subtitle`, `description`, `image`, `order`, `featured`, `year`, `affiliation`, `methods`, `tools`, `links`).

New writing: add a post to `_posts/` named `YYYY-MM-DD-title.md`.
