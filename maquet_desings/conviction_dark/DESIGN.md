# Design System Strategy: The Stoic Anchor

## 1. Overview & Creative North Star
In the volatile landscape of digital assets, the design system serves as a "Stoic Anchor." This is not just a dashboard; it is an editorial sanctuary for the mind. We move beyond the frantic "terminal" aesthetic of crypto and toward a high-end, therapeutic experience.

**Creative North Star: The Digital Curator**
The interface should feel like a premium, printed architectural journal. We achieve this through **intentional asymmetry**, where content is offset to create breathing room, and a **high-contrast typography scale** that pits timeless Serifs against brutalist Monospace data. By utilizing generous whitespace (`spacing.20` and `spacing.24`) and rejecting the "boxed-in" grid, we convey a sense of calm authority and long-term conviction.

---

## 2. Colors: Tonal Depth & Soul
Our palette is rooted in the "Deep Navy" abyss, punctuated only by the "Conviction Green" that signals psychological stability.

### The "No-Line" Rule
Standard 1px borders are strictly prohibited for sectioning. Use background shifts to define boundaries. 
- **Guideline:** To separate a sidebar from a main feed, transition from `surface-dim` (#0d1322) to `surface-container-low` (#151b2b). The eye perceives the change in depth without the visual "noise" of a structural line.

### Surface Hierarchy & Nesting
Treat the UI as a series of physical layers.
*   **Base:** `background` (#0d1322)
*   **Sectioning:** `surface-container-low` (#151b2b)
*   **Content Cards:** `surface-container` (#191f2f)
*   **Active Overlays:** `surface-container-high` (#242a3a)

### The "Glass & Gradient" Rule
To escape a flat, "Bootstrap" feel:
*   **Floating Elements:** Use `surface-container-highest` at 80% opacity with a `backdrop-blur` of 20px. This allows the Deep Navy background to bleed through, creating a "Frosted Obsidian" effect.
*   **Signature Textures:** Apply a subtle linear gradient to Primary CTAs—from `primary` (#4edea3) to `primary_container` (#10b981). This adds a "lithographic" quality to the green, making it feel substantial rather than digital.

---

## 3. Typography: The Editorial Voice
We use three distinct voices to create a sophisticated hierarchy:

*   **The Authority (Display/Headline):** *Newsreader (Serif)*. Used for psychological insights and grounding statements. The high x-height and elegant serifs evoke the feeling of a broadsheet newspaper, grounding the user in reality.
*   **The Guide (Title/Body):** *Manrope (Sans-serif)*. A clean, geometric bridge between the Serif and Mono. It is used for all functional instructions and secondary narratives.
*   **The Evidence (Labels/Data):** *Space Grotesk (Monospace)*. Used for scores, percentages, and timestamps. It feels technical and precise, stripping away the emotion from the numbers.

---

## 4. Elevation & Depth: Tonal Layering
We reject the heavy drop shadows of the early 2010s. Depth is achieved through light, not shadow.

*   **The Layering Principle:** Place a `surface-container-lowest` card on a `surface-container-low` background to create a "recessed" look. Place a `surface-container-high` card on a `surface` background to create a "lifted" look.
*   **Ambient Shadows:** For floating modals, use a shadow with a 40px blur, 0% spread, and an opacity of 6% using the `on-surface` color. It should feel like a soft glow of light blocked by a heavy object.
*   **The "Ghost Border" Fallback:** If a divider is mandatory for accessibility, use the `outline-variant` token at **15% opacity**. It should be felt, not seen.

---

## 5. Components: Precision & Minimalist Utility

### Buttons (The Conviction Action)
*   **Primary:** `primary_container` background with `on_primary_container` text. Radius: `md` (0.375rem). Use a subtle 1px "Ghost Border" of `primary` to make the green pop.
*   **Secondary:** No background. `outline` border at 30% opacity. Text in `primary`.
*   **Tertiary:** Text-only in `on_surface_variant`, transitioning to `on_surface` on hover. No box.

### Cards & Content Lists
*   **Rule:** Forbid the use of divider lines.
*   **Execution:** Use `spacing.6` (2rem) of vertical whitespace between list items. For cards, use `surface-container` with a `lg` (0.5rem) corner radius. Content should "float" within the container with `spacing.5` internal padding.

### Psychological Vitality Gauges (Progress Bars)
*   Track: `surface-container-highest`.
*   Indicator: `primary` (#4edea3). 
*   **Signature Touch:** Add a 4px "bloom" (outer glow) to the indicator head using the `primary` color at 40% opacity to symbolize "Mental Energy."

### Inputs (The Focused Field)
*   Bottom-border only. Use `outline-variant` at 40%. On focus, animate the border to `primary` and scale the label using `label-sm`. Avoid the "enclosed box" input to keep the layout feeling open and breathable.

---

## 6. Do’s and Don’ts

### Do:
*   **Embrace Asymmetry:** Align headlines to the left while keeping data points right-aligned with significant horizontal gaps.
*   **Use Mono for Numbers:** Always use `Space Grotesk` for crypto values. It provides a "just the facts" tone.
*   **Exaggerate Whitespace:** If a section feels crowded, double the padding. This tool is for people in high-stress states; the UI must not add to that stress.

### Don’t:
*   **Don't Use Pure Black:** Always use the Deep Navy `#0B1120` (`surface`) to maintain the premium, "inky" depth.
*   **Don't Use Solid Dividers:** Never use a solid 1px line to separate content. Use a background color shift or 32px of empty space.
*   **Don't Use "Vibrant" Red:** Our `error` red is slightly desaturated (#ffb4ab). We want to alert the user to danger without triggering a fight-or-flight response.