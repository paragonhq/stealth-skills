---
name: states
description: Design every state a screen can be in, not just the happy one. Loading with skeletons that match the layout, spinner timing, empty states that teach one action, partial and stale data, errors that say what to do, optimistic updates and rollback, streaming, offline, unsaved changes, and success that does not interrupt. Use when building any screen that fetches, submits or can be empty, or when a screen "flashes", "jumps", or shows a spinner over everything.
---

# states

The happy path is one state out of six. Interfaces are judged on the other five, because that is where people are stuck, waiting or worried.

## Enumerate before building

For every region that depends on data, write the list:

| State | When | What is shown |
| --- | --- | --- |
| Loading, first time | no data yet | skeleton in the final layout |
| Loading, again | data exists, refreshing | keep the data, subtle indicator |
| Empty | request succeeded, nothing to show | one line, one action |
| Partial | some of the data, or a page of it | what we have, plus a clear edge |
| Error | request failed | what happened, what to do, retry |
| Stale / offline | data may be old | the data, marked |
| Success | an action completed | in place, quietly |

If a region only handles two of these, it is not finished.

## Loading

### Skeletons

- Match the final layout exactly: same heights, same widths within reason, same rhythm. A skeleton that is replaced by content of a different shape is worse than a spinner.
- Reserve the space. Nothing on the page moves when the content arrives.
- Grey blocks at the `--hover` level with the element's radius. No shimmer, or a slow 2–2.5s pulse at low amplitude; a fast shimmer makes waiting feel longer.
- Three to five skeleton rows, not a screen full. The user needs to know the shape, not the count.
- Skeleton for content. Spinner for actions.

### Timing

- Do not show any loading state for the first 150–200ms; most requests finish and the flash is worse than nothing.
- Once shown, keep it for at least 300ms so it does not flicker.
- Beyond 3 seconds, say something: "Still working…" or a progress estimate.
- Beyond 10 seconds, offer a way out: cancel, or "we'll notify you".

### Refetching

When data already exists, never replace it with a skeleton. Keep it, and show refresh with a small indicator near the region (a faint spinner in the header, a subtle bar) or nothing at all if the refresh is fast. Data disappearing and reappearing is the most common cause of a screen feeling broken.

### Images

Declare `width` and `height` (or `aspect-ratio`) so the box exists before the bytes. Fade in over 200ms on load. Below the fold: `loading="lazy"`. Failed: a neutral placeholder at the same size, never a broken-image icon.

## Empty

An empty state is an onboarding moment. It has:

1. One line saying what would be here ("No projects yet").
2. One action that fills it ("Create a project"), styled as the primary button.
3. Optionally, one line on why it matters or how it gets filled.

It lives inside the region it describes, at the region's size, not as a page takeover. No illustration unless the product has an illustration system; a lone clipart astronaut reads as a template.

Distinguish empty from filtered-to-empty: "No results for *foo*" with a clear-filters action is a different state from "No projects yet".

## Partial

- Paginated: show the edge. "Showing 50 of 1,240" with a way to get more. Infinite scroll needs a visible end and a way to reach the footer.
- Progressive: render what you have as it arrives; do not wait for the slowest request.
- Streaming lists: append at the end without moving what the user is reading; new items above the viewport get a "3 new" pill instead of shifting the page.

## Error

Errors say three things, in this order:

1. What happened, in plain words ("Couldn't save your changes").
2. Why, if it helps the user act ("You're offline", "That name is already taken").
3. What to do ("Try again", "Choose another name"), as an action.

Placement: as close to the cause as possible. A field error is under the field. A save error is next to the save button. A region error is inside the region. A page error is a page. Never a toast for something the user needs to fix.

Never show a stack trace, a status code alone, or "Something went wrong" with no action. Keep the data on screen when the refresh fails; mark it stale instead of blanking it.

Retry is automatic once, quietly, for network blips. After that it is the user's choice.

## Optimistic updates

For toggles, reorders, renames, likes, and anything with a low failure rate:

1. Change the interface on the same frame.
2. Send the request.
3. On failure: revert, and explain at the point of failure.

Keep a stable id for the optimistic item so a later refetch merges rather than duplicates. Do not do this for money, deletes without undo, or anything the user cannot easily see reverted.

## Stale and offline

- Data that may be old is marked ("Updated 4m ago"), not hidden.
- Offline: a persistent, quiet bar, not a modal. Reads keep working from cache; writes queue and say so.
- When the connection returns, sync and clear the bar. Do not reload the page.

## Unsaved changes

- Show the state: a dot on the tab title, a "Saved" / "Saving…" / "Unsaved" label near the action.
- Autosave when the cost of a wrong save is low; explicit save when it is not.
- Warn on navigation away only when there is something to lose, and say what.
- Never lose the input. Recover drafts from local storage.

## Success

- In place. The button says "Saved" for 1.5s, the row updates, the count increments.
- A toast only when the effect is somewhere else ("Added to *Reading list*").
- A full-screen success is for one-time events: signup, payment, onboarding complete.
- Never "Successfully saved successfully". Say what changed.

## Transitions between states

- Skeleton to content: cross-fade 200ms, no movement.
- Content to error: replace in place, no shake.
- Empty to first item: the item enters; the empty message leaves. Not both animating at once.
- State changes triggered by the user's own action are faster than ones from the server.

## Checklist

- [ ] Every fetching region has skeleton, empty, error, and refetch states
- [ ] Nothing moves when data arrives
- [ ] No loading indicator before 150ms; none shorter than 300ms once shown
- [ ] Refetch keeps existing data on screen
- [ ] Empty states have one action
- [ ] Errors say what to do and sit next to the cause
- [ ] Optimistic updates revert with an explanation
- [ ] Stale data is marked, not hidden
- [ ] Success is shown in place

## From the catalogue

- [Page Flows](https://pageflows.com) — recorded onboarding, empty and error journeys from shipping products.
- [Nielsen Norman Group](https://www.nngroup.com/articles) — response-time limits and the research behind them.
- [Mobbin](https://mobbin.com) — search "empty state" and "error" to see how real apps handle them.
- [Smashing Magazine](https://www.smashingmagazine.com) — long-form on skeleton screens and perceived performance.
- More at [stealth.pm/look](https://stealth.pm/look#app-flows).
