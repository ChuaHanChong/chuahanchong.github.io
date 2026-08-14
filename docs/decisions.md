# Decision record

Decisions taken while building this site, and why. Written so that a future change
argues with the reasoning rather than rediscovering it.

Date: 2026-08-14. Decider: Chua Han Chong.

---

## 1. Purpose: findability page, not a research showcase

**Chosen:** a canonical "this is me" page — bio, research interests, links, CV.

**Why:** the PhD started 2026-08-11. There are no papers. A findability page is honest
at year 0 and converts into a publications page for free as output lands. A research
showcase today would be mostly empty sections.

**Rejected:** blog (needs sustained writing habit; most academic blogs stall at two
posts), job-hunt landing page (premature).

---

## 2. Hosting: GitHub Pages user site

**Chosen:** `chuahanchong.github.io`.

**Why:** free, no infrastructure, the GitHub account already exists. A custom domain is
one `CNAME` file away, so deferring it costs nothing and locks in nothing.

**Rejected:** NTU-provided web space (dies at graduation), Vercel/Netlify (another
vendor for a static site), custom domain now (real annual cost, zero benefit today).

---

## 3. Stack: al-folio v1.2

**Chosen:** al-folio, Jekyll, applied via "Use this template" semantics (clone → strip
`.git` → fresh `git init`).

**Why:** it is the field default in ML — 16k stars, 13.1k forks, v1.2 released
2026-08-09. The user asked for modular and maintainable: al-folio means editing YAML
and Markdown, never layout code. v1.x is a thin starter over versioned plugin gems, so
upgrades are less painful than the old fork-and-diverge model.

**Rejected:** plain HTML/CSS (no modularity — explicitly not wanted), academicpages
(lighter but fewer features), Astro (no toolchain cost given Node is installed, but the
academic template ecosystem is immature and the layout becomes yours to maintain).

**Note on fork vs template:** upstream recommends the template button. A fork keeps an
upstream link and makes it easy to open a pull request against al-folio by accident.

---

## 4. Local preview: Homebrew Ruby (Docker was chosen but never installed)

**Chosen originally:** Docker Desktop, `docker compose up`, <http://localhost:8080>, because
al-folio marks the native-Ruby setup "Legacy, no longer supported" and the machine's
system Ruby is 2.6.10 (2019), far below what the Gemfile needs.

**What actually happened:** `brew install --cask docker` failed. The cask symlinks helper
binaries into `/usr/local/bin`, which needs root, and a non-interactive shell gives `sudo`
no terminal to prompt on. Homebrew rolled the install back and purged it, so Docker is
**not installed**.

**Actual setup:** `brew install ruby imagemagick` (no sudo required), then `bundle install`
into `vendor/bundle` and `bundle exec jekyll build`. This has driven every build and every
verification since. Ruby 4.0.6 locally vs 3.3.5 in CI, so a green local build is strong
evidence rather than proof.

**Rejected:** OrbStack and colima (both lighter, but Docker Desktop was the stated
preference), no local preview at all (~1–2 min per feedback cycle and a public commit per typo).

**Consequence:** preview is a local convenience only. The live site is built by GitHub
Actions; the laptop can be off. Docker remains installable at any time with
`brew install --cask docker` run from an interactive terminal.

---

## 5. Sections: all built, only About and CV published

**Chosen:** every page written and wired; `publications`, `projects`, `blog`, `news`,
`repositories`, `teaching` carry `published: false`.

**Why:** a visitor clicking "Publications" and finding a placeholder learns the same
fact more slowly and less kindly than one who sees no such tab. Building the pages now
means turning one on later is a one-word change, not new work.

**Rejected:** "work in progress" placeholders and progress bars (originally requested;
a progress bar visualises a number you do not control and reads oddly to academic
readers), deleting the pages outright (throws away the wiring).

**Key detail:** `published: false`, not `nav: false`. `nav: false` only hides the navbar
link — the page still builds, still has a URL, and still lands in `sitemap.xml` via
jekyll-sitemap, so Google can index an empty page. `published: false` means Jekyll skips
the file entirely.

---

## 6. CV: RenderCV, one source for page and PDF

**Chosen:** `_data/cv.yml` in RenderCV format; `.github/workflows/render-cv.yml`
generates the PDF; `cv_pdf` points at the generated file.

**Why:** recruiters want a PDF, Google wants indexable text, and maintaining both by
hand guarantees they drift. One source removes the drift by construction.

**Rejected:** page only (no attachable PDF), PDF link only (nothing indexable, poor on
mobile), skipping the CV (it is typically the most-clicked link on a PhD student page).

**Superseded:** the original plan was to host the existing hand-designed
`CV_ChuaHanChong.pdf` separately. Discovering the RenderCV pipeline made that pure
duplication, so the generated PDF is now the only one.

---

## 7. Positioning: researcher-first, industry as credibility

**Chosen:** lead with "PhD student, CCDS NTU, working on spatial intelligence, embodied
AI, real2sim2real"; nine years of industry follows as the reason those questions are
interesting.

**Why:** the audience for the next four years is academic. Nine years of production AI
is rare enough among year-1 students to be an asset, but only if framed as the origin of
the research questions rather than as a separate career.

**Rejected:** bridge/dual identity ("bridges research and production" is the most-written
sentence in AI and reads as unfocused before there are papers to anchor it), industry-first,
academic blank slate (discards the differentiator).

**Consequence:** the existing CV was written for industry — "Accomplished AI enthusiast",
"vibe coding", a percentage on every bullet. The site rewords toward research framing while
keeping the substance and the numbers.

---

## 8. Research framing: spatial intelligence, embodied AI, real2sim2real

**Chosen:** those three, stated as the research area, with no named thesis topic.

**Why:** stated by the decider. It also coheres with prior work — DINOv2, SAM2, SigLIP2,
self-supervised representation learning — so it reads as a trajectory rather than a pivot.
Naming interests rather than a thesis topic ages better through year-1 scope changes.

---

## 9. Visual identity: deep teal, light default, dark toggle

**Chosen:** `#0f766e` as the single accent, light theme by default, dark mode available.

**Why:** decided on the decider's behalf (no stated preference). It dates slowly, which
matters for a page touched twice a year; it reads serious to academic readers; and embodied
AI work is visually rich, so figures and video should carry the colour rather than site chrome.

**Rejected:** stock al-folio purple (recognisable at a glance as un-customised), dark
default (worse for long CV text and printing), NTU brand colours (reads as a department
page, and stops being yours at graduation).

**Implementation:** al_folio_core maps `$purple-color` onto `--global-theme-color` and
`--global-hover-color` in both themes, and exposes no other hook, so the accent is set by
configuring that one variable — see decision 13.

---

## 10. Links: three live, four scaffolded

**Chosen:** email, GitHub, LinkedIn live. ORCID, Google Scholar, X, Hugging Face present
in `_data/socials.yml` but commented out.

**Why:** an icon linking to an empty profile is worse than no icon. Commented placeholders
mean turning one on is uncommenting a line.

**Note:** jekyll-socials has no Hugging Face key; it needs `custom_social`. ORCID is worth
registering now even at zero papers — it is free, permanent, and starts accumulating from
the first submission.

---

## 11. Email: NTU address only

**Chosen:** `CHUA1163@e.ntu.edu.sg` on the social icon. No personal address anywhere.

**Why:** an institutional address signals a real researcher at a real lab and fares better
in academic spam filters.

**Superseded 2026-08-14:** originally the personal Gmail was listed alongside it as the
durable fallback for after graduation. Removed at the decider's request. The graduation
problem is now unsolved - the fix is a custom domain with a forwarding alias (decision 2,
deferred), which is the better answer anyway.

---

## 12. Publications and Projects ship empty

**Chosen:** no seeded content in either. `papers.bib` holds a commented template only.

**Why:** the decider's call. The MSc dissertation and the IR↔RGB repositories were available
and were declined.

**Known cost:** the jekyll-scholar pipeline is therefore unexercised. The first real paper is
the first time it runs — which is the worst day to debug it. The commented template in
`papers.bib` is there to shorten that.

---

## 13. Accent set by overriding the gem's `main.scss`

**Chosen:** a local `assets/css/main.scss` copied from al_folio_core, adding
`$purple-color: #0f766e` to the existing `@use "variables" with (...)` call.

**Why:** the variable is declared `!default` in a gem-owned file, and that `with()` call is
the only configuration point. Overriding `_sass/_variables.scss` or `_sass/_themes.scss`
locally would shadow the gem file entirely and require maintaining a full copy.

**Known cost:** the `@use` list is now frozen against the gem. A future al-folio release that
adds or renames a partial will silently drop those styles until this file is re-synced. Flagged
in the file, in `README.md`, and here.

---

## 14. RenderCV design lives in `_data/cv.yml`, not `design.yaml`

**Chosen:** the `design:` block sits at the top of `_data/cv.yml`.
`assets/rendercv/design.yaml` is kept, but carries a warning that it is inert.

**Why:** RenderCV applies a design overlay only when invoked with `--design`. al-folio's
`render-cv.yml` passes only `--settings`, and `settings.render_command.design` is not wired
into the overlay merge. Verified empirically: a bogus key in `design.yaml` produced neither an
error nor any change in output, while the same design block inside `cv.yml` took effect
immediately.

**Rejected:** patching `render-cv.yml` to pass `--design` (works, but edits an upstream file
and adds upgrade friction for no gain).

---

## 15. RenderCV pinned to the 2.x line

**Chosen:** `rendercv[full]>=2.8,<3` in `requirements.txt`.

**Why:** al-folio ships it unpinned, and the result is that upstream's own demo `_data/cv.yml`
**fails to render** against current RenderCV — 2.x removed the top-level `cv.label`, `cv.image`,
`cv.summary`, and `cv.address` keys the demo still uses. `_data/cv.yml` here is written against
2.x; the pin stops a 3.0 release from breaking the CV silently.

---

## 16. Upstream contributor tooling removed

**Chosen:** deleted `CLAUDE.md`, `AGENTS.md`, `.agents/`, `.claude/`, `.codex/`, `.gemini/`,
`.all-contributorsrc`, `readme_preview/`, `lighthouse_results/`, `test/`, the issue and PR
templates, and every workflow except `deploy.yml`, `render-cv.yml`, and `upgrade-check.yml`.

**Why:** those files instruct coding agents on how to contribute *to al-folio*, and would
otherwise govern work in this repository. The remaining CI (prettier, axe, lychee, CodeQL,
visual regression) exists to guard a template project and would mostly email failure notices
about a personal homepage.

**Kept deliberately:** `upgrade-check.yml`, because it reports when a new al-folio release lands
and that is the one piece of upstream CI that saves work here.

**Also kept:** the `giscus:` block in `_config.yml` despite comments being unused —
`deploy.yml` runs a `yaml-update-action` against `giscus.repo` and fails if the key is absent.

---

## 17. Known gem limitations, accepted rather than patched

Three al-folio behaviours were found during verification and deliberately left alone.
Each would cost a template override to fix, and overrides are upgrade liabilities
(decision 13 already carries one that was unavoidable).

**CV section order on the page cannot be controlled.** `al_folio_cv`'s `render.liquid`
renders Experience first, hard-coded, then loops the remaining sections in file order.
So `/cv/` shows Experience before Education while the PDF — which respects `cv.yml`
order — shows Education first. Renaming the section is *not* a workaround: unknown
section names fall through to a generic renderer that only handles `bullet` and `label`
entries, so all nine years of work history would silently vanish from the page.

**`sameAs` in the JSON-LD begins with `null`.** The seed array in `al_folio_core`'s
`metadata.liquid` contributes an empty element. The JSON still parses and consumers
ignore the null; fixing it means overriding a 250-line template.

**The news collection needed `output: false`.** With al-folio's default `output: true`,
each one-line announcement got its own URL and sitemap entry — two thin pages on a
four-page site. They render inline on the about page regardless.

---

## 18. `site.description` must stay a single-line scalar

**Chosen:** `description:` in `_config.yml` is a quoted single-line string, not a `>`
folded block.

**Why:** `al_folio_core`'s `metadata.liquid` interpolates it directly into the
schema.org JSON-LD without `jsonify`. A folded block appends a trailing newline, which
becomes a literal control character inside a JSON string and makes the entire
structured-data block unparseable. Caught by parsing the built `_site/index.html`;
al-folio ships the folded form by default, so this is an upstream trap, and it defeats
the whole point of a findability page.

---

## 19. Name renders as "Chua Han Chong" everywhere

**Chosen:** `first_name: Chua`, `middle_name: Han`, `last_name: Chong`.

**Why:** al-folio joins the three as "first middle last" for the footer, the Open Graph
and schema.org author name, and the blog citation block. The semantically correct split
(given name "Han Chong", surname "Chua") made those read "Han Chong Chua" while the page
heading read "Chua Han Chong" — two different names on one page, which is bad for both
readers and search. Every handle the decider controls (CV header, GitHub, LinkedIn) uses
Chua-first.

**Known cost:** the surname really is Chua, so `citation.liquid` will emit
"Chong, Chua Han" on blog posts. The blog is unpublished. If publishing under
"Han Chong Chua", swap the keys back — noted inline in `_config.yml`.

---

## 20. Build locally, decider pushes

**Chosen:** the site is built in the working tree; creating the GitHub repository and pushing
is left to the decider.

**Why:** publishing puts a real name and photo on a Google-indexable page. That is the decider's
call to make after reviewing the result, not a side effect of building it.
