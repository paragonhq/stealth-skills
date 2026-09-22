---
name: mobile-feel
description: Make a web interface feel native on a phone. Touch targets, dynamic viewport height, safe areas, tap highlight and sticky hover, input zoom, touch-action and overscroll, sheets and bottom bars in the thumb zone, the on-screen keyboard, momentum and pull-to-refresh conflicts, and performance on a mid-range device. Use when a site "feels like a website" on mobile, when building bottom sheets, bottom navigation or mobile forms, or before shipping anything people will use on a phone.
---

# mobile-feel

A phone is not a small desktop. The pointer is a thumb, the viewport moves, the keyboard eats half the screen, and every mistake is felt in the hand.

## Targets

- 44×44px minimum for anything tappable, even if it draws at 20px. Extend the hit area with padding or a pseudo-element, not by making the icon bigger.
- 8px minimum between adjacent targets.
- Primary actions in the bottom third of the screen, where a thumb rests. Destructive actions away from it.
- Rows are the target, not the text inside them.

## Viewport

- `100dvh`, never `100vh`. `vh` includes the browser chrome; `dvh` is what is actually visible. Use `svh` for things that must not resize when the chrome hides.
- `<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">`. `viewport-fit=cover` unlocks safe-area insets. Never `user-scalable=no` or `maximum-scale=1`.
- Safe areas on every fixed or sticky edge element:

```css
.bottom-bar { padding-bottom: max(12px, env(safe-area-inset-bottom)); }
.top-bar { padding-top: env(safe-area-inset-top); }
```

## Touch behaviour

```css
* { -webkit-tap-highlight-color: transparent; }
button, a, [role="button"] { touch-action: manipulation; } /* no 300ms delay, no double-tap zoom */
.drag-handle { touch-action: none; }   /* only on the handle */
.scroll-region { overscroll-behavior: contain; -webkit-overflow-scrolling: touch; }
```

- Replace the removed tap highlight with your own `:active` state, or taps feel dead.
- `:hover` styles stick after a tap on touch devices. Gate every hover with `@media (hover: hover)`.
- `:active` is unreliable on iOS for elements without a `touchstart` listener; a data attribute set on `pointerdown` and cleared on `pointerup`/`pointercancel` is more dependable.
- Long-press selects text and shows a callout on links; `-webkit-touch-callout: none` and `user-select: none` on controls only, never on content.

## Inputs

- Font size 16px or larger on inputs, or iOS zooms the page on focus and does not zoom back.
- `inputmode="numeric"`, `"email"`, `"tel"`, `"decimal"`, `"search"` so the right keyboard appears.
- `autocomplete` values set (`email`, `one-time-code`, `cc-number`…). `autocapitalize="none"` on usernames and codes.
- `enterkeyhint="send"` / `"search"` / `"done"` on the last field.
- Focused inputs must be visible above the keyboard. Listen to `visualViewport` `resize` and scroll the field into view, or anchor the composer to `visualViewport.height`.
- Fixed elements move when the keyboard opens on iOS. Anything that must stay put uses `position: absolute` inside a container sized to the visual viewport.

## Sheets

Bottom sheets are the phone's dialog:

- Drag to dismiss, following the finger 1:1. Release past ~35% or with downward velocity dismisses; otherwise snap back.
- Snap points where the content has natural stops (peek, half, full).
- The backdrop's opacity tracks the drag.
- Scroll inside the sheet only when it is at its full snap; before that, a drag scrolls the sheet, not the content.
- Body scroll locked without layout shift.
- A grabber at the top, 36×5px, at `--fg-4`.
- Use a library that has solved the physics (Vaul) rather than writing it.

Full-screen dialogs on a phone slide up from the bottom; on desktop they fade in centred. It is the same component with two presentations.

## Bottom navigation

- 3–5 items, icon and label, 49–56px tall plus safe area.
- Active item in `--fg`, others in `--fg-3`. No accent fill.
- Tapping the active item scrolls its view to top.
- It hides on scroll-down and reappears on scroll-up only if the content needs the space; otherwise it stays.

## Scroll

- One scrolling region per screen where possible. Nested scroll on a phone is a trap.
- Pull-to-refresh: either implement it properly or set `overscroll-behavior-y: contain` on the scroller so it does not fire the browser's.
- Horizontal carousels: `scroll-snap-type: x mandatory`, `scroll-padding` for the gutters, and a visible cue that there is more.
- Sticky headers under 56px. Anything taller steals the screen.
- No smooth-scroll libraries on touch. Native momentum is the reference; do not compete with it.

## Feedback

- Press states are larger on touch than on desktop: scale 0.96 instead of 0.98, or a visible fill, because there is no hover to prepare the eye.
- Haptics are unavailable on the web (`navigator.vibrate` is Android-only and coarse). Compensate with visual feedback on the same frame.
- Toasts at the bottom-centre, above the bottom bar and the keyboard.
- Loading indicators near the thumb, not at the top of a screen the thumb is covering.

## Layout at 375px

- Side gutters 16px. Never 0, never 24+.
- One column. Cards full width with 12px radius, or edge-to-edge rows with dividers; not both.
- Text at 15–16px body, 13px secondary. 13px body is too small for a phone held at arm's length.
- Tables become cards or a horizontal scroll with a pinned first column. Never a squashed table.
- Truncate less; wrap more. Names wrap to two lines on a phone.
- Test with the largest dynamic type setting and with 200% zoom.

## Performance on a real phone

- Test on a mid-range Android, not the newest iPhone. Throttle 4× CPU in DevTools as a proxy.
- `backdrop-filter` and large box-shadows are the first things to cut when scroll stutters.
- Animate only `transform` and `opacity`. Never `height`, `width` or `top` on a phone.
- Images sized for the device (`srcset`), lazy below the fold, decoded async.
- First interaction ready under 2s on 4G: defer everything that is not the first screen.

## PWA touches

- `manifest.webmanifest` with name, icons (192, 512, maskable), `display: standalone`, `theme_color`, `background_color`.
- `apple-mobile-web-app-capable` and the status bar style.
- A splash that matches the app's frame colour, not white.

## Checklist

- [ ] 44px targets, 8px apart, primary actions low on the screen
- [ ] `dvh`, `viewport-fit=cover`, safe areas on fixed edges
- [ ] Tap highlight removed and replaced with an `:active` state
- [ ] Hover gated; no sticky hover after tap
- [ ] Inputs at 16px with `inputmode`, `autocomplete`, `enterkeyhint`
- [ ] Focused field visible above the keyboard
- [ ] Sheets drag, snap, and lock body scroll
- [ ] One scroll region; overscroll contained
- [ ] Toasts above the bottom bar
- [ ] Tested on a mid-range Android with throttling

## From the catalogue

- [loadmo.re](https://loadmo.re) — mobile-first sites and experiments worth studying.
- [Vaul](https://vaul.emilkowal.ski) — the drawer with the gesture solved.
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines) — targets, sheets, safe areas, as Apple specifies them.
- [Mobbin](https://mobbin.com) — real mobile flows, searchable by pattern.
- [web.dev](https://web.dev) — INP, viewport units and touch guidance.
- More at [stealth.pm/look](https://stealth.pm/look).
