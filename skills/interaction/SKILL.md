---
name: interaction
description: The moment after the click. Press, hover, focus, drag, keyboard, latency and feedback: how a control acknowledges the person using it. Use when building or fixing buttons, rows, lists, toggles, drag and drop, gestures, keyboard shortcuts, copy actions, optimistic updates, or when an interface "feels dead", "feels slow" or "doesn't respond". Motion values live in the motion skill; this skill decides what responds and when.
---

# interaction

An interface feels good or bad in the 100 milliseconds after the user does something. Everything in this skill happens in that window.

## The feedback budget

| Delay until something visibly changes | Perception |
| --- | --- |
| 0–50ms | Instant. The interface is an extension of the hand. |
| 50–100ms | Responsive. Still feels direct. |
| 100–300ms | Noticeable. Needs a press state to cover it. |
| 300ms–1s | Waiting. Needs a busy indicator on the control itself. |
| 1s+ | Needs progress, a cancel, or a way to leave and come back. |

The job is to put *something* in the first 50ms, always. Not the result: the acknowledgement.

## Pointer states

Every interactive element has all of these, styled deliberately:

### Hover

Only where hover exists. Gate it:

```css
@media (hover: hover) {
  .row:hover { background: var(--hover); }
}
```

Hover is a hint, not an event. Colour shifts one step; text brightens; an arrow nudges 1px. It does not lift the element 4px and add a shadow.

For rows and lists: one highlight element that glides between rows on a spring reads better than each row lighting independently, and it tells the user the list is one thing.

### Active (press)

```css
.button:active { transform: scale(0.97); }
.button { transition: transform 120ms var(--ease-out-expo); }
```

Small controls scale less (0.97); large cards scale even less (0.99) or just darken. Icon buttons can scale 0.9. Press should feel like resistance, not a bounce.

Use `:active` on pointer devices and mirror it with a data attribute for touch, where `:active` is unreliable.

### Focus

`:focus-visible`, never `:focus`. Outline 1–2px in a foreground colour, `outline-offset: 2px`, matching the element's radius. Remove the default blue only if you replace it. Focus must be visible on every colour of surface you have.

### Disabled vs busy

Disabled means "not available"; busy means "working on it". They are not the same style. Disabled: reduced opacity, `cursor: not-allowed`, no hover. Busy: the label swaps for a spinner *inside the button at the same width*, the button stays pressed-looking, and it ignores further clicks without looking disabled.

Never let a button change width when it becomes busy. Reserve the width or overlay the spinner.

## Keyboard as a first-class input

- Everything reachable by tab in a sensible order. Test by hiding the mouse.
- Lists support `j`/`k` or arrows, `Enter` to open, and a key to copy or act.
- One global shortcut for search (`/` or ⌘K). One for the most common action.
- Shortcuts are shown in the interface: on hover tooltips, in menus, in a footer hint. Discoverable or they do not exist.
- Modifier shortcuts never fire while typing in an input. Single-key shortcuts never fire when focus is in a field.
- Escape closes the top-most layer only.
- Focus returns to the trigger when a layer closes.

## Latency and optimism

Update the interface as if the request succeeded, then reconcile:

1. Apply the change locally on the same frame.
2. Send the request.
3. On failure, revert and say what happened, near the thing that failed.

Use this for toggles, likes, reorders, renames, and any action with a low failure rate and an easy revert. Do not use it for payments, deletes without undo, or anything the user cannot easily see reverted.

For anything slower than 300ms that cannot be optimistic:

- Show the busy state on the control, not a page-level spinner.
- Do not show a spinner before 150ms; a spinner that flashes for 80ms reads as a glitch.
- Once shown, keep it visible for at least 300ms even if the response arrives sooner.

## Copy to clipboard

The most common small action and usually done badly:

1. Copy on click. Confirm inline: the icon swaps to a check for ~1.3s, then back.
2. Also toast, with what was copied ("Link copied", then the URL's host as a sub-line).
3. Handle the failure: `navigator.clipboard` can reject. Say so, and say what to do instead.
4. Keyboard: a `c` key on the focused row does the same thing.

## Undo over confirm

A confirmation dialog interrupts every user to protect the rare one. Prefer: do it immediately, show a toast with **Undo** for 5–8 seconds. Keep confirms for the irreversible: deleting an account, sending money, publishing publicly.

When you must confirm, the dialog names the object ("Delete *Q3 report*?"), the destructive button says the verb ("Delete"), and it is not the default focused control.

## Drag

- **Threshold**: nothing happens until the pointer has moved 4–6px, so clicks stay clicks.
- **Lift**: the dragged item scales to 1.02, gets a shadow, and moves with the pointer under a fast spring or no easing at all. Lag here reads as broken.
- **Placeholder**: the gap it leaves stays open and animates as other items move around it.
- **Constraints**: rubber-band past the edges (movement × 0.3) rather than hard stop.
- **Drop**: settle with a soft spring. Cancel with Escape returns the item the way it came.
- Use `touch-action: none` on the handle, and only the handle, or the page cannot scroll.
- Keyboard alternative: space to pick up, arrows to move, space to drop. It is required, not extra.

## Sheets and drawers

Drag to dismiss follows the finger 1:1. Release past 35% of the height, or with velocity, dismisses; otherwise snap back. The backdrop's opacity tracks the drag. Use a library that has solved this (Vaul) rather than writing it.

## Tooltips

- Delay 400–600ms on first hover, then 0ms for siblings in the same group while the pointer stays within it ("warm" state). Cool down after ~300ms away.
- Position from the trigger; never cover it.
- Show the shortcut in the tooltip if there is one.
- Do not put anything essential in a tooltip; touch devices never see it.

## Toasts

- Bottom-right on desktop, bottom-centre on phones, above any bottom bar.
- Newest in front; older ones scale back. Cap at three.
- Timer pauses on hover and while the tab is hidden.
- One line of what happened, one optional line of detail, one optional action.
- Never toast something the user can already see happened in place.

## Scroll

- Anchors and focus targets use `scroll-margin-top` equal to the sticky header.
- Scroll containers get `overscroll-behavior: contain` so the page does not scroll when they end.
- Preserve scroll position across navigations that are "back", not across new pages.
- Do not hijack the wheel on product UI. Smooth-scroll libraries belong on marketing pages, and are off under reduced motion.

## Cursor

`cursor: pointer` on links and anything that navigates. Buttons use the default cursor on the web (it is what the platform does), unless the project convention is pointer everywhere; then be consistent. `cursor: grab` / `grabbing` on drag handles. Never a custom cursor in product UI.

## Selection

Set `::selection` to the foreground colour with the background colour as text, or to a tint of the accent. The default blue on a tuned neutral palette looks like a bug.

## Double actions

- Submit buttons disable after the first click until the response arrives.
- Toggles debounce: rapid clicks resolve to the last state, sending one request.
- Links that open in a new tab say so with an arrow icon, and use `rel="noopener"`.

## Checklist before shipping a control

- [ ] Hover gated to hover devices, one-step change
- [ ] `:active` scale or darken, ~120ms
- [ ] `:focus-visible` ring on every surface it appears on
- [ ] Disabled and busy are distinct and the width never changes
- [ ] Reachable and operable by keyboard; shortcut shown if it has one
- [ ] First visible change within 50ms of the click
- [ ] Failure state exists and says what to do
- [ ] Works with a finger: 44px target, no hover-only affordance

## From the catalogue

- [Invisible Details of Interaction Design](https://rauno.me/craft/interaction-design) — the canonical essay, with live demos.
- [Family Values](https://benji.org/family-values) — deciding what an app should feel like.
- [Vaul](https://vaul.emilkowal.ski), [Sonner](https://sonner.emilkowal.ski), [cmdk](https://cmdk.paco.me) — drawers, toasts, palettes with the interaction solved.
- [interior.dev](https://www.interior.dev) — micro-interactions tuned frame by frame.
- [Design Spells](https://www.designspells.com) — small details worth stealing.
- More at [stealth.pm/components](https://stealth.pm/components#micro).
