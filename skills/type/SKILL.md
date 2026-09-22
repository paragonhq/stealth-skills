---
name: type
description: Set type like it was chosen, not defaulted. Typeface selection, size scales for product versus reading UI, line height and tracking by size, measure, hierarchy through weight and colour rather than size, tabular numerals, OpenType features, text wrapping, truncation, mono for data, font loading. Use when choosing fonts, building a type scale, fixing hierarchy, or when an interface looks "generic", "off" or "like a template" and the cause is text.
---

# type

Most interfaces are 90% text. If the text is defaulted, the interface is defaulted, and no amount of colour or motion hides it.

## Choosing the family

One sans for the interface. One mono for data, code, shortcuts and labels that should read as system. A display face only on marketing pages, and only if the sans cannot carry it.

Pick for the job:

| Job | Traits | Reach for |
| --- | --- | --- |
| Dense product UI | Neutral, tight apertures, good at 12–14px, tabular figures | Geist, Inter, SF-adjacent grotesks |
| Reading (docs, articles) | Larger x-height, wider spacing, optical sizing if available | Source-style humanists, or the same sans at 16px+ with looser leading |
| Data, code, labels | Mono with clear 0/O and 1/l, ligatures optional | Geist Mono, JetBrains Mono, Berkeley Mono |
| Display | Has an opinion; pairs by contrast with the sans, not similarity | one, chosen against the brand |

If the project already has a family, use it. Changing fonts is a redesign, not a fix.

Never: more than two families in product UI; a decorative face for body; a rounded face to "feel friendly".

## Two scales, not one

Product UI and reading UI want different bases. Pick which the screen is.

### Product scale (tools, dashboards, admin, lists)

| Role | Size | Weight | Leading | Tracking |
| --- | --- | --- | --- | --- |
| Page title | 20–22px | 500 | 1.15 | −0.02em to −0.03em |
| Section title | 14–15px | 500 | 1.3 | −0.015em |
| Row / body | 13px | 400 | 1.45 | −0.003em |
| Secondary | 12–12.5px | 400 | 1.5 | 0 |
| Label, meta, mono | 10.5–11px | 400–500 | 1.3 | 0.04–0.1em if uppercase |
| Numbers in tables | body size, mono or tabular | 400 | — | 0 |

Body at 13px is the sweet spot for dense tools when the family is designed for it. Do not go below 10.5px for anything a person must read.

### Reading scale (docs, articles, settings with long copy)

| Role | Size | Weight | Leading | Tracking |
| --- | --- | --- | --- | --- |
| H1 | 28–36px | 500–600 | 1.1 | −0.025em |
| H2 | 20–24px | 500 | 1.2 | −0.02em |
| H3 | 16–18px | 500 | 1.3 | −0.01em |
| Body | 16–17px | 400 | 1.6 | 0 |
| Small | 14px | 400 | 1.5 | 0 |

Measure: 55–70 characters. Set `max-width` in `ch` on the text container, not on the page.

## Hierarchy without size

The mistake generated UI makes is making everything a different size. Three sizes are enough for most screens. Get hierarchy from:

1. **Colour**: four foreground steps. Primary text, secondary, tertiary, faint. Meta text is a lighter step at the same size, not a smaller size.
2. **Weight**: 400 and 500. 600 for a title, rarely. 700 almost never in a UI font: it fills in at small sizes.
3. **Space**: a title is separated from what follows by more space than sits between rows.
4. **Case and family**: mono uppercase with tracking for a section label reads as a label without being bigger or bolder.

A row can carry a name at 13px/500 in primary colour, a description at 12.5px/400 in secondary, and a domain at 10.5px mono in faint. Three levels, two sizes.

## Leading

Tighter as size grows. Roughly:

- 10–12px: 1.4–1.5
- 13–14px: 1.45
- 16–18px: 1.5–1.6 (reading), 1.4 (UI)
- 20–24px: 1.2–1.3
- 28px+: 1.05–1.15

Titles that wrap to two lines are where wrong leading shows first. Set it there, then check the single-line case still sits right.

## Tracking

Negative as size grows; slightly positive for small caps and mono labels.

- 24px+: −0.02em to −0.04em
- 14–20px: −0.01em to −0.02em
- 12–14px: 0 to −0.005em
- Uppercase labels at 10–11px: +0.06em to +0.1em
- Mono at any size: 0

Never track body text positive. Never letter-space a lowercase word.

## Numbers

Any number that sits in a column, changes over time, or is compared to another uses tabular figures:

```css
.tabular { font-variant-numeric: tabular-nums; }
```

Counters, prices, timestamps, row counts, percentages, keyboard shortcuts. Mono is the stronger version of the same signal; use it for values that are identifiers (IDs, hashes, hostnames) and tabular sans for values that are quantities.

Slashed zero (`zero` feature) in mono when 0 and O can be confused.

## OpenType

Turn on what the face was designed with, sitewide:

```css
html { font-feature-settings: "ss01", "cv11"; } /* per family: check its specimen */
```

Common ones worth checking: alternate `a` and `g` for a more neutral look, `cv11` single-storey a in Inter and Geist, `ss01` open digits, `case` for punctuation with caps, `tnum` (same as the CSS property above), `calt` for code ligatures if the team wants them.

## Rendering

```css
html {
  -webkit-font-smoothing: antialiased;
  text-rendering: optimizeLegibility;
}
```

Antialiased on macOS makes light text on dark backgrounds thinner and cleaner. On light backgrounds it is close to neutral. Set it once, at the root.

## Wrapping and truncation

- Titles and headings: `text-wrap: balance`. No more orphans.
- Paragraphs: `text-wrap: pretty` where supported.
- Rows that must stay one line: `truncate` (`overflow: hidden; text-overflow: ellipsis; white-space: nowrap`) with a `min-width: 0` on the flex child, or it will not truncate.
- Descriptions in cards: `line-clamp: 2`, never 3+; if three lines are needed, the layout is wrong.
- Do not truncate anything the user needs to act on (a name they must recognise, an error). Wrap it.
- Hyphenate long reading text with `hyphens: auto` and `lang` set; never in UI.

## Links in text

Underline, with `text-underline-offset: 3px` and `text-decoration-color` at the faint step, brightening on hover. Colour alone is not a link. In lists of items, the whole row is the link and it is not underlined.

## Labels, buttons, inputs

- Sentence case everywhere. "Save changes", not "Save Changes". The only uppercase is small mono section labels.
- Button text: 13px/500 in product UI, same as the row name. Never smaller than the body around it.
- Input text: 16px on touch devices or the browser zooms the page. Use the same size on desktop or the field will change size between them; 14–15px is a workable compromise with a media query for touch.
- Placeholder: tertiary colour, never as the only label.

## Loading fonts

- Self-host or use the framework's font loader; both give `font-display: swap` and no layout shift when combined with size-adjust.
- Preload only the weights you use above the fold. Two weights, not six.
- Variable font when the family offers one: one file, all weights, and animated weight if you ever want it (you probably do not).
- Fallback stack that matches metrics (`size-adjust`, `ascent-override`) so the swap does not reflow.

## Marks of defaulted type

| Sign | Fix |
| --- | --- |
| Five font sizes on one screen | Collapse to three; move hierarchy to colour and weight |
| Bold everywhere | 500 for emphasis, 400 default; bold is for one thing per screen |
| Title case buttons | Sentence case |
| 1.5 line-height on a 32px heading | 1.1 |
| Proportional digits in a table | `tabular-nums` |
| Orphan on a two-line title | `text-wrap: balance` |
| Grey text that fails contrast | Faint step is for decoration only; secondary text must pass 4.5:1 |
| Letter-spaced lowercase | Remove tracking; reserve it for uppercase mono |

## From the catalogue

- [Geist](https://vercel.com/font) — sans, mono and pixel families designed for product UI; the scale above assumes something like it.
- [Google Fonts](https://fonts.google.com), [Pangram Pangram](https://pangrampangram.com), [Klim](https://klim.co.nz), [ABC Dinamo](https://abcdinamo.com) — where good families come from.
- [Modular Scale](https://www.modularscale.com), [Utopia](https://utopia.fyi) — build a scale once instead of guessing.
- [Wakamai Fondue](https://wakamaifondue.com) — see which OpenType features a font actually has.
- [Better Web Type](https://betterwebtype.com) — the reading-scale fundamentals.
- More at [stealth.pm/assets](https://stealth.pm/assets#type) and [stealth.pm/tools](https://stealth.pm/tools#css).
