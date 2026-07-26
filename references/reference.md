# Reference

One file, read once. Covers: skeleton syntax, verified rendering pitfalls,
color formula, emoji picking.

## Skeleton

~~~markdown
# <Topic Title>

## MindMap

```mermaid
%%{ init: { 'theme': 'base', 'themeVariables': { 'primaryColor': '<dark hex>', 'primaryTextColor': '#FAFAFA', 'secondaryColor': '<dark hex>', 'tertiaryColor': '<dark hex>' } } }%%
mindmap
  root(("`<div style='font-size:2.2em;line-height:1.1'><emoji></div><b><Topic Title></b>`"))
    ("`<emoji> <Branch 1 keyword>`")
      ("`<emoji> <named leaf concept>`")
      <generic descriptive leaf, no emoji needed, plain text>
    ("`<emoji> <Branch 2 keyword>`")
      ("`<emoji> <named leaf concept>`")
```
~~~

No `## Reference`/source section unless there's a real link to add — don't
invent one.

## Why emoji, not FontAwesome `::icon()`

This skill originally used `::icon(fa fa-name)` — the classic approach, and
what Mermaid's own docs still describe. But direct testing found it
doesn't actually work: rendering test files through mermaid 11.12 and
11.16, in a real browser (Playwright, inspecting the live DOM), with the
classic CSS-class syntax, the newer icon-pack system
(`mermaid.registerIconPacks()`, both the `fa fa-name` and bare `fa name`
forms), and `securityLevel: 'loose'` — every combination produced **zero**
icon-related markup in the rendered SVG. Not a CORS issue, not a font
issue, not a CSP issue: the renderer itself doesn't emit anything for
`::icon()` on mindmap diagrams right now. That's a bug in mermaid, not a
config problem on our end, so don't try to resurrect `::icon()` — use
emoji in the label text instead, confirmed working the same way (rendered
and visually verified via screenshot).

## Verified pitfalls (found by rendering real test files, not guessed)

**No `config: layout: tidy-tree` frontmatter.** That layout engine is a
separate npm package (`@mermaid-js/layout-tidy-tree`) most Mermaid
renderers don't ship with — hard syntax error outside a handful of
environments that specifically register it. Plain `mindmap`, no layout
override, renders everywhere.

**Shape is the main size lever, but root's emoji gets one exception.**
Mindmap has no font-size-per-depth setting for node shapes themselves.
Measuring actual rendered SVG output: a double-paren circle ≈ 140-170px, a
single-paren rounded box ≈ 170×54, a plain-text leaf smaller still. So
branch/leaf visual hierarchy comes from shape choice:
- Branches: rounded box — `(Title)`.
- Leaves: plain text, no shape — this is already the smallest, don't add a
  shape just for consistency.

Root is the one place worth a real size boost, verified working: the
quoted markdown-string form passes raw HTML through (mermaid's markdown-
string nodes render via `marked`, which doesn't sanitize by default), so
wrap root's emoji in a sized `<div>` on its own line, then bold the title
with `<b>` — not markdown `**bold**`, which breaks (renders literal
asterisks) once raw HTML is already in the same label, confirmed by
rendering:
```
root(("`<div style='font-size:2.2em;line-height:1.1'>🔥</div><b>Personal Finance</b>`"))
```
This makes root's emoji large and stacked above the bold title — the most
visually prominent node, as intended. Keep branches/leaves as plain inline
`emoji + text` (no div, no size bump) — this treatment is for root only.

**Emoji, non-ASCII characters, and hyphens all break plain/unquoted node
text** in Mermaid versions still common in markdown previewers (they lex
unquoted text as ASCII-only, and hyphens have separately been observed to
break the same unquoted path). Since almost every node now carries an
emoji, quoting becomes the default for most labels:
- Wrap in Mermaid's quoted markdown-string form, inside any shape, id
  optional: `("`text`")`. Quoting works in every shape (circle/round/
  square), so keep using the same root/branch/leaf shape hierarchy above
  even for quoted nodes — a quoted round shape is still visually smaller
  than a quoted square-bracket box.
- A hyphen with no emoji can instead be reworded to drop it ("Client
  specific interfaces" not "Client-specific interfaces") if that's simpler
  than quoting — but once a node has an emoji, quote it, don't bother
  trying to reword around the emoji.

Only quote the nodes that actually need it — a leaf with plain ASCII text
and no emoji can stay in normal unquoted style.

**Never split a label across two raw source lines**, even a long root
title — Mermaid reads tree structure from each line's indentation, so a
wrapped continuation line is misread as a new sibling/child. Shorten the
wording instead.

**Branch fan-out ≤ ~6 direct children.** More than that (e.g. 11 items in
one category) crowds nodes until they visually overlap — split into named
sub-groups instead of one flat list.

## Optional contrast sub-pattern

When a branch splits into two opposing sides (High/Low, Pros/Cons,
Before/After) — only when the topic genuinely has two poles, not forced:

```
    ("`<emoji> <Branch keyword>`")
      ("`➕ <Side A label>`")
        <example>
      ("`➖ <Side B label>`")
        <example>
```

➕/➖ read universally as "more of this"/"less of this".

## Color palette formula

Keys needed (all hex): `primaryColor`, `primaryTextColor`, `secondaryColor`,
`tertiaryColor`. That's it — four keys, not twelve. `secondaryTextColor`,
`tertiaryTextColor`, `mindmapRootColor`, `mindmapMainColor`,
`mindmapSecondaryColor`, `mindmapTertiaryColor`, `mindmapTextColor`, and
`mindmapLineColor` are **verified dead**: rendered real multi-branch test
files and inspected the actual DOM/CSS mermaid produces (across mermaid
11.12 and the current 11.16) — none of those eight keys' hex values ever
show up anywhere in the output, on any version. Don't set them; they're
pure token waste and — worse — they're what caused the actual contrast bug
(see below).

**Verified mechanics** (found by inspecting real rendered output, not
guessed from docs):
- `primaryColor`/`secondaryColor`/`tertiaryColor` only seed each branch's
  *hue*. Mermaid regenerates the actual fill lightness/saturation itself via
  an internal per-branch hue-rotation (root ≈ primaryColor's hue, branch 1 ≈
  secondaryColor's hue, branch 2 ≈ tertiaryColor's hue, branch 3+ keep
  auto-rotating hue +30° with no color input from us at all) — the exact
  lightness you pick for secondary/tertiary does not reliably survive into
  the render.
- `primaryTextColor` is applied to **every** node's label text — root,
  every branch, every leaf, uniformly, confirmed in current mermaid (11.16).
  There is no per-depth text color; the render only has one lever for text.

**This is the actual bug users hit.** The old guidance here said "give
`mindmapSecondaryColor`/`mindmapTertiaryColor` a lighter fill for deeper
nesting" — but since every node's text uses the same single
`primaryTextColor`, making a branch's fill lighter just means *that
branch's text* (still whatever `primaryTextColor` is) loses contrast
against it. That's exactly "keyword color looks the same as its branch."

**Fix, verified by rendering real 2-branch and 5-branch test files and
measuring actual contrast ratios on the output:**
1. Keep `primaryColor`/`secondaryColor`/`tertiaryColor` all at *similar,
   dark* lightness (roughly 20-35% — a deep, rich version of your mood hue).
   Vary hue between them for visual distinction, not lightness. Do NOT build
   an ascending "lighter ramp for deeper nesting" — that's the exact
   mechanism that breaks contrast, per above.
2. Set `primaryTextColor` to a fixed near-white, `#FAFAFA`. With every
   branch kept dark per step 1, this reads cleanly everywhere — verified
   across 2-branch and 5-branch renders, contrast ratios 7:1-12:1 on every
   node, comfortably clearing WCAG AA's 4.5:1.

Generate a fresh hue per topic — don't reuse a previous note's exact hex
values.

Generate a fresh ramp per topic — don't reuse a previous note's exact hex
values unless the mood genuinely matches.

## Picking emoji

For each node that gets one, ask: what concrete object/action/symbol does
this abstract idea bring to mind? Concrete keyword → its literal emoji.
Abstract → closest physical metaphor (growth → 🌱📈, risk → ⚠️🛡️, time →
⏰⏳, structure → ✅📋). Prefer specific over generic ("curiosity" → 🔍, not
a generic ⭐). If two siblings end up with similar emoji, at least one was
too generic — find something sharper.

Cheat sheet (not closed — pick a sharper one if it fits better):

| Concept | Emoji |
|---|---|
| Brain / cognition | 🧠 |
| Growth / progress | 🌱, 📈 |
| Curiosity | 🔍, 🧭 |
| Creativity | 🎨, 💡 |
| Discipline / structure | ✅, 📏 |
| Goal / target | 🎯, 🏁 |
| Social / people | 👥, 🤝 |
| Communication | 💬, 📢 |
| Emotion | 😊, ❤️, 💔 |
| Trust / cooperation | 🤝 |
| Competition / conflict | ♟️, 👊 |
| Anxiety / stress | ⚡, ⚠️ |
| Calm / resilience | 🧘, 🛡️ |
| Time / schedule | ⏰, ⏳ |
| Habit / routine | 🔁, 📅 |
| Risk / caution | ⚠️, 🛡️ |
| Energy | ⚡, 🔥 |
| Rest / recovery | 🛏️, 🔋 |
| Ranking / scale | ⭐, 📊 |
| Money / value | 💰, 🪙 |
| Health / body | ❤️‍🩹, 💪 |
| Decision / choice | 🔀, 🧭 |
| Learning | 📖, 🎓 |
| Food / diet | 🍽️, 🥗 |
| Travel | ✈️, 🧳 |
| Technology / code | 💻, 🔧 |
