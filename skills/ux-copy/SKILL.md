---
name: ux-copy
description: Interface writing. Buttons that are verbs, sentence case, errors that say what happened and what to do, empty states with one action, confirmations that name the thing, tone without exclamation marks, numbers and dates formatted for humans, placeholders versus labels, length limits, and the words to never use. Use when writing or reviewing any text in an interface: labels, buttons, menus, errors, empty states, toasts, dialogs, onboarding, tooltips.
---

# ux-copy

Interface text is read in fragments, by people doing something else. It is not prose. Every word has to earn its place by helping them finish.

## Rules that never bend

1. **Sentence case.** "Save changes", "Create project", "Sign in". Title Case Reads As Marketing.
2. **Buttons are verbs.** The label says what happens: "Delete", "Send invite", "Copy link". Never "OK", "Yes", "Submit", "Confirm" alone.
3. **Say what happened, then what to do.** Errors, empty states and toasts all follow this order.
4. **No exclamation marks.** No "Oops", no "Uh oh", no "Awesome", no "Yay". The interface is calm.
5. **No "please", no "sorry".** "Enter your email", not "Please enter your email". "Couldn't save", not "Sorry, we couldn't save".
6. **No "successfully".** "Saved", not "Successfully saved". The past tense is the success.
7. **Name the thing.** "Delete *Q3 report*?" not "Are you sure?". "Invite sent to alex@…" not "Invite sent".
8. **One idea per line.** If a sentence has "and", it is probably two.
9. **The user is "you". The product is "we" only when it did something.** "We couldn't reach the server" is fine. "We're excited to…" is not.
10. **Numbers are digits.** "3 items", not "three items". "1 item", not "1 item(s)".

## Buttons

| Situation | Label |
| --- | --- |
| Save a form | Save changes |
| Create a thing | Create project / New project (menu) |
| Destructive | Delete project (the verb, and the noun if there is space) |
| Cancel | Cancel (never "Close" for a form; "Close" is for information) |
| Confirm a destructive dialog | The verb: Delete, Remove, Revoke. Never "Yes" |
| Send | Send / Send invite |
| Continue in a flow | Continue (not "Next" unless steps are numbered) |
| Finish a flow | Done / Finish setup |
| Sign in / out | Sign in, Sign out (not Log in, Login, Logout) |
| Sign up | Create account |
| Copy | Copy link / Copy code (then "Copied" for ~1.5s) |
| Retry | Try again |
| Learn more | Say what they will learn: "How billing works", not "Learn more" |

Two buttons side by side: primary is the verb, secondary is "Cancel". Not "Yes" / "No". Not two verbs of equal weight unless it is a real choice ("Save draft" / "Publish").

## Labels and fields

- Labels are nouns, short, no colon: "Email", "Display name", "Team".
- Hints go under the field, one line, when the format is not obvious: "Used for sign-in and receipts".
- Placeholders are examples, not instructions: `name@company.com`, not "Enter your email". Never the only label.
- Required marker once, explained once: "* Required". Or mark optional fields "(optional)" instead, if most are required.
- Character limits shown as "12/80" only near the limit.

## Errors

Three parts, in order, only the first is mandatory:

1. **What happened.** "Couldn't save your changes." "That name is taken." "The file is too large."
2. **Why, if it helps.** "You're offline." "Names must be unique in a team." "Limit is 10 MB."
3. **What to do.** "Try again", "Choose another name", "Compress it or pick a smaller file", as an action where possible.

| Not this | This |
| --- | --- |
| Something went wrong | Couldn't load your projects. Try again. |
| Invalid input | Enter a valid email address |
| Error 500 | The server didn't respond. Try again in a minute. |
| Oops! Please try again later! | Couldn't send the invite. Try again. |
| You don't have permission | Only owners can delete projects. Ask an owner. |
| Field required | Enter a name |

Field errors are short and specific: "Enter a name", "Use at least 8 characters", "Doesn't match". No period on a fragment; a period on a sentence.

## Empty states

One line saying what will be here, one action that fills it, an optional line of context.

- "No projects yet" · **Create project**
- "No results for *typo*" · **Clear search**
- "Nobody has commented" · **Add a comment**
- "Your inbox is empty" · (no action needed; this is good news, say nothing else)

Not: "It looks like you don't have any projects yet! Get started by creating your first project below."

## Confirmations

Only for the irreversible. The dialog:

- Title is the action with the object: "Delete *Q3 report*?"
- Body is the consequence in one line: "This removes it for everyone on the team. This can't be undone."
- Buttons: "Delete" (danger) and "Cancel". Never "Are you sure?" as the title; never "Yes"/"No".

Prefer undo: do it, toast "Deleted *Q3 report*" with **Undo** for six seconds.

## Toasts

- One line of what happened: "Link copied", "Invite sent to alex@…", "Saved".
- Optional second line for where or what: the URL host, the destination.
- Optional action: **Undo**, **View**, **Open**.
- Never for things the user can already see changed in place.
- Never "Success!"

## Menus and navigation

- Menu items are verbs for actions ("Rename", "Duplicate", "Move to…") and nouns for destinations ("Settings", "Billing").
- "…" on any item that opens a further step. No "…" on items that act immediately.
- Destructive items last, separated, in the danger colour.
- Nav labels are single nouns: "Projects", "People", "Settings". Not "My projects", not "Project list".

## Onboarding and marketing inside the product

- The first screen says what to do, not what the product is. They already signed up.
- Tooltips on first use are one line and dismiss on the first interaction.
- Feature announcements are one line with a link, in a place that does not block the work.
- No "Welcome to X! We're thrilled to have you". "Create your first project" is the welcome.

## Tone

Calm, direct, specific. Written by a competent colleague who respects your time. The test: read it aloud in a flat voice. If it sounds strange flat, it is too excited for an interface.

Words to delete on sight: *just, simply, easily, quickly, please, sorry, oops, awesome, amazing, seamless, powerful, robust, leverage, utilize, in order to, successfully, currently, actually.*

## Numbers, dates, units

- Digits always. Thousands separated for the locale. "1,240 items".
- Relative time when recent ("4m ago", "Yesterday"), absolute on hover and after seven days ("12 Sep 2026").
- Durations: "2h 14m", not "2 hours and 14 minutes" in UI.
- Units with a space and a short form: "10 MB", "3.2 GB", "12 ms".
- Ranges with an en dash: "9–5", "Mon–Fri".
- Counts in labels: "3 selected", "12 people". Handle 0 and 1: "No items", "1 item", "2 items".
- Percentages without decimals unless precision matters: "42%".
- Currency with the symbol and the locale's format; never trailing ".00" in summaries.

## Punctuation

- Real characters: ’ “ ” — – …
- No period on labels, buttons, titles or single-line hints. A period on full-sentence body text and multi-sentence messages.
- No colon after labels.
- Oxford comma in lists of three or more.
- One space after a period.

## Length

- Button: 1–3 words.
- Label: 1–3 words.
- Toast: under 40 characters on the first line.
- Error: under 80 characters, or two lines with an action.
- Tooltip: one line, under 60 characters.
- Empty state: title under 40 characters, action under 3 words.
- Dialog body: under 140 characters.

If it does not fit, the design needs a different element, not smaller text.

## Internationalisation

- Leave 30–40% room for German and Finnish. Do not fix widths to English strings.
- Do not build sentences from fragments in code ("You have " + n + " items"); use full strings with placeholders.
- Do not rely on capitalisation, word order or punctuation that other languages lack.
- Dates, numbers, currencies and plurals through `Intl`, never hand-formatted.

## Checklist

- [ ] Every button is a verb in sentence case
- [ ] Every error says what happened and, where possible, what to do
- [ ] Every empty state has one action
- [ ] No exclamation marks, no "please", no "sorry", no "successfully"
- [ ] Confirmations name the object and use the verb
- [ ] Numbers are digits; 0 and 1 are handled
- [ ] Real punctuation characters
- [ ] Strings fit with 30% growth

## From the catalogue

- [Nielsen Norman Group](https://www.nngroup.com/articles) — error message and microcopy research.
- [Apple Human Interface Guidelines: Writing](https://developer.apple.com/design/human-interface-guidelines/writing) — the clearest short guide to interface voice.
- [Page Flows](https://pageflows.com) — see the copy real products use at each step.
- [Design Spells](https://www.designspells.com) — small writing details that land.
- More at [stealth.pm/learn](https://stealth.pm/learn#guidelines).
