# Glossary

Terms used in [`decisions.md`](decisions.md) and across this repository, in the sense
they carry *here*.

## Site and tooling

**al-folio** — the Jekyll starter this site is built on. As of v1.x it is a *thin
starter*, not a theme: this repository owns content and wiring, while layouts,
includes, Sass, and Liquid tags live in separately versioned gems under the
[`al-org-dev`](https://github.com/al-org-dev) organisation.

**Thin starter** — a project template that holds only your content plus a small amount
of configuration, delegating all rendering to versioned dependencies. The trade: you get
upstream improvements without merging, but you cannot edit rendering code in place —
you override it, and every override becomes an upgrade liability (see decision 13).

**Gem-owned file** — a file that ships inside an al-folio plugin gem rather than this
repository. Placing a file at the same path locally *shadows* it completely; there is no
partial override. `assets/css/main.scss` is the one file shadowed here.

**Jekyll collection** — a folder of Markdown files Jekyll treats as a set (`_news`,
`_posts`, `_projects`). Declared in `_config.yml` under `collections:`.

**jekyll-scholar** — the plugin that renders `_bibliography/papers.bib` into the
publications page. It is why publications are maintained as BibTeX rather than written
as HTML.

**RenderCV** — the tool that turns `_data/cv.yml` into a typeset PDF, via Typst. Run in
CI by `.github/workflows/render-cv.yml`. Its YAML schema is strict: unknown keys are
errors, which is how al-folio's own demo CV came to be broken (decision 15).

**`published: false`** — Jekyll front matter meaning "do not build this file at all". It
produces no output file, no URL, and no `sitemap.xml` entry. Distinct from `nav: false`,
which only removes the navbar link while the page stays live and indexable.

**`--global-theme-color`** — the CSS custom property al_folio_core uses for the site
accent in both light and dark themes. Fed from the Sass variable `$purple-color`, whose
name is now a misnomer here: it holds teal.

## Research

**Spatial intelligence** — an agent's capacity to build and use a representation of
physical space: where things are, how they relate geometrically, and what follows for
acting on them.

**Embodied AI** — AI studied through an agent that perceives and acts in a physical or
simulated body, so perception, action, and consequence form a loop rather than a
single-shot prediction.

**Real2sim2real** — the loop of reconstructing a real environment in simulation,
training or evaluating in that simulation at a scale reality does not allow, then
transferring back to real hardware. The hard part is the last step.

**Sim-to-real gap** — the discrepancy between simulated and real conditions that makes a
policy or model work in simulation and fail in the world. The problem motivating the
research direction here.

## Page design

**Findability page** — a personal site whose job is to be the canonical result when
someone searches your name: who you are, what you work on, how to reach you. Contrasted
with a research showcase (publications first) or a blog (writing first). See decision 1.
