# dhbw-presentation

DHBW corporate design theme for [touying](https://typst.app.universe/package/touying)
slide decks. The layout follows the official DHBW PowerPoint template: red edge
bar on the left, slide title top left next to the DHBW wordmark, footer with the
`date · authors · topic` line on the left and beamer-style progress dots grouped
by topic on the right. DHBW red (`#e2001a`), slate grey text (`#5c6971`),
Source Sans 3 for body text, Fira Code Nerd Font for code and Fira Math for
equations.

> The included DHBW logo is a trademark of the Duale Hochschule
> Baden-Württemberg and is only provided for DHBW-related documents. The
> package itself is not affiliated with or endorsed by the DHBW.

## Usage

```typst
#import "@preview/dhbw-presentation:0.1.0": *

#show: dhbw-theme.with(
  aspect-ratio: "16-9",
  config-info(
    subtitle: [Elektrotechnik],
    author: ("Max Mustermann", "Erika Musterfrau"),
    date: datetime(year: 2026, month: 10, day: 5),
  ),
  // appended as the final slide: a bibliography() with #cite works too
  sources: ("Typst-Dokumentation, typst.app/docs",),
)

// the first topic renders the title slide, every following one a chapter slide
= Topic
== Slide title
- Body

= Next chapter
== Another slide
```

**Title slide.** With `auto-title-slide: true` (the default) the *first*
level-1 heading renders it: the heading text becomes the title, subtitle,
authors and the plain date (`5. Oktober 2026`) come from `config-info`. Set
`config-info(title:)` and the title slide shows *that* title instead, while the
first heading gets a chapter slide of its own right after it — so deck title and
first chapter sit on consecutive transition pages (the chapter slide is skipped
when both texts are identical). A deck without level-1 headings gets a title
slide prepended from `config-info(title:)` instead. Set `auto-title-slide: false`
and call `#title-slide(...)` yourself if
you want the title elsewhere or need extra `meta` lines (supervisor, course, ...);
`meta` entries must be plain strings or content — markup such as
`[3. Semester]` inside a string would be parsed as an ordered list.

**Chapter slides.** Every level-1 heading renders a `chapter-slide` (layout of
the title slide: big chapter title, full logo, red
`www.dhbw.de` — no presenters, no date); the first heading only when it is not
already the title slide. Title and chapter slides are
transition pages: no header, no footer, no progress dot. Label a heading with
`<touying:skip>` to suppress its slide, or pass `chapter-slides: false` to turn
all of them off.

**Footer.** `date · authors · topic` on the left — the topic is
`config-info(title:)` if you set it, otherwise the first heading — and the
indicator on the right. `progress:` selects it: `"dots"` (default) draws the
topic progress, `"number"` the red logical slide number left of the footer
line, `"both"` the two in parallel and `none` nothing at all — use `"number"`
for decks with hundreds of slides, where one dot per slide stops being
readable. Pass `footer-text:` to `dhbw-theme` to
replace the line or `none` to hide it. There is no physical PDF page number: a
slide built with `#pause` is one logical slide, so its indicator does not move
while the physical page does.

Headings drive the layout:

| Level | Meaning |
| ----- | ------------------------------------------------------- |
| `=` | Chapter — title slide (first), chapter slide (every other one, plus the first when `config-info(title:)` differs), groups the progress dots |
| `==` | Slide title — printed top left in the header band |
| `===` | Sub-heading inside a slide |

Slides are separated by `---` if you need more than one slide per topic.

## Sources

`sources:` appends one final slide titled `sources-title` (default
`[Quellen]`). It accepts

- `bibliography("sources.yml")` — Hayagriva YAML or BibTeX, citations in the
  slides with `@key`/`#cite`, the built-in bibliography title is dropped;
- an array or a single string — rendered as a bullet list;
- any other content — rendered unchanged.

The slide is an appendix: it closes the last topic in the footer, but it is not
part of the progress dots.

The slide is only created when it has something to show: an empty array or a
`bibliography()` without a single `@key`/`#cite` in the deck adds nothing (a
`bibliography(.., full: true)` lists every entry by itself and always renders).

## API

### `dhbw-theme`

| Argument | Default | Meaning |
| -------------- | ---------------------------------------- | ---------------------------------------- |
| `aspect-ratio` | `"16-9"` | `"16-9"` or `"4-3"` |
| `font` | `"Source Sans 3"` | typeface |
| `code-font` | `"FiraCode Nerd Font Mono"` | inline and block code |
| `math-font` | `"Fira Math"` | equations, and fallback for body symbols like `#sym.dot` |
| `size` | `24pt` | base font size |
| `primary` | `dhbw-red` | accent colour (alerts, rules, active dot) |
| `margin` | `(x: 2.2em, top: 3.5em, bottom: 1.5em)` | page margins |
| `logo` | `auto` | DHBW wordmark in the header, `none` hides |
| `footer-text` | `auto` | line left in the footer, `auto` = `date · authors · topic` |
| `progress` | `"dots"` | footer indicator: `"dots"`, `"number"` (red logical slide number left of the line), `"both"` or `none` |
| `link-text` | `[www.dhbw.de]` | red link bottom left of title and chapter slides, `none` hides it |
| `link-url` | `"https://www.dhbw.de"` | target of that link |
| `auto-title-slide` | `true` | the first `=` renders the title slide, `false` for an explicit `#title-slide(..)` |
| `chapter-slides` | `true` | every `=` after the first renders a `chapter-slide` |
| `sources` | `none` | final slide: `bibliography(..)`, list of strings or content |
| `sources-title`| `[Quellen]` | heading of that slide |

Additional named arguments are passed straight to touying
(`config-info`, `config-common`, ...).

### Functions

- `title-slide(title, subtitle, author, meta, date, link-text, link-url, logo-height)` — every argument defaults to `auto` and resolves to the
  matching `config-info` field (the link to `dhbw-theme(link-text: .., link-url: ..)`): authors as one line each, `date` printed as
  `5. Oktober 2026` via `format-date`, `meta` for extra lines; full logo top
  right, red `www.dhbw.de` bottom left (`link-text: none` hides it)
- `chapter-slide(title, config, link-text, link-url, logo-height)` — the
  chapter divider in the same layout without subtitle, authors or date;
  `dhbw-theme` inserts it automatically, `#chapter-slide[Title]` also works on
  its own
- `focus-slide[...]` — full-bleed red slide for key messages
- `progress-dots(..)` — the footer progress, also usable standalone: equal
  sized dots, one per logical slide, grouped by topic; `none` on appendix
  slides
- `slide-number(..)` — red logical slide number for the footer: counts content,
  chapter and appendix slides (chapter slides count silently, they show no
  footer), starts at 1 after the title slide
- `format-date(date, lang: "de")` — German month names (`5. Oktober 2026`),
  because typst cannot localise `datetime.display()` yet
- `dhbw-logo(height: 1.3em)` — DHBW wordmark for the header
  (`assets/dhbw-wordmark.svg`, derived from the official logo by clipping the
  claim lines — regenerate it if `assets/dhbw.svg` ever changes)
- `dhbw-logo-full(height: 3.4em)` — wordmark with the claim lines, for the
  title slide
- colours: `dhbw-red`, `dhbw-red-dark`, `dhbw-slate`, `dhbw-ink`, `dhbw-gray`,
  `dhbw-gray-mid`, `dhbw-gray-faint`, `dhbw-white`

## Local development

The example imports the theme from `../src/lib.typ`, so it needs a root above
`example/`:

```sh
typst compile --root . example/main.typ     # from the package root
typst compile --root . dhbw-presentation/example/main.typ   # from the repo root
```

Alternatively point the package cache at the checkout:

```sh
typst compile --package-path <dir-with-preview-folder> example/main.typ
```

`<dir-with-preview-folder>` must contain `preview/dhbw-presentation/0.1.0`
pointing at the package root.

## Publishing

1. Bump `version` in `typst.toml`.
1. Push to GitHub.
1. Open a pull request at [typst/packages](https://github.com/typst/packages)
   under `packages/preview/dhbw-presentation/<version>`.
