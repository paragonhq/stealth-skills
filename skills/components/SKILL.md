---
name: components
description: Build a component the way a library author would. Decide primitive versus hand-rolled, cover the full state matrix, design the API by composition, style through data attributes, keep it controlled or uncontrolled deliberately, and pick the right library for the job from a short trusted list instead of installing whatever is first on npm. Use when creating or refactoring a button, input, menu, dialog, drawer, toast, tooltip, table, command palette, tabs, or any reusable piece of UI.
---

# components

A component is a contract: it looks the same everywhere, behaves the same everywhere, and does not need to be looked at to be trusted. Most generated components fail the third part.

## Primitive or hand-rolled?

Hand-roll only what has no behaviour: a card, a badge, a divider, a stat. Anything with focus management, keyboard interaction, positioning or ARIA uses a headless primitive.

| Need | Primitive | Why not hand-roll |
| --- | --- | --- |
| Dialog, sheet, popover, menu, select, tooltip, tabs, toggle, slider, combobox | Base UI, Radix, React Aria, Ark | focus trap, escape, outside click, typeahead, ARIA, portal, collision |
| Drawer with drag | Vaul | gesture physics, snap points, scroll locking |
| Toast | Sonner | stacking, swipe, timers, a11y announcements |
| Command palette | cmdk | filtering, keyboard, groups, a11y |
| Animated numbers | NumberFlow | digit columns, locale formatting |
| Data table | TanStack Table (headless) | sorting, virtualisation, column state |
| Forms | native + a small validation lib | constraint API, focus on first error |

The rule: if it has a spec in ARIA Authoring Practices, use a primitive that implements it.

## The state matrix

Every interactive component covers all of these before it is done. Write them down; check them off.

| State | Visual | Behaviour |
| --- | --- | --- |
| Default | — | — |
| Hover (hover devices only) | one-step change | — |
| Focus-visible | ring | — |
| Active / pressed | scale .97 or darken | — |
| Selected / checked / on | fill or accent | `aria-*` reflects it |
| Disabled | 50% opacity, no hover | not focusable, `aria-disabled` if it must stay in the tab order |
| Busy / loading | spinner in place, same width | ignores input, `aria-busy` |
| Error / invalid | border + message | `aria-invalid`, `aria-describedby` to message |
| Empty (for containers) | one line + one action | — |
| Read-only | text styling, no affordance | — |

Sizes: two or three (`sm`, `md`, `lg`). Variants: `primary`, `secondary` (border), `ghost`, `danger`. Not seven. Not `outline-gradient-xl`.

## Style from state, not from props

Style reads the element's actual state so the component and its CSS cannot disagree:

```tsx
<button data-state={open ? "open" : "closed"} data-size={size} data-variant={variant} aria-busy={busy || undefined} />
```

```css
.button[data-variant="primary"] { ... }
.button[data-state="open"] { ... }
.button[aria-busy] { ... }
.button:where(:disabled, [aria-disabled="true"]) { ... }
```

Primitives already emit `data-state`, `data-highlighted`, `data-disabled`; style those rather than mirroring them into React state.

## API by composition

Small parts that compose beat one component with forty props:

```tsx
<Dialog.Root>
  <Dialog.Trigger asChild><Button>Rename</Button></Dialog.Trigger>
  <Dialog.Content>
    <Dialog.Title>Rename file</Dialog.Title>
    <Dialog.Description>…</Dialog.Description>
    <Dialog.Close asChild><Button variant="secondary">Cancel</Button></Dialog.Close>
  </Dialog.Content>
</Dialog.Root>
```

Guidelines:

- `asChild` / `render` to put behaviour on the user's element instead of wrapping.
- Spread rest props onto the root element; forward `className` and `style`; forward refs.
- Boolean props for states, string unions for variants, no `type="..."` collisions with native attributes.
- Controlled and uncontrolled both work: `value` + `onValueChange`, or `defaultValue`. Never half of one.
- Event handlers are called `onX` and receive the native event or a plain value, never a synthetic object of your own invention.
- No `isLoading`/`loading` duplicates; one name, used everywhere.

## Tokens, not values

A component references tokens for colour, radius, spacing, easing and duration. If a component has a hex, a `0.3s` or a `cubic-bezier` inline, it has forked the system.

Radius is consistent per level: inputs and buttons 6–8px, cards and popovers 8–12px, dialogs 12–16px, pills full. Nested radii shrink: an element inside a 12px card uses 8px, not 12px.

## Positioning layers

Popovers, menus and tooltips: portal to `body`, position from the trigger with collision handling, `transform-origin` set from the resolved placement, and a scale/opacity entrance of ~160ms. Dialogs: centred, backdrop, scroll lock that does not shift layout (compensate for the scrollbar), focus moved in and returned on close.

Z-index is a scale, not a number: `--z-sticky: 10; --z-dropdown: 20; --z-dialog: 30; --z-toast: 40`. Anything above 50 is a bug.

## Inputs

- A visible label. Placeholders are hints, not labels.
- Error message below, linked with `aria-describedby`, appears after blur or submit, not on every keystroke.
- Height matches the button height in the same size (32px `sm`, 36px `md`, 40px `lg`).
- 16px font size on touch, or the browser zooms.
- `inputmode` and `autocomplete` set. `enterkeyhint` on phones.
- Icons inside inputs are decorative unless they are buttons; buttons have a 32px hit area even if they draw at 16px.

## Lists and rows

- The whole row is the target (an `::after` pseudo-element covering the row on the link), not just the name.
- One hover highlight that follows the pointer, not per-row backgrounds.
- Keyboard: `j`/`k` or arrows move, `Enter` activates, and the focused row is visibly outlined.
- Truncate the description, never the name.
- Virtualise past ~200 rows.

## Icons

One set. One stroke width. One size in a context (14px in dense UI, 16px in comfortable, 20px in touch). `currentColor` so they follow text. Decorative icons are `aria-hidden`; icon-only buttons have `aria-label`.

## Tables

- Header row sticky, with a bottom border.
- Numbers right-aligned and tabular; text left; never centred.
- Row height 32–40px; zebra striping only in very dense tables, and at the `--hover` level.
- Sort indicator in the header, with the active column visible without hovering.
- Empty state inside the table region, not replacing it.

## Testing a component

Before calling it done, open it and:

1. Tab through it with the mouse unplugged.
2. Press it, hold it, drag off it, release.
3. Resize to 375px.
4. Toggle dark and light.
5. Turn on reduced motion.
6. Fill it with the longest realistic content and the shortest.
7. Screen reader: does it have a name, a role and a state?

## Marks of a generated component

| Sign | Fix |
| --- | --- |
| Custom dropdown with `useState` and an outside-click hook | Menu primitive |
| `variant` with eight values | four |
| `className` not forwarded | forward it, merge it |
| Inline `transition: all 0.3s` | tokens |
| Spinner replaces the label and the button shrinks | spinner overlays, width fixed |
| Icon-only button with no label | `aria-label` |
| `div onClick` | `button` |
| Modal with no focus trap | Dialog primitive |

## From the catalogue

- [Base UI](https://base-ui.com), [Radix Primitives](https://www.radix-ui.com/primitives), [React Aria](https://react-spectrum.adobe.com/react-aria), [Ark UI](https://ark-ui.com) — headless behaviour with the accessibility done.
- [shadcn/ui](https://ui.shadcn.com) — a finished look on top of primitives, copied into your repo.
- [Sonner](https://sonner.emilkowal.ski), [Vaul](https://vaul.emilkowal.ski), [cmdk](https://cmdk.paco.me), [NumberFlow](https://number-flow.barvian.me) — the specific problems solved.
- [ARIA Authoring Practices](https://www.w3.org/WAI/ARIA/apg/patterns) — the spec for each pattern's keyboard and roles.
- More at [stealth.pm/components](https://stealth.pm/components).
