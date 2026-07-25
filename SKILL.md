---
name: dmindmap
description: Generates a Mermaid mindmap decorated with emoji for any topic, where every branch keyword gets an emoji whose real-world meaning reinforces that keyword so the map is easier to memorize. Use whenever the user asks for a mindmap, mind map, concept map, or visual summary of a topic for their notes vault (Obsidian/markdown), asks to "turn X into a mindmap", wants icons/emoji next to mindmap nodes, or references an existing mindmap note (like an OCEAN/Big Five style file) as a pattern to follow for a new topic. Also trigger when the user wants a memorable/visual way to study or memorize a topic's structure.
---

# Mermaid Mindmap with Emoji

Build a Mermaid `mindmap` where nearly every node's label starts with an
emoji chosen for its *meaning*, not decoration — a good emoji is a memory
hook. Picking emoji well is the hard part; syntax is the easy part (and
every syntax rule below was found by actually rendering test files, not
guessed from docs — follow them exactly, they're not stylistic opinions).

**Use emoji, not FontAwesome `::icon()`.** This skill originally used
`::icon(fa fa-name)`, but direct browser testing (Playwright, real DOM
inspection across mermaid 11.12 and 11.16, with and without icon-pack
registration, at every security level) confirmed mermaid's mindmap
renderer emits *zero* icon-related markup for `::icon()` — a real bug in
mermaid itself for this diagram type, not a config issue. Emoji are plain
Unicode text embedded in the label, so they render anywhere the diagram
renders, with no external dependency and no risk of this breaking again.

Read `references/reference.md` once before writing anything — it has the
full skeleton, every verified pitfall, the color formula, and emoji-picking
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
4. **Node tree**: root = topic. Give an emoji to every branch and every
   leaf that names a distinct concept. Cap direct children per branch at
   ~6, splitting into sub-groups otherwise (reference.md explains why).
5. **Emoji**: one per branch/keyword, placed as the first character(s) of
   that node's label — use the reasoning method + cheat sheet in
   reference.md rather than picking generic/mismatched ones.
6. **Assemble**: follow the skeleton in reference.md exactly — no
   `layout: tidy-tree`, root/branch/leaf shape hierarchy, one label per
   source line.
7. **Quote every label with an emoji, non-ASCII character, or hyphen** per
   reference.md — with emoji on almost every node, that's most labels now;
   a leaf can stay unquoted plain text only if it's genuinely plain
   descriptive ASCII with no emoji.
8. Only add a `## Reference`/source section if there's a real link to
   include.

## Before finishing, check

- Emoji on every branch + named leaf; no two siblings share one.
- No branch exceeds ~6 direct children.
- Every label on one source line, no hyphens/non-ASCII/emoji left unquoted.
- Root/branch/leaf shape hierarchy intact (circle → round → plain).
- Palette suits this topic, not copied from another note.
- No `::icon(...)` anywhere in the output.
