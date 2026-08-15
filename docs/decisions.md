# Decision record

Why this site is the way it is. Read before changing something that looks odd.

Decider: Chua Han Chong. Started 2026-08-14, last revised 2026-08-15.
Numbering is stable - `glossary.md` cites decisions by number.

---

## 1. Purpose: findability page, not a research showcase

A canonical "this is me" page: bio, research interests, links, CV. The PhD started
2026-08-11 and there are no papers yet; this converts into a publications page for free
as output lands.

---

## 2. Hosting: GitHub Pages user site

`chuahanchong.github.io`. Free, no infrastructure. A custom domain is one `CNAME` file
away, so deferring it locks in nothing.

---

## 3. Stack: al-folio v1.2

Applied via "Use this template" semantics (clone, strip `.git`, fresh `git init`) rather
than a fork, so there is no upstream link to open a pull request against by accident.

v1.x is a thin starter over versioned plugin gems, so most files here are YAML and
Markdown, and upgrades are a gem bump rather than a merge.

---

## 4. Local preview: Docker

```bash
docker compose up
```

Serves <http://localhost:8080> from the `amirpourmand/al-folio` image, with the repo
bind-mounted and auto-regeneration on. Upstream marks the native Ruby setup "legacy, no
longer supported", so this is the documented path.

The native path ran the build here through 2026-08-14, and its `vendor/bundle` (180M)
and `.bundle/config` were deleted on 2026-08-15 once Docker was verified to serve `/`
and `/cv/` without them. Homebrew `ruby` and `imagemagick` are still installed and are
no longer needed by this repo.

Preview is a local convenience only; the live site is built by GitHub Actions and the
laptop can be off.

---

## 5. Sections: all built, only About and CV published

`publications`, `projects`, `blog`, `news`, `repositories`, `teaching` carry
`published: false`. Turning one on later is a one-word change.

**Use `published: false`, not `nav: false`.** `nav: false` only hides the navbar link -
the page still builds, still has a URL, and still lands in `sitemap.xml`, so Google can
index an empty page.

---

## 6. CV: one source, two outputs

`_data/cv.yml` produces both `/cv/` and the PDF, via
`.github/workflows/render-cv.yml`. Never hand-edit the PDF; CI commits a regenerated one
back to `main`, so pull after any `cv.yml` push.

---

## 7. Positioning: researcher-first, industry as credibility

Lead with the PhD and the research area; nine years of industry follows as the reason
those questions are interesting, not as a separate career.

The source CV was written for industry ("Accomplished AI enthusiast", a percentage on
every bullet). The site keeps the substance and the numbers, reworded.

---

## 8. Research framing: embodied AI with spatial intelligence

Stated as a research area, not a thesis topic - that ages better through year-1 scope
changes. Delivered through real-to-sim-to-real transfer and continual learning.

The order matters and was chosen deliberately: spatial intelligence is the *means*,
embodied AI the *end*. Not "embodied AI and spatial intelligence".

---

## 9. Visual identity: deep teal, light default, dark toggle

`#0f766e`. Dates slowly, reads serious, and leaves the colour budget to figures and
video rather than site chrome. Not NTU brand colours - those read as a department page
and stop being yours at graduation.

---

## 10. Links: four live, three scaffolded

Live: email, GitHub, LinkedIn, X. Commented out in `_data/socials.yml`: ORCID, Google
Scholar, Hugging Face - an icon linking to an empty profile is worse than no icon.

ORCID is worth registering at zero papers: free, permanent, accumulates from the first
submission. Hugging Face has no jekyll-socials key and needs `custom_social`.

---

## 11. Email: NTU address only, on this site

`CHUA1163@e.ntu.edu.sg`. An institutional address signals a real researcher at a real
lab and fares better in academic spam filters. No personal address anywhere on the site.

Scope: **this site only.** The GitHub profile keeps `chuahanchong93@gmail.com` as its
public email, deliberately. Do not "fix" it to match.

The address dies at graduation. The fix is a custom domain with a forwarding alias
(decision 2), deferred.

---

## 12. Publications and Projects ship empty

No seeded content; `papers.bib` holds a commented template only. Cost: the
jekyll-scholar pipeline is unexercised, so the first real paper is the first time it
runs. The template is there to shorten that day.

---

## 13. Accent set by overriding the gem's `main.scss`

`assets/css/main.scss` is a local copy of al_folio_core's, adding `$purple-color: #0f766e`
to the existing `@use "variables" with (...)` call - the only configuration point the gem
exposes.

**Upgrade liability.** The `@use` list is now frozen against the gem. A release that adds
or renames a partial silently drops those styles until this file is re-synced against
<https://github.com/al-org-dev/al-folio-core/blob/main/assets/css/main.scss>.

---

## 14. RenderCV design lives in `_data/cv.yml`, not `design.yaml`

`assets/rendercv/design.yaml` is **inert**. RenderCV applies a design overlay only with
`--design`, which al-folio's workflow never passes. Verified: a bogus key there produced
neither an error nor any change in output.

---

## 15. RenderCV pinned to the 2.x line

`rendercv[full]>=2.8,<3`. al-folio ships it unpinned, and upstream's own demo `cv.yml`
fails to render against current RenderCV - 2.x removed the top-level `cv.label`,
`cv.image`, `cv.summary`, and `cv.address` keys the demo still uses. Do not add them back.

---

## 16. Upstream contributor tooling removed

Deleted the agent instruction files, issue and PR templates, and every workflow except
`deploy.yml`, `render-cv.yml`, and `upgrade-check.yml`. Those files govern contributing
*to al-folio*, and the CI they carry guards a template project, not a homepage.

`upgrade-check.yml` stays - it reports new al-folio releases. The unused `giscus:` block
in `_config.yml` stays too: `deploy.yml` runs `yaml-update-action` against `giscus.repo`
and fails if the key is absent.

---

## 17. CV section order: fixed by a template override

`al_folio_cv`'s `render.liquid` hard-codes Experience first, then loops the rest in file
order, so `/cv/` disagreed with the PDF. `_includes/cv/render.liquid` is a local copy
with that hoist removed, plus Professional Summary rendered as a paragraph instead of a
bullet. Both now follow `cv.yml` order.

Same upgrade liability as decision 13: re-diff against the gem on upgrade.

**Do not rename a CV section to reorder it.** Unknown section names fall through to a
generic renderer handling only `bullet` and `label` entries, so the entries vanish from
the page while still appearing in the PDF. Valid names and their fields are documented at
the top of `_data/cv.yml`.

---

## 18. `site.description` must stay a single-line scalar

`al_folio_core`'s `metadata.liquid` interpolates it into the schema.org JSON-LD without
`jsonify`. A `>` folded block appends a newline, which becomes a literal control
character inside a JSON string and makes the whole structured-data block unparseable.
al-folio ships the folded form by default - an upstream trap that defeats the point of a
findability page.

---

## 19. Name renders as "Chua Han Chong" everywhere

`first_name: Chua`, `middle_name: Han`, `last_name: Chong`. al-folio joins the three as
"first middle last"; the semantically correct split made the footer and metadata read
"Han Chong Chua" against a page heading of "Chua Han Chong". Every handle the decider
controls uses Chua-first.

Cost: `citation.liquid` will emit "Chong, Chua Han" on blog posts. The blog is
unpublished; if publishing under "Han Chong Chua", swap the keys back.

---

## 20. Local edits, decider pushes

Every change is built and reviewed in the working tree first. Publishing puts a real name
and photo on a Google-indexable page, so each push is approved explicitly - not once,
and not as a side effect of building.

---

## 21. Accepted, not patched

- **`sameAs` in the JSON-LD begins with `null`** - seeded by `al_folio_core`'s
  `metadata.liquid`. Parses fine, consumers ignore it, and fixing it means overriding a
  250-line template.
- **Profile photo is 400x514**, soft on retina.
- **Coursera shows a different account name** than the certificates' owner, so anyone
  verifying a credential sees a mismatch. Fixable in Coursera settings, not here.
