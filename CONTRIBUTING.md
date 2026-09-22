# Contributing

Thanks for reading this first.

## What gets merged

A change earns its place when it fixes a decision an agent gets wrong without it. Show the wrong output and the right one. "I prefer 8px" is not a change; "agents produce 13px, 18px and 22px gaps on the same screen, here is the scale that stops that" is.

Good changes:

- A value that is wrong or too loose (a duration, a size, a ratio, a contrast figure), with the reasoning.
- An anti-pattern that generated UI produces often and no skill names yet.
- A gate or ordering that stops an agent from doing work it should not have started.
- A source in **From the catalogue** that is better than the one there.
- A correction of fact.

Not merged:

- Preferences without a failure they prevent.
- New skills that overlap an existing one. Extend the existing one.
- Framework-specific rewrites. The skills are written for the web; recipes may mention React and Motion where a concrete example helps, but the rules are not tied to them.
- Anything that softens the voice. Short, declarative, no "consider".

## Shape

Each skill is `skills/<name>/SKILL.md` with frontmatter:

```yaml
---
name: <name>
description: <one paragraph: what it decides and when to load it>
---
```

Then the body in the shared shape: a gate, the decision order, tables of values, anti-patterns, a checklist, and **From the catalogue** at the end. Read `skills/motion/SKILL.md` for the reference.

Keep tables narrow enough to read in a terminal. Keep code samples under fifteen lines. Sentence case everywhere.

## Proposing a new skill

Open an issue first with: the decisions it covers, why none of the fifteen cover them, and three examples of an agent getting them wrong today. If it holds up, write it in the shape above.

## Testing

Install your branch into an agent and give it a real task that the change should affect:

```bash
npx skills add <you>/stealth-skills#<branch> --skill <name>
```

Include the before and after output in the pull request.
