---
name: scroll-and-webgl
description: Spectacle, budgeted. Scroll-driven animation with native CSS first and GSAP ScrollTrigger when needed, smooth scrolling with Lenis and when to turn it off, split-text reveals, pinned scenes, canvas and WebGL on a performance budget, shaders as material, pausing off-screen, loading strategy, and the reduced-motion and no-JS fallbacks that keep the page readable. Use for marketing pages, portfolios, launches and hero sections. Never for product UI.
---

# scroll-and-webgl

This is the one tier where motion is allowed to be the point. The rules are stricter, not looser, because the failure mode is a page that is beautiful on the author's laptop and unusable everywhere else.

## Where it belongs

| Surface | Allowed |
| --- | --- |
| Marketing page, launch, portfolio, case study | Yes, with the budget below |
| Docs, blog, reading | A title reveal at most |
| Product UI, dashboards, tools, settings | No. Load the `motion` skill instead |

If a scroll effect is proposed for a product screen, the answer is no, and the reason is that people use product screens hundreds of times.

## The budget

Set before starting, checked at the end:

- **60fps on a 4× throttled CPU** for the whole scroll. One dropped frame per second is a fail.
- **LCP under 2.5s** with the effect present. The hero's text is in the HTML and visible before any script runs.
- **INP under 200ms**: scroll handlers never do layout reads; everything is on the compositor or in a `requestAnimationFrame` batch.
- **Memory**: one WebGL context per page. Textures under 2048px. Dispose on unmount.
- **One spectacle per viewport.** A hero shader *or* a pinned scroll scene *or* a split-text reveal at a time. Two at once is noise.

## Scroll-driven, in order of cost

### 1. CSS scroll-driven animations

Zero JavaScript, runs on the compositor, respects reduced motion with one media query:

```css
.reveal {
  animation: fade-up linear both;
  animation-timeline: view();
  animation-range: entry 0% entry 40%;
}
@keyframes fade-up { from { opacity: 0; transform: translateY(24px); } }
```

Use for: reveal on enter, progress bars, parallax under 10%, sticky headers that shrink. Falls back to "just visible" in browsers without support, which is the correct fallback.

### 2. IntersectionObserver + a class

For reveal-once effects where the CSS timeline is not supported or a library's entrance is wanted. Observe, add the class, unobserve. Threshold 0.15–0.3; `rootMargin: "0px 0px -10% 0px"` so things reveal slightly before the bottom edge.

### 3. GSAP ScrollTrigger

For anything scrubbed, pinned, or sequenced:

- `scrub: true` (or `0.5`–`1` for a little lag) ties the timeline to the scrollbar. Nothing else is smooth enough.
- `pin: true` holds a section while a scene plays; keep pinned sections under 300vh of scroll or people get lost.
- `anticipatePin: 1` on pins to avoid a one-frame jump.
- One `ScrollTrigger` per scene, not per element; sequence elements inside a timeline.
- `matchMedia` to remove pins and scrubs below 768px and under reduced motion.
- `ScrollTrigger.refresh()` after fonts load and after any layout change.

### 4. Lenis (smooth scroll)

Lenis makes scroll-linked animation feel continuous by interpolating wheel input. It is right for marketing pages and wrong for everything else:

- Off under reduced motion. Off on touch (native momentum is better). Off in any region with its own scrollbar (`data-lenis-prevent`).
- Duration 1.1–1.3, `easing: (t) => Math.min(1, 1.001 - Math.pow(2, -10 * t))`.
- Drive GSAP's ticker from Lenis (`lenis.on("scroll", ScrollTrigger.update)`) so the two agree.
- Anchors go through `lenis.scrollTo(el, { offset: -header })`, not native `scrollIntoView`.
- Modals and menus call `lenis.stop()` while open.

## Text reveals

Split into lines or words, never characters, unless the text is short (a single word logo). Characters give a typewriter feel that dates quickly.

- Lines: `translateY(100%)` inside an `overflow: hidden` wrapper, 80ms stagger, 700ms ease-out-expo. The classic, still the best.
- Words: opacity + `translateY(6px)` + `blur(6px→0)`, 30ms stagger.
- Once. On first view. Then the text is text.
- The `aria-label` holds the full string on the parent; the split spans are `aria-hidden`. Without JS the text is plain and visible.
- Re-split on resize; a split made at one width breaks lines wrong at another. GSAP SplitText handles this; a hand-rolled split must listen for it.

## Canvas and WebGL

### When

A shader background, a 3D object that responds to the pointer, a particle field, a product model. If it can be done with CSS gradients and a noise SVG, do that.

### How

- **Device pixel ratio capped at 2**, 1.5 on large canvases. Retina at 3× is four times the pixels for no visible gain.
- **Pause off-screen** with IntersectionObserver and when `document.hidden`. A hidden canvas still burning the GPU is the most common cause of a hot laptop.
- **Resize with `ResizeObserver`**, debounced, resetting the drawing buffer once, not per frame.
- **One animation loop** for the page. Multiple `requestAnimationFrame` loops fight.
- **Lazy import** the library and the scene. The hero's text renders first; the canvas fades in at 400ms when ready. A blank hero waiting for three.js is a fail.
- **Dispose** geometries, materials, textures and the renderer on unmount. Memory leaks across route changes are routine in SPAs.
- **Pointer response** is eased: lerp the target by 0.08–0.12 per frame. Direct 1:1 tracking feels mechanical.
- **Fallback**: a static image or a CSS gradient of the same colour and weight, used when WebGL is unavailable, under reduced motion, and on low-end devices (check `navigator.hardwareConcurrency < 4` and `deviceMemory < 4` as a rough gate).

### Shaders as material

A fragment shader for a background is cheaper than a 3D scene and often better: a slow gradient noise, a dithered glow, a grain. Keep it under 30 lines, animate with time and a pointer uniform, and keep contrast low so text on top passes. Dither (ordered or blue-noise) hides banding in dark gradients and adds material.

### 3D

Three.js or React Three Fiber. Models as compressed glTF (Draco or Meshopt), under 2MB, with a `<Suspense>` fallback that matches the final silhouette. Environment lighting from a small HDRI. Shadows off unless they are the point. Post-processing is one pass, or none.

## Loading strategy

1. HTML with all text, above-the-fold image with dimensions. Visible at first paint.
2. Fonts (two weights, preloaded).
3. Layout stabilised. LCP recorded here.
4. Scroll library and observers.
5. Canvas, WebGL, models: lazy, after idle, fading in.

If step 5 fails or is skipped, the page is complete anyway. That is the test.

## Reduced motion and no JS

Under `prefers-reduced-motion: reduce`:

- No Lenis. No pins. No scrubs. No canvas motion (a static frame is fine).
- Reveals are not run; content is visible.
- Video does not autoplay.

Without JavaScript:

- Everything hidden for a reveal is visible (`html:not(.js) [data-reveal] { opacity: 1 }`).
- The page reads top to bottom with no gaps where scenes would be.

## Marks of a page that will be turned off

| Sign | Fix |
| --- | --- |
| Scroll hijacked; the wheel feels wrong | Lenis only on marketing, off on touch, or none |
| Pinned section longer than 300vh | Cut the scene; pin less |
| Text that only exists after a script splits it | Text in HTML; split enhances |
| Canvas running while off-screen | IntersectionObserver pause |
| DPR 3 on a full-screen canvas | Cap at 2 |
| Hero blank until three.js loads | Text first; canvas fades in |
| Parallax at 30% | Under 10%, or none |
| Character-by-character reveal on a paragraph | Lines |
| Effects on a dashboard | Delete them |

## From the catalogue

- [GSAP](https://gsap.com) and ScrollTrigger, SplitText — the timeline tools these recipes assume.
- [Lenis](https://lenis.darkroom.engineering) — smooth scroll, with the caveats above.
- [Three.js](https://threejs.org), [React Three Fiber](https://r3f.docs.pmnd.rs) — 3D on the web.
- [Codrops](https://tympanus.net/codrops) — experimental tutorials for scroll, WebGL and transitions.
- [Maxime Heckel](https://blog.maximeheckel.com) — shaders and WebGL explained by someone who ships them.
- [Godly](https://godly.website), [Awwwards](https://www.awwwards.com) — study what the effect is for before copying it.
- More at [stealth.pm/build](https://stealth.pm/build#webgl).
