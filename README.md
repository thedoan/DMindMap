# DMindMap

A [Claude Code](https://claude.com/claude-code) skill that generates Mermaid
mindmaps decorated with [FontAwesome](https://fontawesome.com/) icons for any
topic — each branch and named leaf gets an icon whose real-world meaning
reinforces the keyword, so the map doubles as a memory aid.

## What it does

Given a topic, the skill:

- Breaks it into 3-7 main branches (splitting further into sub-groups when a
  category has more than ~6 items)
- Picks a color palette that fits the topic's mood (calm, energetic,
  technical, etc.) instead of reusing a fixed theme
- Assigns one FontAwesome solid icon per branch/leaf, chosen for what it
  concretely represents — not decoration
- Assembles a ready-to-drop-in Markdown file: FontAwesome CDN link + a
  Mermaid `mindmap` code block

## Why this exists

Building one of these by hand is easy to get subtly wrong in ways that don't
show up until you actually render the diagram. This skill encodes a set of
constraints found by testing real output against Mermaid (verified with
`@mermaid-js/mermaid-cli`, not just assumed from documentation):

- **No `layout: tidy-tree`** — that layout engine is a separate npm package
  most Mermaid renderers don't have installed, so it hard-fails outside a
  handful of environments.
- **Unicode-safe quoting** — many Mermaid versions still in common use only
  lex plain/unquoted node text as ASCII; non-Latin scripts (and even a bare
  hyphen) need Mermaid's quoted "markdown string" node form instead.
- **Shape-based visual hierarchy** — Mermaid's mindmap has no per-depth
  font-size control, so the root/branch/leaf size hierarchy comes from shape
  choice (circle → rounded box → plain text), confirmed by measuring actual
  rendered SVG output.

## Structure

```
SKILL.md                        Entry point Claude Code loads — workflow only
references/
  reference.md                  Everything else, read once: node syntax
                                 skeleton, verified pitfalls (shape/size
                                 hierarchy, Unicode/hyphen-safe quoting,
                                 no tidy-tree), color formula, icon-picking
                                 guide + FontAwesome class cheat sheet
evals/
  evals.json                    Test prompts used to validate the skill
```

`SKILL.md` and `references/reference.md` used to be four separate files;
they were consolidated to cut duplicated rationale and reduce the number of
file reads needed per mindmap (~55% smaller overall, same rules).

## Install

Copy this repo's contents into a skill directory Claude Code will discover,
e.g.:

```bash
git clone git@github.com:thedoan/DMindMap.git ~/.claude/skills/mermaid-mindmap-fontawesome
```

Then ask Claude Code for a mindmap on any topic — it will pick up the skill
automatically.

## License

MIT — see [LICENSE](./LICENSE).
