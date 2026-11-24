# urosconf

Quarto website for **uRos — The Use of R in Official Statistics**, the annual
international conference. Theme: Bootswatch [Quartz](https://bootswatch.com/quartz/)
with light overrides in `styles/custom.scss`; identity (name, links, logo) in
`_brand.yml`.

```bash
quarto preview   # live preview
quarto render    # build into _site/
```

## Layout

```
_quarto.yml               site config: navbar, footer, theme (quartz + brand + custom.scss)
_brand.yml                brand.yml: name, links, navbar logo (current edition's hex)
styles/custom.scss        cards, speaker rows, logo chips, photo grid, contrast fixes
_templates/
  event-cards.ejs         card-grid listing template (conferences + events)
_includes/                text shared across editions, pulled in with the include shortcode
  code-of-conduct-{en,fr}.qmd
  gdpr-{en,fr}.qmd        reads page metadata: contact.secretariat, gdpr.controller
assets/                   site-wide files reused across years (logos, flags, favicon = current hex)
index.qmd                 home page (hero + 4 most recent editions)
about.qmd                 history of uRos, hosting, Code of Conduct
conferences/
  _metadata.yml           defaults for every conference page (banner, TOC, lightbox)
  index.qmd               archive listing: */index.qmd + archive.yml, sorted by year
  archive.yml             editions still hosted on r-project.ro (one entry per year)
  _template/index.qmd     skeleton for a new edition (ignored by Quarto)
  2026/
    index.qmd             main page (EN)          old URL /conference2026.html redirects here
    fr.qmd                French translation      /conference2026_FR.html
    live.qmd              agenda, map, photos     /conference2026-live.html
    _photos.qmd           photo galleries included by live.qmd
    slides.qmd            slides of all talks, by day and session (links to slides/ or Google Drive)
    slides/<session>/     self-hosted slide files (PDF, HTML decks), e.g. slides/a1/hauke.pdf
    images/ files/ photos/
events/
  index.qmd               listing of workshops/trainings: */index.qmd + archive.yml
  archive.yml             past workshops (2015, 2016)
  _template/index.qmd     skeleton for a new event
```

Anything starting with `_` is never rendered on its own, so templates and
includes can live next to real pages.

## Adding a new conference edition

1. `cp -r conferences/_template conferences/2027` and fill in the front matter.
   The listing card and the facts strip at the top of the page read these fields:
   `year`, `dates`, `location`, `venue`, `host`, `event-format`, `description`,
   `image`, `image-alt`. The shared GDPR text reads `contact.secretariat` and
   `gdpr.controller`.
2. Put images in `conferences/2027/images/` and PDFs in `files/`. Shared logos
   go in `assets/logos/` and are referenced as `/assets/logos/...`.
3. In `_quarto.yml`, point the *Conferences* menu (and the home page buttons in
   `index.qmd`) at the new edition. In `_brand.yml`, swap `hex-current` to the
   new hex sticker.
   Regenerate the favicon from the new hex:
   `convert images/uros2027-hex.png -trim +repage -define icon:auto-resize=64,48,32,16 ../../assets/favicon.ico`
4. For a translation, add `fr.qmd` with `lang: fr` (Quarto translates the UI
   strings) and enable the `.lang-switch` block on both pages.
5. For a live page, copy `2026/live.qmd`. Photos go in `photos/dayN/`, with one
   `.photo-grid` block per session in `_photos.qmd`. Shrink them first, e.g.
   `mogrify -auto-orient -resize 1400x1400\> -strip -quality 80 *.jpg`.

## Slides

`conferences/2026/slides.qmd` lists every talk in programme order with pill links
(`[PDF](slides/a1/x.pdf){.slide-btn}`). PDFs and HTML decks up to ~17 MB are
copied into `slides/<session>/` with short `author.ext` names. PowerPoint files,
videos and larger files stay on the
[shared Google Drive folder](https://drive.google.com/drive/folders/1iYtCNkHRpCJyFSfVuP6ymE_M6E_Sf4bE)
and are linked as `https://drive.google.com/file/d/<id>/view` with
`{.slide-btn .drive}`. Talks without slides get `[No slides shared]{.no-slides}`.
`resources: [slides/]` in the front matter makes Quarto copy the HTML decks as-is.

## Migrating an older edition

Create `conferences/<year>/index.qmd` as above, add `aliases: [/conference<year>.html]`
so the old URL redirects, and **delete that year's entry from
`conferences/archive.yml`** so it doesn't show up twice.

## Adding an event

`cp -r events/_template events/2027-03-short-name` and edit. Events without
their own page (e.g. hosted elsewhere) can go in `events/archive.yml` with a
`path:` to the external page.

## Listing fields (`_templates/event-cards.ejs`)

`title`, `year`, `dates`, `location`, `host`, `event-format`, `description`,
`image`, `image-alt`, `path` (optional; YAML items without it render unlinked),
`legacy: true` (adds an "archived page" badge).

## Migration notes (uRos2026)

Migrated from <https://r-project.ro/conference2026.html>, `_FR.html` and
`-live.html`. The text is verbatim except for formatting. Deliberate changes:

- Broken Markdown lists (literal `*` / `-` after line breaks) became real lists.
  Important dates and the live agenda are now tables, and the agenda is tabbed by day.
- Typos fixed: "INSSE - France" → "INSEE - France" (organisers),
  "This chaptersets" → "This chapter sets", "forStatistical" → "for Statistical",
  "l'Inseede l'Insee" → "l'Insee" (FR).
- Dropped: the defunct Universal Analytics snippet, the Flag Counter widget,
  and the duplicated `day2_12` photo.
- Photos were resized to a maximum of 1400 px (50 MB → 21 MB). The group photo
  is kept at 3000 px.

### Known content issues kept from the source (please review)

- **FR GDPR text** names *Statistique Canada* as data controller (left over
  from 2022; the EN page says Romanian NSI). Fix via `gdpr.controller-fr-de`
  in `conferences/2026/fr.qmd`.
- **FR call for abstracts** still says "The submission website is TBA soon."
- **Live agenda, Wed 18 Nov**: "Tutorial session II" is listed as 09:00–10:30.
  It is probably 11:00–12:30.
- "Presentations will be uploaded on a separate page": there is no link yet.
