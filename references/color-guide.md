# Choosing a Color Palette per Topic

Mermaid's `themeVariables` block needs these keys (all hex):
`primaryColor`, `primaryTextColor`, `secondaryColor`, `secondaryTextColor`,
`tertiaryColor`, `tertiaryTextColor`, `mindmapRootColor`, `mindmapMainColor`,
`mindmapSecondaryColor`, `mindmapTertiaryColor`, `mindmapTextColor`,
`mindmapLineColor`.

## Formula

1. Pick **one mood** for the topic (see table below).
2. Pick a **base hue** for that mood, then generate an analogous 3-step
   ramp (base, +30°, +60° around the color wheel) for
   primary/secondary/tertiary — this is what the OCEAN example does
   (red → orange → yellow, an analogous warm ramp).
3. `mindmapRootColor`/`mindmapMainColor` = your most saturated/boldest color
   (draws the eye to the root). `mindmapSecondaryColor`/`mindmapTertiaryColor`
   = the lighter steps of the ramp, for deeper nesting levels.
4. **Pair each fill with a text color by that fill's own lightness, not by a
   fixed rule.** Each `<x>Color`/`<x>TextColor` pair is rendered together —
   contrast is between the two of *them*, independent of the page
   background. A dark, saturated fill (like a typical `primaryColor`) needs
   a pale text tint on top. But by step 3, `tertiaryColor` is often already
   the *lightest* step of your ramp — pairing it with an even paler
   `tertiaryTextColor` (a light-on-light mistake) washes out to near
   unreadable, which is exactly the kind of node most likely to get lost
   against a dark background too. If a fill is light, give it a dark or
   deeply-saturated text color instead of a lighter one. Rule of thumb: eyeball
   each pair side by side — if you can't tell them apart at a glance, darken
   the text color regardless of what step of the ramp it belongs to.
5. `mindmapTextColor` (leaf/plain node text) stays a neutral dark gray
   (`#333333`) — this assumes leaves render on a light background. If you
   know the target viewer (editor/browser) is commonly in dark mode, use a
   light neutral instead (e.g. `#e0e0e0`) so leaf text doesn't disappear.
   When unsure, dark gray is the safer default since most web-based Mermaid
   viewers render on a white/light canvas regardless of OS theme.
6. `mindmapLineColor` = a mid-tone from your ramp so connecting lines are
   visible but not competing with node colors.

## Mood → hue starting points

| Topic mood | Base hue | Example topics |
|---|---|---|
| Energetic / urgent / bold | Red-orange (~0-30°) | personality traits, warnings, competition |
| Calm / reflective / restorative | Blue-teal (~180-200°) | mindfulness, sleep, therapy |
| Growth / natural / healthy | Green (~90-140°) | habits, health, learning progress |
| Neutral / technical / structured | Blue-slate (~210-230°) | software architecture, systems, processes |
| Warm / creative / social | Yellow-orange (~30-45°) | creativity, brainstorming, collaboration |
| Serious / analytical | Purple-indigo (~250-270°) | strategy, risk, decision-making |

Don't just reuse the OCEAN note's exact hex values for unrelated topics —
generate a fresh ramp for the chosen mood. It's fine to reuse the *formula*
(analogous 3-step ramp + tint pairing), just not the literal colors, unless
the new topic is genuinely in the same energetic/bold mood.

## Worked example: a "Sleep Hygiene" topic (calm mood)

Base hue ~195° (teal-blue):

```
'primaryColor': '#2e86ab', 'primaryTextColor': '#bfe3f2',
'secondaryColor': '#5aa9c9', 'secondaryTextColor': '#d3edf5',
'tertiaryColor': '#8fd0e0', 'tertiaryTextColor': '#0d3c4a',
'mindmapRootColor': '#1f6f8b', 'mindmapMainColor': '#1f6f8b',
'mindmapSecondaryColor': '#5aa9c9', 'mindmapTertiaryColor': '#8fd0e0',
'mindmapTextColor': '#333333', 'mindmapLineColor': '#7cc6d9'
```

Note `tertiaryTextColor` is a **dark** teal here, not a paler tint like
primary/secondary get — because `tertiaryColor` itself is already the
lightest, palest step of the ramp. Pairing it with an even paler text color
would be nearly invisible; a dark color on that light fill is what actually
reads clearly.
