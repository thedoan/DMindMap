# Reference

One file, read once. Covers: skeleton syntax, verified rendering pitfalls,
color formula, icon picking.

## Skeleton

~~~markdown
# <Topic Title>

## MindMap

<link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css" rel="stylesheet" />

```mermaid
%%{ init: { 'theme': 'base', 'themeVariables': { 'primaryColor': '<hex>', 'primaryTextColor': '<hex>', 'secondaryColor': '<hex>', 'secondaryTextColor': '<hex>', 'tertiaryColor': '<hex>', 'tertiaryTextColor': '<hex>', 'mindmapRootColor': '<hex>', 'mindmapMainColor': '<hex>', 'mindmapSecondaryColor': '<hex>', 'mindmapTertiaryColor': '<hex>', 'mindmapTextColor': '#333333', 'mindmapLineColor': '<hex>' } } }%%
mindmap
  root(("`**<Topic Title>**`"))
  ::icon(fa fa-<root-icon>)
    (<Branch 1 keyword>)
    ::icon(fa fa-<icon>)
      <named leaf concept>
      ::icon(fa fa-<icon>)
      <generic descriptive leaf, no icon needed>
    (<Branch 2 keyword>)
    ::icon(fa fa-<icon>)
      <named leaf concept>
      ::icon(fa fa-<icon>)
```
~~~

No `## Reference`/source section unless there's a real link to add — don't
invent one.

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

**Non-ASCII characters and hyphens break plain/unquoted node text** in
Mermaid versions still common in markdown previewers (they lex unquoted
text as ASCII-only, and hyphens have separately been observed to break the
same unquoted path). Two fixes:
- Reword to drop the hyphen when meaning survives ("Client specific
  interfaces" not "Client-specific interfaces") — simplest, keeps the
  node's natural shape.
- Otherwise wrap in Mermaid's quoted markdown-string form, inside any
  shape, id optional: `("`text`")`. This also fixes Vietnamese/CJK/any
  non-ASCII label. Quoting works in every shape (circle/round/square), so
  keep using the same root/branch/leaf shape hierarchy above even for
  quoted nodes — a quoted round shape is still visually smaller than a
  quoted square-bracket box.

Only reword/quote the nodes that actually need it — leave safe ASCII
labels in their normal unquoted style.

**Never split a label across two raw source lines**, even a long root
title — Mermaid reads tree structure from each line's indentation, so a
wrapped continuation line is misread as a new sibling/child. Shorten the
wording instead.

**`::icon(fa fa-name)`** goes on its own line immediately after the node it
decorates, same indent as that node's continuation.

**Branch fan-out ≤ ~6 direct children.** More than that (e.g. 11 items in
one category) crowds nodes/icons until they visually overlap — split into
named sub-groups instead of one flat list.

## Optional contrast sub-pattern

When a branch splits into two opposing sides (High/Low, Pros/Cons,
Before/After) — only when the topic genuinely has two poles, not forced:

```
    (<Branch keyword>)
    ::icon(fa fa-<branch icon>)
      <Side A label>
      ::icon(fa fa-plus)
        <example>
      <Side B label>
      ::icon(fa fa-minus)
        <example>
```

`fa-plus`/`fa-minus` read universally as "more of this"/"less of this".

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

## Picking icons

For each node that gets one, ask: what concrete object/action/symbol does
this abstract idea bring to mind? Concrete keyword → its literal icon.
Abstract → closest physical metaphor (growth → plant/rising arrow, risk →
warning triangle, time → clock/hourglass, structure → checklist/blueprint).
Prefer specific over generic ("curiosity" → magnifying glass, not a
generic star). If two siblings end up with similar icons, at least one was
too generic — find something sharper.

Only `fa-solid` (free) icons, `fa fa-<name>` form — no `fa-brands`/logos,
no Pro-only icons (won't render with the free CDN link).

Cheat sheet (not closed — search
https://fontawesome.com/search?o=r&s=solid&f=classic for anything else):

| Concept | Icon class |
|---|---|
| Brain / cognition | `fa-brain` |
| Growth / progress | `fa-seedling`, `fa-arrow-trend-up` |
| Curiosity | `fa-magnifying-glass`, `fa-compass` |
| Creativity | `fa-palette`, `fa-lightbulb` |
| Discipline / structure | `fa-clipboard-check`, `fa-ruler` |
| Goal / target | `fa-bullseye`, `fa-flag-checkered` |
| Social / people | `fa-people-group`, `fa-handshake` |
| Communication | `fa-comments`, `fa-message` |
| Emotion | `fa-face-smile`, `fa-heart`, `fa-heart-crack` |
| Trust / cooperation | `fa-handshake`, `fa-people-arrows` |
| Competition / conflict | `fa-chess-knight`, `fa-fist-raised` |
| Anxiety / stress | `fa-bolt`, `fa-triangle-exclamation` |
| Calm / resilience | `fa-spa`, `fa-shield-heart` |
| Time / schedule | `fa-clock`, `fa-hourglass-half` |
| Habit / routine | `fa-repeat`, `fa-calendar-check` |
| Risk / caution | `fa-triangle-exclamation`, `fa-shield` |
| Energy | `fa-bolt`, `fa-fire` |
| Rest / recovery | `fa-bed`, `fa-battery-full` |
| Ranking / scale | `fa-ranking-star`, `fa-gauge` |
| High / increase | `fa-plus`, `fa-arrow-up` |
| Low / decrease | `fa-minus`, `fa-arrow-down` |
| Code / logic | `fa-code`, `fa-diagram-project` |
| Money / value | `fa-coins`, `fa-sack-dollar` |
| Health / body | `fa-heart-pulse`, `fa-dumbbell` |
| Decision / choice | `fa-code-fork`, `fa-signs-post` |
| Learning | `fa-book-open`, `fa-graduation-cap` |
