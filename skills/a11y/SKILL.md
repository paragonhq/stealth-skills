---
name: a11y
description: Accessibility as part of building, not a pass afterwards. Semantic HTML first, keyboard order and focus management, visible focus, names for every control, ARIA only where HTML cannot, live regions, reduced motion, contrast, hit sizes, forms with linked labels and errors, headings and landmarks, inert layers, zoom to 200%, and how to test with a screen reader in five minutes. Use when building any interactive UI, when asked to make something accessible, or when a review needs an accessibility pass.
---

# a11y

Accessible interfaces are not a separate thing from good interfaces. Almost every rule here also makes the interface better for someone with a mouse and perfect vision. Treat it as craft, not compliance.

## HTML first

The element carries the behaviour. Use it:

| Want | Use | Not |
| --- | --- | --- |
| Something that does an action | `<button type="button">` | `<div onClick>` |
| Something that navigates | `<a href>` | `<button>` that calls `router.push` |
| A list | `<ul>`/`<ol>` with `<li>` | divs |
| A form | `<form>` with `<button type="submit">` | `onClick` on a button outside a form |
| A checkbox, radio, switch | `<input type="checkbox">`, `role="switch"` on a button | custom divs |
| A section with a heading | `<section aria-labelledby>` | a div with a bold paragraph |
| Emphasis | `<strong>`, `<em>` | `<b>`, `<i>` or a span with font-weight |
| A dialog | `<dialog>` or a primitive with `role="dialog"` | a fixed div |

A native element gets keyboard behaviour, focus, a role and a name for free. Every custom replacement has to earn all four back.

## Names

Every control has an accessible name. In order of preference:

1. Visible text inside it (`<button>Save changes</button>`).
2. A visible `<label for>` (inputs).
3. `aria-labelledby` pointing at visible text.
4. `aria-label`, only for icon-only controls, and it says what the control does, not what the icon is ("Copy link", not "Clipboard icon").

Images: `alt` that says what the image conveys, or `alt=""` if decorative. Icons inside labelled controls are `aria-hidden`.

Names are unique in context. Five "Edit" buttons in a list need "Edit *Q3 report*", either visibly or via `aria-label`.

## Keyboard

- Everything interactive is reachable with Tab in visual order. `tabindex` is `0` or `-1`, never positive.
- Custom widgets follow the ARIA pattern for their role: arrows within a menu, tabs, listbox, radio group; Tab leaves the widget.
- Escape closes the topmost layer only.
- Focus moves into a dialog when it opens, is trapped while open, and returns to the trigger when it closes.
- Content behind a modal is `inert`.
- A skip link is the first focusable element and is visible on focus.
- Single-key shortcuts do not fire while focus is in a text field; modifier shortcuts do not clash with the browser's.
- Roving tabindex or `aria-activedescendant` for composite widgets, so one Tab stop per widget.

## Focus visible

```css
:focus-visible { outline: 2px solid var(--fg); outline-offset: 2px; }
:focus:not(:focus-visible) { outline: none; }
```

- Visible on every surface colour. Test on the darkest and the lightest.
- Matches the element's radius.
- Never `outline: none` without a replacement of at least 3:1 contrast against both the element and its background.
- Rows and cards that are links show focus on the whole row, not on the text.

## ARIA, sparingly

The first rule of ARIA: do not use ARIA if HTML does it. Then:

- `aria-expanded` on the trigger of anything that expands. `aria-controls` pointing at it.
- `aria-current="page"` on the active nav link; `aria-current="location"` for in-page nav.
- `aria-pressed` for toggle buttons; `aria-checked` for switches and checkboxes; `aria-selected` for tabs and options.
- `aria-busy` on regions that are loading; `aria-disabled` when a disabled control must stay focusable.
- `aria-describedby` from an input to its error and hint.
- `aria-live="polite"` on a region that announces toasts and status. `assertive` only for errors that block.
- `aria-hidden="true"` on decorative icons, on visual duplicates (a word split into spans for animation gets an `aria-label` on the parent), and on the backdrop.
- Never `role="button"` on a div when a button would do. Never `aria-label` on a `div` with no role.

## Forms

- Every field has a visible label. Placeholders are hints.
- Required fields are marked, and the mark is explained once.
- Errors: appear after blur or submit, sit under the field, are linked with `aria-describedby`, and the first invalid field receives focus on submit.
- Error text says what is wrong and how to fix it, in words; colour and an icon accompany it, never replace it.
- Groups of radios and checkboxes are in a `<fieldset>` with a `<legend>`.
- `autocomplete` set so browsers and assistive tech can fill them.
- Submit works with Enter from any field.

## Colour and contrast

- Text 4.5:1; large text and UI boundaries 3:1; in both themes.
- Colour is never the only signal: status has an icon or word, links are underlined in running text, charts have patterns or labels.
- Placeholders and disabled text may sit below 4.5:1 only if nothing depends on reading them.
- Focus rings and selection colours checked against every surface.
- `prefers-contrast: more` bumps borders to `--fg-2` and removes translucency.
- `forced-colors: active` (Windows High Contrast): borders on controls that relied on background, `outline` for focus, no `background-image` for meaning.

## Motion

- `prefers-reduced-motion: reduce`: transitions and animations to near zero, smooth-scroll off, parallax off, autoplaying video paused, but nothing hidden. Elements that would have animated in are simply present.
- Nothing flashes more than three times a second.
- Autoplaying motion longer than five seconds has a pause control.

## Structure

- One `h1` per page; headings in order without skipping levels; a heading for every region a screen-reader user would want to jump to.
- Landmarks: one `main`, `header`, `footer`, `nav` (labelled if there are several), `aside`, `search`.
- Lists are lists. A row-based UI is a `<ul>` with `aria-label`.
- Tables are `<table>` with `<th scope>`, or a grid with proper roles. Not divs.
- `lang` on the document and on any passage in another language.
- Reading order in the DOM matches visual order. CSS `order` and `flex-direction: row-reverse` break it.

## Size and zoom

- Targets 24px minimum by WCAG, 44px in practice on touch.
- The page works at 200% browser zoom and at 320px width with no horizontal scroll and no lost content.
- Text spacing can be increased (line-height 1.5, letter-spacing 0.12em, word-spacing 0.16em) without clipping.
- Never disable pinch zoom.

## Timing

- Toasts stay at least 5 seconds, longer with an action, and pause on hover and focus.
- Session timeouts warn with time to extend.
- Nothing requires a timed response to use.

## Five-minute test

1. Unplug the mouse. Tab through the screen. Can you see where you are? Can you do everything?
2. Turn on VoiceOver (⌘F5) or NVDA. Navigate by headings (VO+⌘+H). Do the headings describe the page? Tab to controls: does each say its role, name and state?
3. Open a dialog. Is focus inside? Does Escape close it and put focus back?
4. Zoom to 200%. Does it still work?
5. Turn on reduced motion. Is anything missing?
6. Run axe. Fix every "serious" and "critical". Read the "moderate" ones; most are real.

## Marks of inaccessible UI

| Sign | Fix |
| --- | --- |
| `div onClick` | `button` |
| `outline: none` | `:focus-visible` ring |
| Icon button with no name | `aria-label` |
| Placeholder as label | visible label |
| Error in red text only | icon + text + `aria-describedby` |
| Modal without focus trap | dialog primitive, `inert` behind |
| Custom select | native `<select>` or a listbox primitive |
| Hover-only affordance | visible always, or on focus too |
| Motion that hides content under reduced motion | content visible, motion removed |
| Colour-only status | icon or word |

## From the catalogue

- [Sara Soueidan](https://www.sarasoueidan.com) — accessibility and SVG, written by someone who builds.
- [ARIA Authoring Practices](https://www.w3.org/WAI/ARIA/apg/patterns) — the keyboard and role spec for each pattern.
- [Inclusive Components](https://inclusive-components.design) — components built accessible from the start.
- [axe](https://www.deque.com/axe), [Polypane](https://polypane.app) — test in the browser you already have open.
- [Nielsen Norman Group](https://www.nngroup.com/articles) — usability research that overlaps almost entirely with this list.
- More at [stealth.pm/tools](https://stealth.pm/tools#quality).
