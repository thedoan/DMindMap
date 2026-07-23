---
name: mermaid-mindmap-fontawesome
description: Generates a Mermaid mindmap decorated with FontAwesome icons for any topic, where every branch keyword gets an icon whose real-world meaning reinforces that keyword so the map is easier to memorize. Use whenever the user asks for a mindmap, mind map, concept map, or visual summary of a topic for their notes vault (Obsidian/markdown), asks to "turn X into a mindmap", wants icons next to mindmap nodes, or references an existing mindmap note (like an OCEAN/Big Five style file) as a pattern to follow for a new topic. Also trigger when the user wants a memorable/visual way to study or memorize a topic's structure.
---

# Mermaid Mindmap with FontAwesome Icons

Build a Mermaid `mindmap` diagram where nearly every node carries a FontAwesome
icon chosen for its *meaning*, not decoration. A good icon acts as a memory
hook: seeing 🧠 next to "Neuroticism" or 🤝 next to "Agreeableness" should make
the keyword stick better than plain text would. Picking icons well is the
hard part of this skill — spend your effort there, not on mindmap syntax.

## Workflow

1. **Scope the topic.** Identify 3-7 main branches (the topic's core
   categories/dimensions/phases). Too few branches feels thin; too many
   becomes unreadable. If the user gave a vague topic ("make a mindmap about
   productivity"), pick a sensible breakdown yourself rather than asking —
   only ask when the topic is genuinely ambiguous (e.g. could mean two
   unrelated things) or the user needs to choose a destination file/folder.

2. **Decide the file destination.** Default to creating a **new** markdown
   note (mirrors this vault's existing notes like
   `Notes/Psychology/BigFivePersonalityTraits.md`). Ask the user for a path
   if it isn't obvious from context, or infer a sensible one from the vault's
   existing folder structure (e.g. a psychology topic → `Notes/Psychology/`,
   a programming topic → `IT_CS/...`). If the user points at an existing file
   instead, add or replace its `## MindMap` section in place — don't touch
   the rest of the file.

3. **Pick a color palette that fits the topic's mood** (not a fixed
   palette — see `references/color-guide.md` for the formula and worked
   examples). Calm/reflective topics (mindfulness, sleep) suit cool
   blues/teals; energetic or urgent topics (productivity, personality,
   warnings) suit warm reds/oranges; neutral technical topics suit
   blue/slate tones. Reusing the exact colors from an old note for an
   unrelated new topic is a smell — the palette should read as chosen, not
   copy-pasted.

4. **Build the node tree.** Root = the topic itself. Each main branch = one
   category, and any leaf that names a distinct concept (a specific
   technique, pattern, habit, tool — not a generic filler phrase) should get
   its own icon too; the goal is that a reader scanning the map recognizes
   items by their icon, not just their category. Keep any single branch's
   direct children to roughly 5-6 — if a category naturally has more items
   than that (e.g. "Structural Patterns" with 7 members, "Behavioral
   Patterns" with 11), split it into two or three named sub-groups instead
   of one long flat list. This isn't just tidiness: dense flat lists of 7+
   siblings are what causes real rendering problems — nodes and their icons
   crowd together and visually overlap. See
   `references/mindmap-template.md` for the exact Mermaid skeleton,
   including the optional "contrast" sub-pattern (e.g. High/Low, Pros/Cons,
   Before/After) using `fa fa-plus` / `fa fa-minus` when a branch naturally
   splits into two opposing sides.

5. **Choose one icon per branch/keyword that needs one.** For each keyword,
   ask "what concrete object, action, or symbol does this abstract idea bring
   to mind?" — that's the icon. Read `references/icon-guide.md` before
   picking icons; it has the reasoning method plus a cheat sheet of icons
   already matched to common concepts (growth, risk, communication, time,
   structure, etc.) so you're not guessing FontAwesome class names from
   scratch. Only use `fa-solid` (free) icons in the `fa fa-<name>` form shown
   in the template — brand icons and pro-only icons will not render.

6. **Assemble the file.** Use the exact skeleton in
   `references/mindmap-template.md`: FontAwesome CDN `<link>` above the code
   fence, `mindmap` directive, `init` theme variables block with your chosen
   palette, then the node tree. Mermaid has no font-size-per-depth setting,
   so **shape is what creates the root-biggest-to-leaf-smallest hierarchy**:
   root is a bold double-paren circle (`root(("`**Title**`"))`), branches are
   single-paren rounded boxes, leaves are plain text with no shape at all —
   see "Visual size hierarchy" in the template reference for why this is the
   real mechanism (verified by rendering a test file), not just a styling
   preference. **Do not add a `config: layout: tidy-tree`
   frontmatter block** — that layout engine ships as a separate npm package
   (`@mermaid-js/layout-tidy-tree`) that most Mermaid renderers (the Mermaid
   version bundled with markdown previewers, vim/VS Code plugins, GitHub,
   etc.) don't have installed, so it hard-fails outside a handful of
   environments that specifically register it. Plain `mindmap` with no
   layout override uses Mermaid's built-in tree layout and renders
   everywhere. **Every
   node's label must be written on a single source line, no exceptions** —
   splitting a label's words across two raw lines (even to keep long lines
   short, even for the root) is a real syntax hazard, not just a style
   choice: Mermaid's mindmap parser reads hierarchy from line-by-line
   indentation, and a wrapped continuation line gets misread as a sibling or
   child node, breaking the diagram. If a label is long, shorten the wording
   instead of wrapping it.

7. **Check every label for characters that break unquoted Mermaid text.**
   Two known offenders: non-ASCII characters (Vietnamese diacritics, CJK, or
   anything outside plain `A-Za-z0-9` and ordinary punctuation), and
   **hyphens** (`-`) — both have been observed to break parsing in plain
   unquoted node text in real Mermaid renderers. Prefer rewording a
   hyphenated label to avoid the hyphen entirely when the meaning survives
   (e.g. "Client specific interfaces" instead of "Client-specific
   interfaces") — that's simpler and keeps the node at its natural minimal
   shape. When the hyphen is essential (a compound proper noun like
   "Square-Rectangle problem", a real hyphenated term), wrap that node in
   Mermaid's "markdown string" quoted form instead of plain text — see the
   "Text that breaks unquoted Mermaid parsing" section in
   `references/mindmap-template.md` for the exact syntax (the same quoting
   mechanism fixes both problems). Nodes with plain safe text are unaffected
   and keep the normal unquoted style.

8. **Only add a `## Reference` section** if there are actual related notes
   to link (as in the OCEAN example, which links to
   `./ocean/psychology_ocean_openess_to_experience.md`). Don't invent
   placeholder links.

## Quality bar before finishing

- Every main branch, and every leaf that names a distinct concept, has an
  icon; the icon's real-world meaning genuinely relates to the keyword (a
  reader unfamiliar with the topic should be able to guess roughly what the
  node is about from the icon alone).
- No two sibling nodes share the same icon (except the intentional
  `fa-plus`/`fa-minus` contrast pair).
- No branch has more than ~6 direct children — split larger categories into
  named sub-groups instead.
- Every node label is a single source line — nothing wrapped across two raw
  lines.
- The color theme suits the topic's tone, not a copy of a previous note.
- The file renders as valid Mermaid (matching parens, consistent indentation
  — mindmap nesting in Mermaid is whitespace-sensitive, so keep indentation
  consistent within each level).
- Every node with non-ASCII text (Vietnamese, CJK, etc.) uses the quoted
  markdown-string form, not plain unquoted text.
- No unquoted label contains a hyphen — reworded to avoid it, or wrapped in
  the quoted markdown-string form if the hyphen is unavoidable.
- Shape size decreases root → branch → leaf (circle → round → plain/small
  bracket), since that's the only real lever for visual hierarchy in
  Mermaid's mindmap — the root should visibly be the biggest node, branches
  medium, leaves smallest.
