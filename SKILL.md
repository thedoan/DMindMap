---
name: mermaid-mindmap-fontawesome
description: Generates a Mermaid mindmap decorated with FontAwesome icons for any topic, where every branch keyword gets an icon whose real-world meaning reinforces that keyword so the map is easier to memorize. Use whenever the user asks for a mindmap, mind map, concept map, or visual summary of a topic for their notes vault (Obsidian/markdown), asks to "turn X into a mindmap", wants icons next to mindmap nodes, or references an existing mindmap note (like an OCEAN/Big Five style file) as a pattern to follow for a new topic. Also trigger when the user wants a memorable/visual way to study or memorize a topic's structure.
---

# Mermaid Mindmap with FontAwesome Icons

Build a Mermaid `mindmap` where nearly every node carries a FontAwesome icon
chosen for its *meaning*, not decoration — a good icon is a memory hook.
Picking icons well is the hard part; syntax is the easy part (and every
syntax rule below was found by actually rendering test files, not guessed
from docs — follow them exactly, they're not stylistic opinions).

Read `references/reference.md` once before writing anything — it has the
full skeleton, every verified pitfall, the color formula, and icon-picking
guidance. This file is just the workflow order.

## Workflow

1. **Scope the topic**: 3-7 main branches. Vague topic → pick a sensible
   breakdown yourself; only ask the user when it's genuinely ambiguous or
   you need a destination path.
2. **Destination**: default to a new note mirroring this vault's existing
   ones (e.g. `Notes/Psychology/BigFivePersonalityTraits.md`). Infer a
   sensible folder from the topic if not given. If the user points at an
   existing file, replace just its `## MindMap` section.
3. **Color palette**: pick a fresh one per topic's mood (formula in
   reference.md) — never reuse a previous note's exact hex values.
4. **Node tree**: root = topic. Icon every branch and every leaf that
   names a distinct concept. Cap direct children per branch at ~6,
   splitting into sub-groups otherwise (reference.md explains why).
5. **Icons**: one per branch/keyword, `fa-solid` only — use the reasoning
   method + cheat sheet in reference.md rather than guessing class names.
6. **Assemble**: follow the skeleton in reference.md exactly — no
   `layout: tidy-tree`, root/branch/leaf shape hierarchy, one label per
   source line.
7. **Quote non-ASCII or hyphenated labels** per reference.md — reword to
   drop a hyphen when possible, otherwise use the quoted form.
8. Only add a `## Reference`/source section if there's a real link to
   include.

## Before finishing, check

- Icon on every branch + named leaf; no two siblings share one (except the
  `fa-plus`/`fa-minus` pair).
- No branch exceeds ~6 direct children.
- Every label on one source line, no hyphens/non-ASCII left unquoted.
- Root/branch/leaf shape hierarchy intact (circle → round → plain).
- Palette suits this topic, not copied from another note.
