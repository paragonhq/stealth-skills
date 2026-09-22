---
name: polish
description: The last ten percent, as a checklist. Favicons and social images, document titles, selection and scrollbar colours, focus rings, keyboard hints, tabular numbers, icon consistency, image dimensions, external link markers, anchor offsets, the 404 page, reduced motion and hover gating, tab title state, and the small fixes that separate a finished interface from a working one. Use when asked to polish, finish, ship, "make it feel done", or before a release.
---

# polish

Polish is not adding things. It is the list of things that are already wrong that nobody has looked at yet. Run it top to bottom; fix everything you find; do not skip an item because it seems small. Small is the point.

## Document and metadata

- [ ] `<title>` is specific per page: "Settings — App", not "App". Updates on client navigation.
- [ ] Title reflects state where useful: an unread count, an unsaved dot ("• Untitled").
- [ ] Favicon: SVG with a dark-mode variant via `prefers-color-scheme` inside the SVG, plus a 32px PNG fallback and a 180px apple-touch-icon.
- [ ] Open Graph and Twitter images exist, are 1200×630, and show the page's title in the site's type, not a generic logo.
- [ ] `theme-color` meta for both schemes so the browser chrome matches.
- [ ] `lang` on `<html>`. `color-scheme` on the root.
- [ ] Canonical URLs; no duplicate content at `/page` and `/page/`.
- [ ] A 404 page in the app's frame, with a way back and a search if the app has one.

## Text

- [ ] Sentence case in every button, label, heading and menu item.
- [ ] No orphan words on titles: `text-wrap: balance`.
- [ ] Tabular numerals on every number in a column, counter, price, timestamp or shortcut.
- [ ] Real apostrophes and quotes (’ “ ”), en dashes for ranges, ellipsis character not three dots.
- [ ] Empty states, error messages and loading text are written, not placeholder.
- [ ] No "Lorem", no "TODO", no `console.log` in production.
- [ ] Dates are formatted for the locale, relative when recent ("4m ago"), with the absolute time on hover.

## Colour and surface

- [ ] `::selection` set to match the palette.
- [ ] Scrollbars themed: `scrollbar-width: thin; scrollbar-color`.
- [ ] Focus ring visible on every surface: test on the darkest and lightest backgrounds.
- [ ] Both themes reviewed on every screen. No `#fff` text on `#000`.
- [ ] No flash of the wrong theme on load.
- [ ] Form controls, `<select>` popups and date pickers match the theme (`color-scheme`).
- [ ] Disabled states are visibly disabled but still legible.

## Layout

- [ ] Nothing shifts on load: images have dimensions, fonts have metric fallbacks, skeletons match content.
- [ ] Sticky header offset applied to anchors and focus targets (`scroll-margin-top`).
- [ ] No horizontal scroll at 375px.
- [ ] Side gutters 16px on phones, never 0.
- [ ] Safe areas respected for fixed bottom elements.
- [ ] Long content tested: the longest name, the longest error, 0 items, 10,000 items.
- [ ] Scrollbar gutter compensated when a dialog locks scroll (no 15px jump).

## Interaction

- [ ] Every button has `:active` feedback.
- [ ] Hover effects gated to `@media (hover: hover)`.
- [ ] Every icon-only control has an `aria-label` and a tooltip.
- [ ] Keyboard shortcuts shown somewhere visible: tooltips, menu items, a footer hint.
- [ ] Copy actions confirm inline (icon swap) and handle clipboard failure.
- [ ] External links open in a new tab with an arrow marker and `rel="noopener"`.
- [ ] Links have `cursor: pointer`; drag handles have `grab`.
- [ ] Escape closes the top layer; focus returns to the trigger.
- [ ] Double submit prevented.
- [ ] Toasts pause on hover and when the tab is hidden.

## Motion

- [ ] `prefers-reduced-motion` handled globally and nothing is hidden behind an animation that never runs.
- [ ] No `transition: all`.
- [ ] Entrances ease out; exits are shorter.
- [ ] Nothing animates on every re-render (check list filters, tab switches, sorts).
- [ ] Page-load animations happen once, on the first paint, not on every navigation back.
- [ ] No animation on keyboard-driven actions.

## Icons and imagery

- [ ] One icon set, one stroke width, `currentColor`.
- [ ] Icons optically aligned with text (usually 1px up).
- [ ] Decorative icons `aria-hidden`.
- [ ] Images: `alt` written, `loading="lazy"` below the fold, modern formats, `srcset` for retina.
- [ ] Avatars have a fallback (initials on a neutral surface) that matches the size exactly.
- [ ] Favicons for external items fetched from the site's own declared icon, with a fallback letter tile.

## Performance you can feel

- [ ] Fonts preloaded (the two weights above the fold), `font-display: swap` with metric overrides.
- [ ] Route transitions do not white-flash; the frame persists.
- [ ] Interaction to next paint under 100ms on a throttled CPU for the primary actions.
- [ ] Large lists virtualised; large images not decoded on the main thread on scroll.
- [ ] `content-visibility: auto` on long below-the-fold sections.
- [ ] No layout thrash from measuring in loops.

## Machine readers

- [ ] Headings form an outline (one `h1`, then `h2`…).
- [ ] Landmarks: `header`, `nav`, `main`, `footer`, with `aria-label` when there is more than one of a kind.
- [ ] A skip link to main content.
- [ ] Structured data if the content is a list, article or product.
- [ ] `robots`, `sitemap`, and if the site is a resource, `llms.txt`.

## The no-JavaScript and slow-network pass

- [ ] Every word and link is in the HTML. Turn JavaScript off and read the page.
- [ ] Elements hidden for a reveal animation are visible without JS (`html:not(.js) [data-reveal] { opacity: 1 }`).
- [ ] Throttle to Slow 3G once and watch the load order. Nothing critical arrives last.

## Ship gate

The interface is finished when you can:

1. Tab through every screen without a mouse.
2. Use it at 375px with one thumb.
3. Switch theme on every screen and nothing looks wrong.
4. Turn on reduced motion and nothing is missing.
5. Read every state: loading, empty, error, done.
6. Find no value that is not a token: colours, spacing, easing, duration, radius.

## From the catalogue

- [Details That Make Interfaces Feel Better](https://jakub.kr/writing/details-that-make-interfaces-feel-better) — the spirit of this list.
- [HTML Can Do That](https://chrisburnell.com/html-can-do-that) — behaviour that ships in HTML before JS.
- [web.dev](https://web.dev) — Core Web Vitals and the measurements behind "feels fast".
- [Favicon and OG tools](https://stealth.pm/tools#svg) — generate and check the assets.
- [Lighthouse, axe, and the checks](https://stealth.pm/tools#quality) — run before shipping.
- More at [stealth.pm/tools](https://stealth.pm/tools).
