# DESIGN.md Format — Complete Specification Reference

This is the authoritative reference for agents generating, parsing, or validating
DESIGN.md files. Source: https://github.com/google-labs-code/design.md

---

## File Structure

A DESIGN.md file has exactly two layers:

```
---
<YAML front matter — machine-readable design tokens>
---

## Overview
<Markdown prose sections — human-readable rationale>
```

The YAML front matter is delimited by `---` fences at the top of the file.
The Markdown body follows immediately after the closing `---` fence.

---

## YAML Front Matter: Token Schema

```yaml
version: "alpha"          # optional; current version string
name: <string>            # required — name of the design system
description: <string>     # optional — one-sentence summary

colors:
  <token-name>: <Color>   # e.g. primary, secondary, tertiary, neutral, on-primary

typography:
  <token-name>:           # e.g. h1, h2, body-md, body-sm, label-caps, caption
    fontFamily: <string>
    fontSize: <Dimension>
    fontWeight: <number | string>   # optional
    lineHeight: <Dimension | number> # optional
    letterSpacing: <Dimension>       # optional
    fontFeature: <string>            # optional — CSS font-feature-settings value
    fontVariation: <string>          # optional — CSS font-variation-settings value

rounded:
  <scale-level>: <Dimension>  # e.g. sm: 4px, md: 8px, lg: 16px, full: 9999px

spacing:
  <scale-level>: <Dimension | number>  # e.g. xs: 4px, sm: 8px, md: 16px, lg: 32px

components:
  <component-name>:
    <token-name>: <string | token reference>
```

### Token Types

| Type | Format | Example |
|------|--------|---------|
| Color | `#` + 6 hex digits (sRGB) | `"#1A1C1E"` |
| Dimension | number + unit (`px`, `em`, `rem`) | `48px`, `-0.02em`, `1.5rem` |
| Token Reference | `{path.to.token}` | `{colors.primary}`, `{rounded.sm}` |
| Typography | object with named sub-fields (see above) | see Typography section |

**Color rules:**
- Values MUST be lowercase-or-uppercase 6-digit hex with `#` prefix
- Strings must be quoted: `primary: "#1A1C1E"` not `primary: #1A1C1E`
- Token names are lowercase, hyphenated: `on-primary`, `surface-variant`

**Dimension rules:**
- Always include unit suffix: `16px` not `16`
- Spacing values can also be plain numbers (treated as pixels)

**Token Reference rules:**
- Surrounded in `{}` curly braces: `{colors.primary}`
- Path uses dot notation: `{colors.primary}`, `{rounded.md}`, `{spacing.sm}`
- References must resolve to a defined token — broken refs are a lint error

---

## Component Token Schema

```yaml
components:
  button-primary:
    backgroundColor: "{colors.tertiary}"
    textColor: "{colors.on-tertiary}"
    rounded: "{rounded.sm}"
    padding: 12px
  button-primary-hover:
    backgroundColor: "{colors.tertiary-container}"
  button-primary-disabled:
    backgroundColor: "{colors.surface-variant}"
    textColor: "{colors.on-surface-variant}"
  card:
    backgroundColor: "{colors.surface}"
    rounded: "{rounded.md}"
    padding: "{spacing.md}"
```

**Valid component properties:**
`backgroundColor`, `textColor`, `typography`, `rounded`, `padding`,
`size`, `height`, `width`

**Variant convention:** Variants (hover, active, pressed, disabled, focus) are expressed
as **separate component entries** with a related name suffix:
- `button-primary` → base state
- `button-primary-hover` → hover state
- `button-primary-disabled` → disabled state

---

## Prose Sections

Sections use `##` headings. They **can be omitted**, but those present **must appear
in this order**:

| # | Section Heading | Accepted Aliases |
|---|----------------|-----------------|
| 1 | `## Overview` | `## Brand & Style` |
| 2 | `## Colors` | — |
| 3 | `## Typography` | — |
| 4 | `## Layout` | `## Layout & Spacing` |
| 5 | `## Elevation & Depth` | `## Elevation` |
| 6 | `## Shapes` | — |
| 7 | `## Components` | — |
| 8 | `## Do's and Don'ts` | — |

**Section order is enforced by the linter** (`section-order` rule, severity: warning).

### Section Content Guidelines

**`## Overview`** — The aesthetic concept and brand personality in natural language.
Opens with the core design philosophy metaphor. Covers mood, tone, references, and
the "why" behind the visual direction. 2–4 short paragraphs. No bullet points.

**`## Colors`** — Describes each named color: its role, emotion, and usage guidance.
Reference token names in bold. Explain the relationship between primary/surface/on-* colors.

**`## Typography`** — Explains the type system: which font families, the scale rationale,
when to use each type style. Cover personality of each typeface choice.

**`## Layout`** — Describes the spatial philosophy: grid system, spacing scale,
content density, whitespace approach, alignment principles.

**`## Elevation & Depth`** — Describes the shadow and layering strategy: how depth
is created, when to use elevation, shadow color approach. Note: elevation is **prose only**
(no dedicated frontmatter tokens — shadows are typically expressed as component tokens).

**`## Shapes`** — Describes the border radius philosophy: what the rounded scale
communicates, when to use sharp vs soft corners, shape-as-character.

**`## Components`** — Describes the component vocabulary at a high level:
interaction patterns, common states, how components express the brand.

**`## Do's and Don'ts`** — Concrete usage rules. Format as:
`**Do:** <affirmative rule>` and `**Don't:** <negative rule>`. Pairs are common.

---

## Consumer Behavior for Unknown Content

| Scenario | Behavior |
|----------|----------|
| Unknown `##` section heading | Preserve; do not error |
| Unknown color token name | Accept if value is valid Color |
| Unknown typography token name | Accept as valid typography token |
| Unknown component property | Accept with warning |
| Duplicate `##` section heading | **Error** — reject the file |
| Missing `name:` in front matter | Implementation-defined |

---

## Linting Rules (all 8)

The `npx @google/design.md lint` command runs these rules:

| Rule | Severity | What It Checks |
|------|----------|----------------|
| `broken-ref` | **error** | Token references `{path.to.token}` that don't resolve to a defined token |
| `missing-primary` | warning | Colors are defined but no `primary` color exists — agents will auto-generate one |
| `contrast-ratio` | warning | Component `backgroundColor`/`textColor` pairs with contrast ratio below WCAG AA (4.5:1) |
| `orphaned-tokens` | warning | Color tokens defined but never referenced by any component |
| `token-summary` | info | Summary of how many tokens are defined in each section |
| `missing-sections` | info | Optional sections (spacing, rounded) absent when other tokens exist |
| `missing-typography` | warning | Colors are defined but no typography tokens exist |
| `section-order` | warning | Sections appear out of the canonical order |

**Lint output format (JSON):**
```json
{
  "findings": [
    {
      "severity": "error | warning | info",
      "rule": "broken-ref",
      "path": "components.button-primary.backgroundColor",
      "message": "Reference {colors.accent} not found in defined tokens"
    }
  ],
  "summary": { "errors": 0, "warnings": 2, "info": 1 }
}
```

---

## CLI Reference

```bash
# Install
npm install @google/design.md
# or run directly:
npx @google/design.md <command> [options]

# Validate a DESIGN.md file
npx @google/design.md lint DESIGN.md
npx @google/design.md lint --format json DESIGN.md
cat DESIGN.md | npx @google/design.md lint -

# Compare two versions (detect regressions)
npx @google/design.md diff DESIGN.md DESIGN-v2.md

# Export to other formats
npx @google/design.md export --format json-tailwind DESIGN.md > tailwind.theme.json
npx @google/design.md export --format css-tailwind DESIGN.md > theme.css
npx @google/design.md export --format dtcg DESIGN.md > tokens.json

# Print the spec (useful for injecting context)
npx @google/design.md spec
npx @google/design.md spec --rules
```

**Export formats:**
| Format | Output | Use For |
|--------|--------|---------|
| `json-tailwind` | JSON | Tailwind v3 `theme.extend` config object |
| `css-tailwind` | CSS | Tailwind v4 `@theme { ... }` CSS custom properties |
| `dtcg` | JSON | W3C Design Tokens Format Module (tokens.json) |

**Exit codes:** `lint` and `diff` exit with `1` if errors found, `0` otherwise.
`diff` exits with `1` if regressions detected (more errors/warnings in the "after" file).

---

## Design Intelligence → DESIGN.md Mapping

This table is the core of the translation layer. When generating a DESIGN.md from a
design-intelligence session, each DI agent's output maps to specific DESIGN.md sections.

### Front Matter Tokens

| DI Source | → DESIGN.md Token |
|-----------|-------------------|
| `design-system-architect` — color palette | `colors:` (primary, secondary, tertiary, neutral, surface, on-primary, etc.) |
| `design-system-architect` — typography scale | `typography:` (h1, h2, h3, body-md, body-sm, label, caption, etc.) |
| `design-system-architect` — spacing system | `spacing:` (xs, sm, md, lg, xl, 2xl) |
| `design-system-architect` — border radius | `rounded:` (none, sm, md, lg, full) |
| `component-designer` — component visual specs | `components:` (button-primary, card, input, badge, etc.) |

### Prose Sections

| DI Source | → DESIGN.md Section |
|-----------|---------------------|
| `art-director` — aesthetic direction, visual metaphors, brand personality | `## Overview` |
| `design-system-architect` — color semantics and rationale | `## Colors` |
| `design-system-architect` — type system rationale, font choices | `## Typography` |
| `layout-architect` — spatial philosophy, grid rationale, density | `## Layout` |
| `design-system-architect` — shadow and depth strategy | `## Elevation & Depth` |
| `design-system-architect` — border radius philosophy | `## Shapes` |
| `component-designer` — component vocabulary and interaction patterns | `## Components` |
| `voice-strategist` — usage guidelines and anti-patterns | `## Do's and Don'ts` |

### DI Concepts NOT Expressible in DESIGN.md (Gaps)

| DI Concept | Gap | Recommendation |
|-----------|-----|----------------|
| `animation-choreographer` — motion timing, easing, sequences | No DESIGN.md equivalent | Document in `MOTION.md` alongside DESIGN.md |
| `responsive-strategist` — breakpoints, fluid grids, device adaptations | No DESIGN.md equivalent | Document in `RESPONSIVE.md` or within `## Layout` prose (high-level only) |
| Accessibility audit depth | Only WCAG contrast ratio is checked | Reference full a11y specs in `## Do's and Don'ts` |

---

## Complete Example

```markdown
---
version: "alpha"
name: Heritage
description: Premium editorial design system with architectural restraint
colors:
  primary: "#1A1C1E"
  secondary: "#6C7278"
  tertiary: "#B8422E"
  neutral: "#F7F5F2"
  surface: "#FFFFFF"
  on-primary: "#F7F5F2"
  on-tertiary: "#FFFFFF"
typography:
  h1:
    fontFamily: Public Sans
    fontSize: 3rem
    fontWeight: 700
    lineHeight: 1.1
    letterSpacing: -0.03em
  h2:
    fontFamily: Public Sans
    fontSize: 2rem
    fontWeight: 600
  body-md:
    fontFamily: Public Sans
    fontSize: 1rem
    lineHeight: 1.6
  label-caps:
    fontFamily: Space Grotesk
    fontSize: 0.75rem
    fontWeight: 600
    letterSpacing: 0.08em
rounded:
  none: 0px
  sm: 4px
  md: 8px
  lg: 16px
spacing:
  xs: 4px
  sm: 8px
  md: 16px
  lg: 32px
  xl: 64px
components:
  button-primary:
    backgroundColor: "{colors.tertiary}"
    textColor: "{colors.on-tertiary}"
    rounded: "{rounded.sm}"
    padding: 12px
    typography: "{typography.label-caps}"
  button-primary-hover:
    backgroundColor: "#9E2D20"
  card:
    backgroundColor: "{colors.surface}"
    rounded: "{rounded.md}"
    padding: "{spacing.md}"
---

## Overview

Architectural Minimalism meets Journalistic Gravitas. The UI evokes a premium
matte finish — a high-end broadsheet or contemporary gallery catalogue. Every
interaction is considered, restrained, and purposeful.

## Colors

The palette is rooted in high-contrast neutrals and a single accent color.

- **Primary (#1A1C1E):** Deep ink for headlines and core UI chrome.
- **Secondary (#6C7278):** Sophisticated slate for borders and metadata.
- **Tertiary (#B8422E):** Boston Clay — the sole driver for interaction.
- **Neutral (#F7F5F2):** Warm limestone foundation, softer than pure white.

## Typography

Public Sans carries the primary voice — authoritative without being cold.
Space Grotesk appears only in all-caps labels, adding industrial contrast.

## Layout

An 8px base grid enforces spatial rhythm. Generous whitespace is the primary
luxury signal. Content density is low by design — nothing competes for attention.

## Elevation & Depth

Depth is expressed through tonal shifts, not shadows. Surface-on-surface
layering uses a 4% opacity white overlay per elevation level.

## Shapes

Corners are almost sharp. The `sm: 4px` radius softens edges just enough to
feel crafted, not clinical. Avoid large radii — they conflict with the editorial posture.

## Components

Interactive elements use the tertiary color exclusively. State changes are
communicative but subtle — hover darkens, active depresses, disabled grays.

## Do's and Don'ts

**Do:** Use whitespace generously — empty space is a design element.
**Don't:** Use tertiary color for decorative elements. It signals action only.
**Do:** Maintain typographic hierarchy strictly — one h1 per view.
**Don't:** Mix border radius scales — pick one and apply it consistently.
```
