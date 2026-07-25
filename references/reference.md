# Reference

One file, read once. Covers: skeleton syntax, verified rendering pitfalls,
color formula, emoji picking.

## Skeleton

~~~markdown
# <Topic Title>

## MindMap

```mermaid
%%{ init: { 'theme': 'base', 'themeVariables': { 'primaryColor': '<hex>', 'primaryTextColor': '<hex>', 'secondaryColor': '<hex>', 'secondaryTextColor': '<hex>', 'tertiaryColor': '<hex>', 'tertiaryTextColor': '<hex>', 'mindmapRootColor': '<hex>', 'mindmapMainColor': '<hex>', 'mindmapSecondaryColor': '<hex>', 'mindmapTertiaryColor': '<hex>', 'mindmapTextColor': '#333333', 'mindmapLineColor': '<hex>' } } }%%
mindmap
  root(("`**<emoji> <Topic Title>**`"))
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

**Shape is the only size lever.** Mindmap has no font-size-per-depth
setting. Measuring actual rendered SVG output: a double-paren circle ≈
140-170px, a single-paren rounded box ≈ 170×54, a plain-text leaf smaller
still. So root/branch/leaf visual hierarchy comes entirely from shape
choice:
- Root: circle + bold — `root(("`**Title**`"))`.
- Branches: rounded box — `(Title)`.
- Leaves: plain text, no shape — this is already the smallest, don't add a
  shape just for consistency.

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

Keys needed (all hex): `primaryColor`, `primaryTextColor`,
`secondaryColor`, `secondaryTextColor`, `tertiaryColor`, `tertiaryTextColor`,
`mindmapRootColor`, `mindmapMainColor`, `mindmapSecondaryColor`,
`mindmapTertiaryColor`, `mindmapTextColor`, `mindmapLineColor`.

1. Pick a mood → base hue: energetic/bold → red-orange (0-30°); calm →
   blue-teal (180-200°); growth/health → green (90-140°); technical →
   blue-slate (210-230°); creative/social → yellow-orange (30-45°);
   serious/analytical → purple-indigo (250-270°).
2. Build an analogous 3-step ramp (base, +30°, +60°) for
   primary/secondary/tertiary. `mindmapRootColor`/`mindmapMainColor` = most
   saturated step. `mindmapSecondaryColor`/`mindmapTertiaryColor` = lighter
   steps for deeper nesting.
3. **Pair each fill with text by that fill's own lightness, not a fixed
   rule.** A dark fill needs pale text. But `tertiaryColor` is usually
   already your lightest ramp step — pairing it with an even paler text
   color washes out to unreadable (a real bug found by testing); give a
   light fill dark/saturated text instead. Eyeball each pair: if you can't
   tell them apart, darken the text regardless of which step it is.
4. `mindmapTextColor` (leaf text): dark gray `#333333` — assumes a light
   viewer background, the safe default.
5. `mindmapLineColor`: a mid-tone from the ramp.

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
