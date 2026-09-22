---
name: surface
description: Colour, elevation and material for interfaces. Neutral palettes that survive dark and light mode, semantic tokens, borders versus shadows by theme, alpha overlays, contrast that passes, a single accent used sparingly, glass and blur budgets, theme switching without a flash. Use when building a palette, adding dark mode, fixing colours that look "muddy", "cheap", "flat" or "washed out", or when shadows and borders look wrong in one theme.
---

# surface

Colour in an interface is mostly the absence of colour. Get the neutrals and the elevation right and one accent will do the rest.

## Tokens first

Name colours by role, never by value. A palette that survives both themes has roughly this shape:

| Token | Role |
| --- | --- |
| `--page` | The outermost background, behind the app frame |
| `--frame` | The app surface |
| `--raised` | Cards, popovers, anything lifted one step |
| `--hover` | Hover fill for rows and items |
| `--line` | Default border |
| `--line-2` | Stronger border, focus rings, dividers that must be seen |
| `--fg` | Primary text and icons |
| `--fg-2` | Secondary text |
| `--fg-3` | Tertiary, meta, placeholders |
| `--fg-4` | Faint: decorative, disabled, dividers in text |
| `--accent` | One. For the primary action and focus, and nothing else |
| `--ok`, `--warn`, `--danger` | Status, muted, with a text-safe variant each |

Everything else derives. Components never reference a hex; they reference a token. Dark mode is a second set of values for the same tokens, not a second set of components.

## Neutrals

Near-black and near-white, both slightly warm or slightly cool, consistently. Pure `#000` and `#fff` are too hard; a dark theme on `#080808` with text at `#ededed` reads as considered where `#000`/`#fff` reads as unset.

A workable dark set:

```
page #080808 · frame #0d0d0d · raised #141414 · hover #161616
line #1c1c1c · line-2 #262626
fg #ededed · fg-2 #9b9b9b · fg-3 #6b6b6b · fg-4 #454545
```

And light:

```
page #f2f2f0 · frame #fbfbfa · raised #ffffff · hover #f1f1ef
line #ebebe8 · line-2 #dcdcd8
fg #151515 · fg-2 #5f5f5c · fg-3 #8d8d89 · fg-4 #b8b8b3
```

Steps are small on purpose. Surfaces differ by 2–4% lightness; that is enough for the eye to separate them without stripes. Test by taking a screenshot and squinting: you should see the structure, not a patchwork.

Build neutrals in OKLCH when generating them, so that steps are perceptually even and the hue stays constant as lightness changes.

## Dark mode is not inverted light mode

- Light mode lifts things with shadows. Dark mode lifts things with lighter surfaces and borders. A shadow on `#0d0d0d` is invisible.
- Dark surfaces get a 1px inset top highlight at 4% white to read as lit from above: `box-shadow: 0 1px 0 rgba(255,255,255,.04) inset`.
- Saturated colours glow on dark. Desaturate the accent 10–20% and lower its lightness for dark mode.
- Pure white text on pure black vibrates. Lower both.
- Images and illustrations may need a subtle dim (`opacity: .9`) or a dark-mode variant.
- `color-scheme: dark` on the root so form controls, scrollbars and the browser UI match.

## Borders vs shadows

| Theme | Card / popover edge |
| --- | --- |
| Dark | `border: 1px solid var(--line-2)` + inset highlight; a soft outer shadow only for floating layers: `0 12px 40px -12px rgba(0,0,0,.9)` |
| Light | `box-shadow: 0 1px 0 rgba(255,255,255,.9) inset, 0 12px 32px -14px rgba(0,0,0,.18)`; border only where the surface sits on the same colour |

Shadows are one layer, soft, with negative spread, in the direction of a single light source. Never `0 0 20px` (a glow), never multiple stacked blurs, never a coloured shadow in product UI.

Borders are 1px. Not 0.5px (it renders inconsistently), not 2px unless it is a focus ring.

## Alpha

Overlays, hover fills and dividers on unknown backgrounds use alpha, so they work on any surface:

- Hover fill: foreground at 4–6%
- Pressed fill: foreground at 8–10%
- Divider: foreground at 8–12%
- Backdrop behind dialogs: page colour at 60–70%, with `backdrop-filter: blur(4px)` at most
- Glass surfaces (`backdrop-filter`) are expensive; one per screen, never animated, never nested

## The accent

One hue. It goes on the primary button, the focus ring if the neutral ring is too quiet, links in running text, and the active state of a control. It does not go on icons, headings, borders, backgrounds or badges.

If everything is the accent, nothing is. Count the accent-coloured elements on a screen; if it is more than three, remove some.

Status colours are muted (a green at ~55% chroma, not a traffic light) and always paired with a word or icon. Colour is never the only signal.

## Contrast

- Body and secondary text: 4.5:1 against its surface.
- Large text (18px+ or 14px+ bold) and UI boundaries (icons, borders on inputs): 3:1.
- Tertiary text is allowed to sit at 3:1 only when it is not needed to use the interface.
- Faint (`--fg-4`) is decoration. Never put information in it.
- Check both themes. A grey that passes on white will fail on `#0d0d0d`.

Placeholders count as text if they carry the only label; then they must pass. Better: always have a visible label.

## Gradients, noise, texture

Product UI: none, or one very quiet radial behind a hero region at 2% opacity to give the page a light source. Marketing: allowed, and still one.

Noise or dither over a gradient hides banding and adds material; keep it at 3–6% opacity and tile a small SVG rather than a large PNG.

A backdrop pattern (grid, ticks, dots) at the `--line` level sits behind the frame and stops at its edge; it never runs under content.

## Selection, focus, scrollbars

```css
::selection { background: var(--fg); color: var(--frame); }
:focus-visible { outline: 1px solid var(--fg-2); outline-offset: 2px; }
* { scrollbar-width: thin; scrollbar-color: var(--line-2) transparent; }
```

These three lines are the difference between a themed app and a page with a theme applied to it.

## Theme switching

- Decide the theme before first paint with an inline script reading storage and `prefers-color-scheme`. A flash of the wrong theme is a bug.
- Store the choice; respect the system when there is no choice.
- Transition `background-color` on the root over ~400ms so the switch is not a strobe. Or use a View Transition to reveal the new theme from the toggle; either way, under reduced motion it is instant.
- Both themes are first-class. If the team only ever looks at dark, light will rot, and vice versa. Review in both.

## Marks of a cheap surface

| Sign | Fix |
| --- | --- |
| `#000` page, `#fff` text | `#080808` / `#ededed` |
| Cards with border *and* shadow *and* a different background | one of the three, two at most |
| Glow shadows (`0 0 24px accent`) | soft directional shadow or none |
| Every card a different tint | one surface token |
| Accent on icons and headings | neutrals; accent on the one action |
| Grey text at 2.5:1 "because it looks lighter" | `--fg-2` that passes; use size and weight for hierarchy |
| Gradient buttons | flat fill, border-only secondary, ghost tertiary |
| Dark mode with the same shadows as light | borders and inset highlights |

## From the catalogue

- [Radix Colors](https://www.radix-ui.com/colors) — scales with a defined step for each role, in both themes.
- [Open Props](https://open-props.style) — sensible defaults for shadows and neutrals.
- [Realtime Colors](https://www.realtimecolors.com) — see a palette on a real layout before committing.
- [Contrast checkers](https://stealth.pm/tools#contrast) — check it before someone else does.
- [Dark Mode Design](https://www.darkmodedesign.com) — sites that got dark right.
- [fffuel](https://www.fffuel.co) — SVG noise and grain generators.
- More at [stealth.pm/assets](https://stealth.pm/assets#colour-systems).
