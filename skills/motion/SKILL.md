---
name: motion
description: Build interface motion that would pass a strict review. Decides whether something should animate at all, names its purpose, picks the cheapest tool, the right properties, the curve, the duration or spring, the origin, how it interrupts and how it exits, then writes the implementation with reduced-motion built in. Use when asked to animate, add motion, add a transition, make something feel alive, or when building any component that enters, exits, moves or changes state. For scroll-driven and WebGL work use scroll-and-webgl.
---

# motion

Motion is the most abused tool in interface work because it is the most visible. This skill exists to make you write less of it, and to make what remains correct.

## Before writing a line

Answer these in order. Each gate can end the task with zero code, and that is a good outcome.

### 1. How often will they see it?

| Seen | Rule |
| --- | --- |
| Every few seconds (typing, list navigation, hover on rows) | Nothing, or under 120ms and barely visible |
| Many times a day (menus, palettes, tabs, toggles) | Fast feedback only, 120–200ms, no delight |
| A few times a day (dialogs, drawers, sheets, toasts) | Full entrance and exit, 180–320ms |
| Rarely (onboarding, success, first run) | The only place a longer, expressive animation belongs |

**Keyboard-initiated actions are instant.** ⌘K, Escape, j/k, tab switching by shortcut: render the new state on the same frame. Motion here reads as latency.

### 2. What is it for?

Name one. If you cannot, stop.

- **Acknowledge** — the press, the toggle, the drop. Proof the interface heard them.
- **Locate** — where a thing came from or went. A popover grows from its trigger; a toast leaves the way it came.
- **Continue** — bridge two layouts so content does not teleport. Reordering, expanding, tab indicators.
- **Signal** — a state changed while they were not looking. A saved badge, a counter roll.
- **Explain** — show how something works. Marketing and onboarding only.

"Looks alive" is not a purpose. "Everything else has an animation" is not a purpose.

### 3. Which tool?

Cheapest that does the job, in this order:

| Need | Use |
| --- | --- |
| Hover, focus, press, colour, simple enter | CSS `transition` |
| Looping or multi-step, no interruption needed | CSS `@keyframes` |
| Enter *and* exit of mounted React elements, layout changes, springs, interruption | Motion (`motion/react`) |
| Timelines, scrub, pin, split text, sequenced scenes | GSAP |
| Scroll-linked | CSS scroll-driven animations first, ScrollTrigger when they cannot |

Do not install a library for a fade. Do not hand-roll exit animations with `setTimeout` when Motion's `AnimatePresence` exists in the project.

### 4. Which properties?

`transform` and `opacity`. Both are composited; nothing else is reliably.

| Want | Do | Not |
| --- | --- | --- |
| Appear | `opacity 0→1` + `scale .96→1` or `translateY 6px→0` | `scale(0)`, `display` swap alone |
| Grow to content | Measure and animate `height` with `overflow: hidden`, or `grid-template-rows: 0fr→1fr` | animating `max-height: 9999px` |
| Reveal along an edge | `clip-path: inset(...)` | `width` |
| Blur in | `filter: blur(6px→0)` paired with opacity, short | blur alone, blur on large areas |
| Move between positions | `transform` via layout animation (`layout` in Motion, FLIP in GSAP) | animating `top`/`left` |
| Colour | `background-color`, `color`, `border-color` with `transition` | `filter: hue-rotate` |

Never `transition: all`. List the properties. It is faster and it stops surprises.

### 5. Which curve?

| Situation | Curve |
| --- | --- |
| Entering the screen | strong ease-out: `cubic-bezier(0.16, 1, 0.3, 1)` |
| Leaving the screen | ease-in is acceptable here only, or the same ease-out at 70% duration |
| Moving while on screen (reorder, tab indicator, resize) | ease-in-out: `cubic-bezier(0.76, 0, 0.24, 1)` |
| Hover and colour | `ease` or a soft ease-out, never expo |
| Continuous (marquee, spinner, progress) | `linear` |
| Follows a gesture or a pointer | a spring, not a curve |
| iOS-style sheet or drawer | `cubic-bezier(0.32, 0.72, 0, 1)` |

Built-in `ease-in-out` and `ease-out` are too weak to feel intentional. Use the custom curves above and store them as tokens: `--ease-out-expo`, `--ease-in-out-quart`.

Never ease-in on an entrance. The user watches the first 50ms hardest; ease-in wastes them.

### 6. How long?

| Element | Duration |
| --- | --- |
| Press feedback | 100–140ms |
| Hover state | 120–180ms |
| Tooltip, small popover | 140–200ms |
| Menu, dropdown, select | 160–220ms |
| Tab indicator, toggle thumb | 180–260ms |
| Dialog, sheet, drawer | 220–320ms |
| Toast enter | 240–320ms; exit 180ms |
| Page or view transition | 300–450ms |
| Marketing reveal | 600–1200ms, once |

Exits run at roughly 70% of the entrance. Under 100ms is invisible; over 400ms in product UI is a wait. When in doubt, shorten by 20%.

### 7. Springs

For anything the user drags, throws, or that must feel physical:

| Feel | Motion config |
| --- | --- |
| Snappy UI (indicators, chips, small moves) | `{ type: "spring", stiffness: 520, damping: 38, mass: 0.7 }` |
| Soft (cards, panels, list reorder) | `{ type: "spring", stiffness: 260, damping: 28, mass: 0.9 }` |
| Following the pointer (hover highlight that glides) | `{ type: "spring", stiffness: 650, damping: 45, mass: 0.5 }` |
| Sheet settle after release | `{ type: "spring", stiffness: 300, damping: 30 }` or `bounce: 0` with `duration: 0.4` |

Bounce is almost never right in product UI. A spring should overshoot by a pixel or two at most; if it wobbles, raise damping.

### 8. Where from?

Set `transform-origin` to the trigger. A popover from a button in the top-right grows from its top-right corner. Dialogs are the exception: they are centred and scale from centre. Toasts come from the edge they will leave by.

### 9. Staggers

Stagger only lists that arrive together, once. 12–25ms between items, capped: after the eighth item, no more delay, or the tail arrives after the user has started reading. Never stagger on re-render or filter changes.

### 10. Interruption

A user who closes a menu while it is still opening must see it reverse from where it is, not finish opening then close. CSS transitions do this by default. Keyframes do not. Motion does. If you used keyframes for something interruptible, you used the wrong tool.

### 11. Exit

Exits are deliberate: shorter, simpler, often just opacity. A dialog that fades and scales in can fade only on the way out. Nothing should be visible for even a frame after the user dismissed it and then start disappearing; the first frame after the click is already changed.

### 12. Reduced motion

Ship it in the same change:

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.001ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.001ms !important;
  }
}
```

In Motion, `useReducedMotion()` and swap transforms for opacity. Smooth scrolling libraries are disabled entirely. Everything that would have animated in is simply visible.

## Recipes

### Menu / popover

```css
.menu {
  transform-origin: var(--origin, top left);
  transition: opacity 160ms var(--ease-out-expo), transform 160ms var(--ease-out-expo);
}
.menu[data-state="closed"] { opacity: 0; transform: scale(0.96) translateY(-2px); }
.menu[data-state="open"] { opacity: 1; transform: none; }
```

### Dialog

```tsx
<motion.div
  initial={{ opacity: 0, scale: 0.97, y: 6 }}
  animate={{ opacity: 1, scale: 1, y: 0 }}
  exit={{ opacity: 0, transition: { duration: 0.15 } }}
  transition={{ duration: 0.26, ease: [0.16, 1, 0.3, 1] }}
/>
```

Backdrop fades separately, slightly longer on the way in, shorter on the way out.

### Toast

Enter from the edge it lives on, ease-out, ~280ms. Stack older toasts behind with `scale(0.95)` and `translateY(-8px)`, dimmed. Exit toward the same edge at ~180ms. Pause timers on hover and when the tab is hidden.

### Height to content

```css
.grid { display: grid; grid-template-rows: 0fr; transition: grid-template-rows 240ms var(--ease-in-out-quart); }
.grid[data-open] { grid-template-rows: 1fr; }
.grid > * { overflow: hidden; min-height: 0; }
```

### Tab indicator

One element with `layoutId` (Motion) sprung between tabs; never animate `left`/`width` on each tab.

### Hover highlight that glides between rows

One absolutely positioned pill, position updated on `pointerenter`, animated with the "following the pointer" spring. Remove on `pointerleave` with a 150ms fade.

### Number change

Animate digits rolling, not the whole number fading. Use a library that handles digit columns (NumberFlow) rather than writing it.

### Text reveal (marketing only)

Split into words, `opacity 0→1`, `y 6→0`, `filter blur(6px→0)`, 30ms between words, once, on first view.

## Anti-patterns

| Seen in generated UI | Instead |
| --- | --- |
| `transition: all 0.3s ease` | named properties, 160–220ms, custom ease-out |
| `animate-bounce` on an icon | nothing |
| Every card lifting `-4px` on hover with a shadow | one of: border brightens, background lifts one step |
| Skeleton shimmer at 1.5s | static skeleton, or a slow 2.5s pulse |
| Page-load entrance on every element | at most the title and one region, once |
| Spinner for everything | skeleton for content, spinner only for actions |
| `duration: 0.5` for a dropdown | `0.18` |
| Parallax on product UI | never |

## Performance

`will-change: transform` only on elements about to animate, removed after. Avoid animating anything with `backdrop-filter`. Large blurs are expensive; keep radius under 8px and area small. Test on a throttled CPU: if it drops frames, cut the property, not the duration.

## From the catalogue

- [You Don't Need Animations](https://emilkowal.ski/ui/you-dont-need-animations) — the frequency argument.
- [Motion](https://motion.dev) — the React animation library these recipes assume.
- [GSAP](https://gsap.com) — timelines, scrub, SplitText.
- [Easing Wizard](https://easingwizard.com), [Anime.js Easing Editor](https://animejs.com/easing-editor) — build and compare curves.
- [Transitions.dev](https://transitions.dev) — the handful of transitions web apps need.
- [NumberFlow](https://number-flow.barvian.me) — digit rolling done right.
- [60fps](https://60fps.design), [Design Spells](https://www.designspells.com) — study recordings before inventing.
- More at [stealth.pm/build](https://stealth.pm/build#engines) and [stealth.pm/tools](https://stealth.pm/tools#easing).
