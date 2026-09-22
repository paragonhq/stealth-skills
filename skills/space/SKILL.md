---
name: space
description: Layout, spacing and rhythm. A spacing scale and how to stay on it, density modes, alignment including optical alignment, reading and app widths, gap over margin, grids that collapse well, container queries, sticky regions, scroll containers, safe areas, and using empty space as a tool. Use when building a page structure, fixing uneven spacing, making something "breathe" or "tighter", handling responsive layout, or when a screen feels cluttered or floating.
---

# space

Space is the cheapest material and the one generated interfaces waste most. Uneven spacing is the first thing an experienced eye sees and the last thing a model fixes.

## The scale

Everything sits on a 4px grid. The useful stops:

```
2 · 4 · 6 · 8 · 10 · 12 · 16 · 20 · 24 · 32 · 40 · 48 · 64 · 96
```

Choose a value from this list, never in between. If 14px "looks right", the neighbouring elements are wrong; fix them and 12 or 16 will look right.

Related things sit closer than unrelated things. A label to its input: 6. Two inputs in a form: 16. Two sections of a form: 32. The ratio between levels is what makes structure visible without lines.

## Density

Decide it per surface, not per component:

| Surface | Row height | Padding inside | Between groups |
| --- | --- | --- | --- |
| Dense list / table (tools, admin) | 28–32px | 8–10px horizontal | 24px |
| Comfortable list (settings, feeds) | 40–48px | 12–16px | 32px |
| Reading | — | 0, the measure does the work | 40–64px |
| Marketing | — | 24–40px | 96px+ |

A dense tool with 48px rows feels like a toy. A settings page with 28px rows feels like a spreadsheet. Match the density to how long someone looks at each item.

## Widths

- App frame: 1120–1280px max, centred, with a visible edge (border or background change) so the frame reads as an object on the page.
- Reading measure: 55–70ch, set on the text block.
- Sidebars: 240–280px. Narrower and labels truncate; wider and the content column suffers.
- Forms: 400–520px. A full-width text input on a 1400px screen is a mistake.
- Dialogs: 400px (confirm), 520px (form), 720px (content). Never full-width on desktop.

## Gap, not margin

Space between siblings is the parent's job. Use `gap` in flex and grid layouts; children carry no outer margin. It removes the "last child has extra margin" class and the "first child is too high" bug, and it means a component can be dropped anywhere without bringing its spacing with it.

Margins are for one thing: separating a block from what follows in flowing text (`margin-bottom` on paragraphs in an article).

## Alignment

- Text aligns to text: the left edge of a title, its description and the rows beneath share one x. Icons sit outside that line, not inside it.
- Icons and text on a row align on the icon's optical centre, which is usually 1px above the geometric one. Nudge with `translateY(-0.5px)` or `margin-top: -1px`, not with padding on the text.
- Numbers right-align in columns; text left-aligns; never centre a column of anything you compare.
- Buttons in a row share a baseline and a height. A primary and a secondary button never differ in height.
- Optical alignment beats geometric: a play icon looks centred when it is slightly right of centre; a rounded pill's text looks centred when it is slightly left.

## Rhythm

Vertical spacing between sections increases with the level of the break:

- Rows inside a section: 0 (rows are the spacing) or 2–4px
- Section header to its rows: 8–12px
- Between sections: 24–32px
- Page header to first section: 28–40px

Consistent rhythm is what makes a long page feel calm. Vary content, not spacing.

## Responsive

Fluid first, breakpoints second:

1. Let the content wrap and the grid collapse (`repeat(auto-fill, minmax(280px, 1fr))`) before adding any media query.
2. Two or three breakpoints at most, tied to what breaks: sidebar collapses (~1024px), columns stack (~640px). Not "sm/md/lg/xl/2xl" because the framework has them.
3. Use container queries for components that live in different-width slots. A card should not know the viewport.
4. Side gutters: 16px on phones, 20–28px on desktop. Never 0.
5. Nothing scrolls horizontally except things meant to (a carousel, a code block with `overflow-x: auto`).

Test at 375px, 768px, 1024px and 1440px. Then resize slowly between them and watch for jumps.

## Sticky and fixed

- A sticky header is 44–52px, translucent (`background: color-mix(...)` at 80% + `backdrop-filter: blur`), with a bottom border. Content beneath uses `scroll-margin-top` matching the height so anchors land below it.
- Sticky sidebars are `position: sticky; top: 0; height: 100dvh` with their own scroll region for overflow, using `overscroll-behavior: contain`.
- Fixed bottom bars on phones sit above the home indicator: `padding-bottom: env(safe-area-inset-bottom)`.
- Nothing is fixed on top of reading content on a phone except a bar under 56px.

## Scroll containers

- Inner scroll regions are explicit (`min-height: 0` on the flex child, `overflow-y: auto`), have a visible edge, and get `overscroll-behavior: contain`.
- Only one axis scrolls at a time in any region.
- A scroll region that is not obviously scrollable needs a cue: a fade at the bottom edge, or content visibly cut at the boundary.

## Empty space as a tool

A page with one form and a lot of space around it reads as focused. The same form crammed against a sidebar and a header reads as cluttered. Before adding a card border to group things, try more space around the group and less inside it.

When an area feels empty *and wrong*, it is usually the width, not the space: the content is narrower than the region it sits in. Constrain the region, do not fill it.

## Grids for cards

`grid-template-columns: repeat(auto-fill, minmax(280px, 1fr))` with `gap: 8–16px`. Cards share one height per row (`align-items: stretch`) and the content inside aligns top, with actions pushed to the bottom by `margin-top: auto`. Never masonry for anything with actions.

## Marks of unconsidered space

| Sign | Fix |
| --- | --- |
| Values like 13px, 18px, 22px, 30px | Snap to the scale |
| Same 16px gap between everything | Hierarchy: 8 inside, 16 between, 32 between groups |
| Margins on components | `gap` on the parent |
| Card border to group three things | Space around them |
| Full-width inputs on desktop | 400–520px |
| Five breakpoints | Two, where it actually breaks |
| Icon vertically centred by padding | Align on optical centre, nudge 1px |
| Content jumping at a breakpoint | Fluid grid, then fewer breakpoints |

## From the catalogue

- [Ahmad Shadeed](https://ishadeed.com) — deep CSS layout write-ups with interactive figures.
- [Every Layout](https://every-layout.dev) — the layout primitives, composed.
- [Utopia](https://utopia.fyi) — fluid space and type scales.
- [Refactoring UI](https://www.refactoringui.com) — the spacing-hierarchy argument in pictures.
- [Refero](https://refero.design), [Mobbin](https://mobbin.com) — see how shipped apps set density.
- More at [stealth.pm/learn](https://stealth.pm/learn).
