---
name: agent-ui
description: Interfaces with a model in the loop. Streaming text without layout thrash, thinking and reasoning states, tool calls and their status, approvals, citations, the composer, stop and regenerate, scroll pinning, markdown rendering, latency masking, and honesty about what the system is doing. Use when building chat, copilots, agent dashboards, AI features inside a product, or anything that shows model output as it is produced.
---

# agent-ui

An interface with a model behind it has one extra state everything else lacks: *in progress, with partial output, of unknown length*. Every pattern here follows from handling that state honestly.

## Principles

1. **Show the work, collapsed.** Reasoning, tool calls and sources are visible on demand, never hidden and never forced open.
2. **The user can always stop.** Every generation has a stop control in the same place, reachable by Escape.
3. **Never fake progress.** No spinners that mean nothing, no "thinking" that is a timer, no typewriter effect slower than the real stream.
4. **The input is always available.** Composing the next message while the current one streams is normal.
5. **Output is a document, not a log.** Rendered markdown, real code blocks, stable layout.

## The composer

- Multiline by default, grows to 6–8 lines then scrolls. Never a single-line input for a model.
- `Enter` sends, `Shift+Enter` newlines on desktop; on phones, a send button and `Enter` newlines.
- Attachments, model or mode selection and slash commands live in a row *below* the text, in the same box, not scattered around it.
- The send button becomes a stop button while streaming. Same position, same size.
- Draft persists across reloads and route changes.
- Focus returns to the composer after sending, after stopping, and after a dialog closes.
- Character or token limits are shown only when within 10% of them.

## Streaming text

- Append tokens to the DOM as they arrive; do not re-render the whole message per token. Batch to animation frames (~16ms) if the stream is faster than that.
- Do not animate individual characters. A soft cursor block at the end of the current line is enough to signal "still going".
- Markdown renders incrementally with a parser that tolerates unclosed blocks. An unclosed code fence renders as a code block, not as a paragraph of backticks.
- Reserve nothing: the message grows. But the *container* must not scroll-jump (see Scroll).
- Long outputs: after ~40 lines of code, collapse with "Show more" only after the stream finishes, never during.

## Thinking and reasoning

- A single collapsed line while it runs: "Thinking…" with an elapsed timer after 2 seconds. Not a shimmering paragraph.
- Expandable to the reasoning text, monospaced or muted, at a smaller size than the answer.
- When the answer starts, the thinking block collapses to one line with its duration ("Thought for 8s") and stays there.
- Never animate a fake "thinking" if the model returns nothing to show; use the plain elapsed indicator.

## Tool calls

Each call is a compact row inside the message flow:

```
▸ Read  src/components/Button.tsx                         120ms ✓
▸ Search  "focus-visible" in src/                          3 hits ✓
▸ Edit  src/components/Button.tsx                         running…
```

- Icon or verb, the target, a status: running, done (with a result summary), failed (with the error, expandable).
- Chips or rows, not cards. Group consecutive calls; ten reads collapse into "Read 10 files ▸".
- Expanding shows the raw input and output, monospaced, scrollable, with copy.
- A running call shows a small determinate or indeterminate indicator on its own row; the whole message does not spin.
- Failures do not stop the layout; they stay in place, marked, and the model's next message explains.

## Approvals

When the agent wants to do something with consequences (write a file, send an email, run a command, spend money):

- A block *in the flow*, not a modal, with: what it wants to do, the exact target, and Allow / Deny. Optional: "Allow for this session".
- Keyboard: Enter allows, Escape denies, when the block is focused. Focus moves to it when it appears.
- Once decided, the block collapses to one line: "Allowed: run `npm test`".
- Never auto-approve because the user was slow. Timeouts deny.
- Show the diff for file writes, the recipient for messages, the amount for spends.

## Sources and citations

- Inline markers (`[1]`) link to a sources list at the end of the message; hovering a marker previews the source.
- The sources list shows favicon, title, host. Clicking opens in a new tab.
- Quotes from sources are visually distinct (border-left, muted) and short.
- No source, no claim of one. Do not render a citation UI for text that was not grounded.

## Message actions

On hover (or always, on touch): copy, regenerate, edit-and-resend (user messages), thumbs. Small icon buttons in a row under the message, 28px targets, appearing without shifting the layout (reserve the row height).

Copy copies the markdown source, not the rendered text, and confirms with an icon swap.

## Scroll

- Pin to bottom while streaming *only if* the user is already at the bottom. The moment they scroll up, stop pinning.
- Show a "↓ Jump to latest" pill when unpinned and new content arrives.
- New messages from the user scroll their own message to the top of the viewport, leaving room for the reply to grow beneath.
- `overflow-anchor` and stable heights above the viewport so old content does not shift as images and code blocks render.

## Latency

- First token later than 400ms: the assistant message appears immediately as an empty bubble with the cursor. Do not wait for content to show the turn started.
- Tool-heavy turns: the tool rows *are* the progress indicator.
- Rate limits and queues: say so, with a time if known. "In queue, ~20s" beats a spinner.

## Errors and interruptions

- Stopped by the user: the partial message stays, marked "Stopped". It can be continued.
- Failed mid-stream: the partial message stays, with the error beneath and a Retry that resends the same prompt.
- Context limits: warn before, not after. Offer to summarise or start fresh.
- Never clear the user's last message on failure.

## Markdown rendering

- Code blocks: language label, copy button, horizontal scroll, no wrap, 12.5–13px mono, max ~30 lines before collapsing after stream end.
- Tables: horizontal scroll on narrow screens, header row distinct, numbers tabular.
- Headings inside a reply are one to two levels smaller than the app's headings; a reply is not a page.
- Links open in a new tab with an external marker.
- Sanitise. Rendered output is untrusted.

## Multi-agent and long-running

- A run has a header: status, elapsed, stop. Steps are the tool-call rows above, grouped by phase.
- Background runs surface a small persistent indicator (a dot on the nav item) and a notification on completion, not a modal.
- History is a list of runs with status and a one-line summary, searchable.

## Honesty

- Label the model and mode in use. Show when a message was edited or regenerated.
- Distinguish what the agent *did* from what it *says it did*: tool rows are ground truth; prose is prose.
- "Working…" with nothing happening for 30 seconds is a bug. Either show what is happening or say it is stuck.

## Checklist

- [ ] Stop is always visible and Escape works
- [ ] Composer grows, persists, and regains focus
- [ ] Streaming batches to frames; no per-character animation
- [ ] Thinking collapses to a one-line duration
- [ ] Tool calls are rows with status; groups collapse
- [ ] Approvals are inline, keyboardable, and time out to deny
- [ ] Scroll pins only when at the bottom; jump pill otherwise
- [ ] Partial output survives stop and failure
- [ ] Markdown is sanitised, code blocks copy, tables scroll
- [ ] Nothing spins without a reason the user can see

## From the catalogue

- [Beautiful UI](https://www.beautifului.dev) — traces, tool chips, approvals as primitives.
- [AI Elements](https://ai-sdk.dev/elements) — chat, reasoning and sources components from the AI SDK.
- [assistant-ui](https://www.assistant-ui.com), [prompt-kit](https://www.prompt-kit.com) — composable chat primitives.
- [AICSS](https://aicss.dev) — thinking states, tool calls and citations in React, Vue and Svelte.
- [Amelia Wattenberger](https://wattenberger.com) — writing on interfaces for AI beyond the chat box.
- More at [stealth.pm/components](https://stealth.pm/components#agent-ui).
