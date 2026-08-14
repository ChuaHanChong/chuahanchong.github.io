# chuahanchong.github.io

Personal academic site for Chua Han Chong — PhD student, College of Computing and Data Science, Nanyang Technological University.

Built on [al-folio](https://github.com/alshedivat/al-folio) v1.2 (Jekyll), deployed to GitHub Pages by `.github/workflows/deploy.yml` on every push to `main`.

## Where things live

| What | File |
|---|---|
| Bio, photo, affiliation, social links | `_pages/about.md` |
| Site title, URL, SEO, feature switches | `_config.yml` |
| CV content — **and** the downloadable PDF | `_data/cv.yml` |
| Social icons and their handles | `_data/socials.yml` |
| News items shown on the about page | `_news/*.md` |
| Publications | `_bibliography/papers.bib` |
| Accent colour | `assets/css/main.scss` (`$purple-color`) and `design.colors` in `_data/cv.yml` |

Design decisions and the reasoning behind them: [`docs/decisions.md`](docs/decisions.md).

## Local preview

Either path works. Preview is **only** for your machine — the live site is built by GitHub Actions, so nothing here needs to be installed or running for the site to be up.

### Native (what this repo is set up for)

Gems are already installed under `vendor/bundle`. macOS system Ruby is far too old, so use the Homebrew one:

```bash
brew install ruby imagemagick          # once
export PATH="/opt/homebrew/opt/ruby/bin:$PATH"
bundle install                         # once
bundle exec jekyll serve
```

Then open <http://localhost:4000>.

### Docker (upstream's recommended path)

al-folio marks the native setup "legacy, no longer supported" and documents Docker instead:

```bash
docker compose pull
docker compose up
```

Then open <http://localhost:8080>.

Docker Desktop is **not currently installed here**. Installing it needs a terminal that can prompt for `sudo` (the cask symlinks helpers into `/usr/local/bin`), so run it yourself:

```bash
brew install --cask docker
```

## The CV is one source, two outputs

`_data/cv.yml` produces both the `/cv/` page and `assets/rendercv/rendercv_output/Chua_Han_Chong_CV.pdf`. Editing that file and pushing triggers `.github/workflows/render-cv.yml`, which regenerates the PDF and commits it back. Never hand-edit the PDF.

Two traps worth knowing, both verified against RenderCV v2.8:

- **Do not add `label`, `image`, `summary`, or `address` under `cv:`.** RenderCV 2.x rejects them and the workflow fails. They shipped in al-folio's demo file; they are also unused by the website's CV templates.
- **`assets/rendercv/design.yaml` is inert.** RenderCV reads it only when passed `--design`, which the stock workflow does not do. The design that actually ships is the `design:` block at the top of `_data/cv.yml`.

## Pages that exist but are not published

`publications`, `projects`, `blog`, `news`, `repositories`, and `teaching` are all written and wired, but carry `published: false`. That means Jekyll does not build them at all — no URL, no sitemap entry, nothing indexable. Each file's front matter says what to add before flipping it to `true`.

## Known quirks (verified, deliberately not patched)

- `/cv/` renders **Experience before Education**; the PDF renders Education first. The order is hard-coded in the `al_folio_cv` gem and cannot be configured. Renaming the section drops the entries entirely.
- `_config.yml`'s `description:` **must stay a single-line scalar**. A `>` folded block adds a trailing newline that lands inside the schema.org JSON-LD and makes it unparseable.
- The `sameAs` array in the JSON-LD starts with a `null`, contributed by the gem. Harmless; consumers ignore it.
- `_config.yml` splits the name as Chua / Han / Chong so the footer and metadata match the heading. The surname is really Chua, so the blog's "cite this" block would read "Chong, Chua Han" — only relevant once the blog is published.

## Open TODOs

- ORCID iD, Google Scholar ID, X, Hugging Face — `_data/socials.yml`
- MSc completion month — `_data/cv.yml`, currently `2026-01`, confirm against transcript
- Certificate dates — `_data/cv.yml`, omitted rather than guessed
- Higher-resolution profile photo — `assets/img/prof_pic.jpg` is 400×514, soft on retina displays

## Upgrading al-folio

`assets/css/main.scss` is a local override of a gem-owned file, taken from al_folio_core at v1.2. When you take an al-folio upgrade, diff it against
<https://github.com/al-org-dev/al-folio-core/blob/main/assets/css/main.scss> and re-sync, or newly added styles will silently go missing.
