---
name: review
description: A strict interface review with evidence. Resolves the scope, recons the stack and its conventions, inspects in a fixed order from function to polish, reports findings only with a file and line and a concrete change, ranks by user impact, caps at fifteen, and returns a single table plus a verdict. Use when asked to review, critique, audit, or QA a screen, component, branch or pull request for interface quality.
---

# review

A review is a list of things to change, each with a place, a fix and a reason. Anything else is an opinion. This skill produces the list.

## Posture

- Evidence, not taste. A finding cites `path/file.tsx:42` and shows what is there now. If you cannot point at it, you have not found it.
- Fix in the project's idiom. If they use Tailwind, the fix is a class. If they have tokens, the fix names the token. A finding that asks them to adopt a different stack is not a finding.
- A short list from a real inspection beats a long list padded to look thorough. Fifteen findings maximum. If there are more, the top fifteen by impact, and a line saying the rest exist.
- Deliberate choices are not findings. Density, radius, voice and palette are theirs unless they fail a hard rule (contrast, focus, hit size, missing state).
- Never say "consider". Say what to change.

## 1. Scope

State what is being reviewed before reviewing it: a screen, a flow, a component, a diff. If the request names a branch or PR, the scope is the changed files plus the surfaces they affect. If the request is a screen, the scope is that screen in all its states and at 375px and 1440px.

If the scope is too large to inspect credibly, narrow it to one complete flow, say which, and say what was excluded. Never imply that uninspected surfaces were reviewed.

## 2. Recon

Two minutes, before any judgment:

- Framework, styling system, component library, motion library, icon set.
- Tokens: where colours, spacing, easing, radius and z-index live. Findings will name these.
- Written conventions: `CLAUDE.md`, `AGENTS.md`, `CONTRIBUTING.md`, a design doc. Findings that contradict a documented convention are reported against the convention, once, not against each instance.
- How to run it. Reviews of interactive behaviour need the interface running; source-only reviews say so.

## 3. Inspect, in this order

Foundational failures hide behind polish. Look bottom-up so the important findings are not buried under cosmetic ones.

| Order | Domain | Look for |
| --- | --- | --- |
| 1 | Function | Is the one action of the screen obvious? Can it be completed? Does anything block it? |
| 2 | Accessibility | Keyboard reachability, focus visibility and management, names on controls, contrast, `div onClick`, colour-only signals, motion under reduced motion |
| 3 | States | Loading, empty, error, partial, busy, disabled, success. Layout shift on data arrival. Spinner timing |
| 4 | Copy | Verbs on buttons, sentence case, errors with next steps, empty states with an action, placeholder text left in |
| 5 | Space | Off-scale values, uneven rhythm, margins on components, alignment, gutters at 375px, horizontal scroll |
| 6 | Type | Too many sizes, hierarchy by size alone, leading and tracking by size, tabular numbers, truncation of important text, orphans |
| 7 | Surface | Hex values outside tokens, borders and shadows by theme, accent overuse, both themes checked, selection and scrollbar |
| 8 | Interaction | Hover ungated, no active state, disabled vs busy, width change on busy, copy feedback, Escape and focus return |
| 9 | Motion | `transition: all`, ease-in on entrance, durations over 300ms in product UI, animation on keyboard actions, `scale(0)`, missing exits, reduced motion |
| 10 | Polish | Title, favicon, OG, external link markers, image dimensions, anchors under sticky headers, 404 |

Each domain has a stealth skill with the full rules (`a11y`, `states`, `ux-copy`, `space`, `type`, `surface`, `interaction`, `motion`, `polish`). Load the ones the scope needs; do not recreate their rules from memory.

## 4. Severity

| Level | Meaning | Examples |
| --- | --- | --- |
| Blocking | Someone cannot complete the task | keyboard trap, unreachable control, contrast below 3:1 on the primary action, submit does nothing on error |
| High | Someone will fail or be confused often | missing error state, layout shift on load, hover-only affordance, ambiguous button label |
| Medium | Feels broken or cheap; most will notice | no press state, `transition: all`, spinner flash, orphan title, off-scale spacing everywhere |
| Low | A trained eye notices | icon 1px low, tracking on a label, a shadow in dark mode |

Rank by level, then by how many people meet it how often. A low on the primary button outranks a medium on the settings page nobody visits.

## 5. Output

One table. Nothing before it except the scope line and the recon line. Nothing after it except the verdict and, if any, the excluded list.

```
Scope: /settings/profile at 375px and 1440px, both themes, running locally.
Stack: Next.js, Tailwind v4 with tokens in globals.css, Base UI, Motion. Conventions: CLAUDE.md.

| # | Level | Where | Now | Change to | Why |
| --- | --- | --- | --- | --- | --- |
| 1 | Blocking | `ProfileForm.tsx:88` | `<div onClick={save}>` | `<button type="submit">` | not keyboard reachable; Enter does not submit |
| 2 | High | `ProfileForm.tsx:41` | error shown only as red border | message under field, `aria-describedby`, focus first invalid | user cannot tell what is wrong |
| 3 | Medium | `Avatar.tsx:12` | `transition: all 0.3s` | `transition: transform 120ms var(--ease-out-expo)` | `all` transitions layout properties; 300ms is slow for hover |
| … |

Verdict: Not ready. Fix 1–2, then 3–7 in this change; 8–12 can follow.
Excluded: the billing tab (separate flow), the mobile nav (not in scope).
```

Rules for the table:

- `Where` is a file and line, or a selector when reviewing a running page without source.
- `Now` is what is literally there: the value, the markup, the behaviour observed.
- `Change to` is a concrete replacement in the project's idiom. Not "improve", not "consider".
- `Why` is one clause. The user effect, not the rule's name.
- No row without all four. No row that is a preference.

Verdict is one of: **Ready**, **Ready after blocking fixes**, **Not ready**. Say which findings gate it.

## 6. What not to do

- Do not rewrite the component in the review. The review is the list; the fix is a separate step, if asked.
- Do not list the same issue per instance. "`transition: all` in 14 files" is one row with the count and the token to use.
- Do not report absence of things the scope never needed ("no dark mode" on a print stylesheet).
- Do not grade. There is no score. There is a list and a verdict.
- Do not soften a blocking finding because the rest is good.

## Self-check before returning

- [ ] Scope and stack stated
- [ ] Every row has a location I actually looked at
- [ ] Ordered by impact, not by the order I found them
- [ ] Fifteen or fewer
- [ ] Every fix is in the project's idiom
- [ ] Verdict names the gating rows
- [ ] Nothing in the table is a preference

## From the catalogue

- [Nielsen Norman Group](https://www.nngroup.com/articles) — heuristics that make good review criteria.
- [Details That Make Interfaces Feel Better](https://jakub.kr/writing/details-that-make-interfaces-feel-better) — the small findings.
- [axe](https://www.deque.com/axe), [Lighthouse](https://developer.chrome.com/docs/lighthouse) — automated first pass; the rest is eyes.
- [Polypane](https://polypane.app) — every breakpoint and theme in one window.
- More at [stealth.pm/tools](https://stealth.pm/tools#quality).
