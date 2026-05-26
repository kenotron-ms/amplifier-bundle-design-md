# amplifier-bundle-design-md

Turn a vague idea into a production design system file that every AI coding tool understands.

This Amplifier bundle connects two projects that approach design from opposite ends: [Google's DESIGN.md](https://github.com/google-labs-code/design.md) portable format and the [design-intelligence](https://github.com/microsoft/amplifier-bundle-design-intelligence) agent bundle. DESIGN.md defines **what** a design system file looks like. Design intelligence provides the **thinking** that fills it with real decisions. This bundle is the bridge.

---

## Table of Contents

- [The Problem](#the-problem)
- [How It Works](#how-it-works)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Quick Start: Idea to DESIGN.md in One Command](#quick-start-idea-to-designmd-in-one-command)
- [The Full Interactive Workflow](#the-full-interactive-workflow)
  - [Phase 1: Starting From Nothing](#phase-1-starting-from-nothing)
  - [Phase 2: The Design Conversation](#phase-2-the-design-conversation)
  - [Phase 3: Generating the DESIGN.md](#phase-3-generating-the-designmd)
  - [Phase 4: Validating It](#phase-4-validating-it)
  - [Phase 5: Using It Everywhere](#phase-5-using-it-everywhere)
- [The Import and Enhance Workflow](#the-import-and-enhance-workflow)
- [The Recipe Workflow](#the-recipe-workflow)
- [Export Formats](#export-formats)
- [Architecture](#architecture)
- [Bundle Contents](#bundle-contents)
- [Relationship to Upstream Projects](#relationship-to-upstream-projects)
- [License](#license)

---

## The Problem

You have an idea for an app. Maybe you know you want it to feel "clean and editorial" or "bold and playful." Maybe you have a competitor's product you admire. Maybe you have nothing at all except a product concept.

Between that starting point and a codebase where every component uses consistent colors, spacing, and typography, there are dozens of design decisions that need to happen. Most developers skip them entirely or make them ad hoc, ending up with an inconsistent UI that looks like it was designed by a committee of random Stack Overflow answers.

The DESIGN.md format solves the **last mile** -- it gives you a portable file that Claude Code, Cursor, Copilot, Stitch, and any spec-aware tool can read and apply. But it does not help you make the design decisions that fill that file.

The design-intelligence bundle solves the **thinking** -- it has 7 specialized agents (art director, design system architect, component designer, layout architect, animation choreographer, responsive strategist, voice strategist) that can reason deeply about aesthetics, tokens, and component patterns. But it has no standard output format.

This bundle connects them. Design intelligence does the thinking. DESIGN.md captures the result. You get a portable design system file backed by real design rationale, not placeholder tokens.

---

## How It Works

```
Your idea
  |
  v
art-director           --> aesthetic direction, visual metaphors, brand personality
  |
  v
design-system-architect --> color tokens, typography scale, spacing, border radius
  |
  v
component-designer      --> button, card, input specs using token references
  |
  v
design-md-generator     --> serializes everything into a spec-compliant DESIGN.md
  |
  v
design-md-reviewer      --> validates with lint rules, catches broken refs, contrast issues
  |
  v
DESIGN.md               --> drop into any repo, understood by Claude Code / Cursor / Copilot / Stitch
```

The reverse also works: bring an existing DESIGN.md, import it, enhance it with motion/responsive/accessibility depth that the format cannot express, then re-export.

---

## Prerequisites

**Required:**
- [Amplifier](https://github.com/microsoft/amplifier) installed and configured

**Recommended:**
- Node.js 18+ and npm/npx (for the `@google/design.md` lint CLI)

The lint CLI is optional. Without it, the reviewer agent falls back to structural pre-validation that covers all 8 lint rules. With it, you get authoritative results from Google's official linter.

To install the CLI separately:

```bash
npm install -g @google/design.md
```

Or use it on demand with `npx` (no global install needed):

```bash
npx @google/design.md lint DESIGN.md
```

---

## Installation

Add this bundle to your Amplifier configuration:

```yaml
includes:
  - bundle: git+https://github.com/kenotron-ms/amplifier-bundle-design-md@main
```

This automatically pulls in the design-intelligence bundle as an upstream dependency. You do not need to install design-intelligence separately -- this bundle includes it.

---

## Quick Start: Idea to DESIGN.md in One Command

Write a brief. It can be short. Save it as `brief.md`:

```markdown
# Tidal

A marine weather app for coastal sailors. Shows tide predictions, wind forecasts,
and harbor conditions. Target audience is experienced sailors who want data density
without clutter. Think nautical chart aesthetics -- functional, precise, with a
sense of the ocean. No bright colors. No playful illustrations. This is a tool,
not a toy.

Key components: data cards, tide chart, wind compass, harbor list, alert badge.
```

Run the export recipe:

```
> Run the design-md-export recipe with brief_path="brief.md", project_name="Tidal", output_path="DESIGN.md"
```

The recipe executes 6 steps automatically:

1. **read-brief** -- Parses your brief into structured context (product, audience, mood, constraints)
2. **establish-aesthetic** -- Art director produces a full aesthetic direction document
3. **architect-tokens** -- Design system architect creates production-ready color, typography, spacing, and radius tokens
4. **design-components** -- Component designer specifies button, card, input, and product-specific components using token references
5. **generate-design-md** -- Generator serializes everything into a spec-compliant DESIGN.md with YAML frontmatter and all 8 prose sections
6. **validate-design-md** -- Reviewer runs all lint rules and reports errors, warnings, and quality assessment

At the end, you have a validated `DESIGN.md` in your project root.

---

## The Full Interactive Workflow

The recipe is fast, but working interactively with the agents gives you more control. Here is the full journey from a vague notion to a production file.

### Phase 1: Starting From Nothing

You start a session with the bundle active. You do not need a formal brief -- just describe what you are building and the feeling you want.

```
> I'm building a personal finance dashboard. I want it to feel calm and trustworthy,
  like a well-designed banking app. Think N26 or Monzo but less playful. Dark mode
  first. The main views are account overview, transaction list, budget tracking,
  and monthly reports.
```

The session has all 10 agents available (7 from design-intelligence, 3 from this bundle). The root context includes a thin awareness file that knows when to route to which agent. At this point, the art director picks up the conversation.

### Phase 2: The Design Conversation

The art director translates your vibes into systematic principles. This is a real conversation -- you push back, refine, redirect.

```
> I like the direction but "Swiss banking" feels too cold. Think more like a really
  well-designed productivity app -- Linear or Notion -- but for money. The accent
  color should feel confident, not alarming. Green is too "stock market." Maybe
  a deep blue or a muted violet?
```

The art director refines the aesthetic direction. When the direction feels right, the design system architect takes over and starts defining concrete tokens:

```
> OK, I love that direction. Let's build the token system. I want an 8px grid,
  and I'd prefer Inter or a similar geometric sans for the UI. Headings can have
  more personality.
```

The design system architect produces:
- A color palette with semantic names (primary, secondary, tertiary, neutral, surface, on-primary, etc.)
- A typography scale with font families, sizes, weights, and line heights
- A spacing scale on your preferred grid
- A border radius scale that matches the aesthetic posture

You can ask the component designer to spec out your key components:

```
> Design the components for this system: transaction row, account card, budget
  progress bar, category badge. Use the tokens we just defined.
```

The component designer produces visual specs with token references (`{colors.primary}`, `{rounded.md}`, `{spacing.sm}`) rather than hardcoded values, so everything stays connected to the system.

At any point, you can also work with:
- **layout-architect** -- for grid philosophy, content density, spatial rhythm
- **animation-choreographer** -- for motion timing and transitions (note: these are NOT captured in DESIGN.md but documented separately)
- **responsive-strategist** -- for breakpoint strategy and device adaptations (also documented separately)
- **voice-strategist** -- for do's and don'ts, usage guidelines, tone rules

### Phase 3: Generating the DESIGN.md

When you have enough design decisions, ask for the export:

```
> Generate a DESIGN.md from everything we've designed.
```

The `design-md-generator` agent activates. It knows the exact mapping:

| Design Intelligence Source | DESIGN.md Destination |
|---|---|
| art-director aesthetic direction | `## Overview` prose |
| design-system-architect color palette | `colors:` frontmatter + `## Colors` prose |
| design-system-architect typography | `typography:` frontmatter + `## Typography` prose |
| design-system-architect spacing | `spacing:` frontmatter |
| design-system-architect border radius | `rounded:` frontmatter + `## Shapes` prose |
| layout-architect spatial philosophy | `## Layout` prose |
| design-system-architect depth strategy | `## Elevation & Depth` prose |
| component-designer specs | `components:` frontmatter + `## Components` prose |
| voice-strategist guidelines | `## Do's and Don'ts` |

The generator validates all token references before writing (no `{colors.accent}` pointing at nothing), checks section order, and writes the file.

What does NOT go into the DESIGN.md (because the format does not support it):
- Motion and animation decisions -- document in a `MOTION.md`
- Responsive breakpoints and fluid scaling -- document in a `RESPONSIVE.md`
- Deep accessibility patterns beyond contrast ratios

### Phase 4: Validating It

After generation, validate:

```
> Validate the DESIGN.md we just wrote.
```

The `design-md-reviewer` agent runs validation in two layers:

**Structural pre-validation** (always available, no CLI needed):
- YAML front matter integrity
- Token reference resolution (all `{path.to.token}` refs must resolve)
- Section order (canonical: Overview, Colors, Typography, Layout, Elevation & Depth, Shapes, Components, Do's and Don'ts)
- Missing primary color check
- Missing typography check
- Orphaned token detection
- Contrast ratio estimation for component token pairs
- Token summary

**CLI validation** (when Node.js is available):
```bash
npx @google/design.md lint --format json DESIGN.md
```

The reviewer produces a structured report:

```
DESIGN.md Review: DESIGN.md

Summary: 0 errors, 1 warning, 1 info

Warnings:
  contrast-ratio on components.budget-bar:
    textColor (#A0A0A0) on backgroundColor (#1A1C1E) has ratio 3.8:1 -- fails WCAG AA.
    Fix: lighten textColor to at least #949494 for 4.5:1.
    Route to: design-system-architect

Info:
  token-summary: 9 colors, 7 typography styles, 5 spacing steps, 4 rounded, 8 components

Status: PASS (0 errors, structurally valid)
```

If errors are found, the reviewer tells you which agent should fix each issue. You iterate until the file is clean.

### Phase 5: Using It Everywhere

Your `DESIGN.md` is now a portable design system. Here is how to use it.

**In any AI coding tool:**

Drop the `DESIGN.md` file into your project root. Claude Code, Cursor, Copilot, and Stitch will read it automatically when generating UI code. The YAML frontmatter gives them exact token values. The prose sections give them the aesthetic intent so they make contextually appropriate choices.

```
your-project/
  DESIGN.md          <-- agents read this automatically
  src/
  package.json
  ...
```

**Export to Tailwind v3:**

```
> Export our DESIGN.md to Tailwind v3 format.
```

Or directly:
```bash
npx @google/design.md export --format json-tailwind DESIGN.md > tailwind.theme.json
```

Produces a `theme.extend` JSON object you plug into `tailwind.config.js`.

**Export to Tailwind v4:**

```bash
npx @google/design.md export --format css-tailwind DESIGN.md > theme.css
```

Produces a `@theme { ... }` CSS block with CSS custom properties for Tailwind v4.

**Export to DTCG (W3C Design Tokens):**

```bash
npx @google/design.md export --format dtcg DESIGN.md > tokens.json
```

Produces a `tokens.json` in W3C Design Tokens Community Group format, compatible with Style Dictionary, Tokens Studio, and other token tooling.

**Use in a monorepo or design system library:**

Put the DESIGN.md at the root and reference it from any package. Tools that understand the format will pick it up. Teams that do not use spec-aware tools can still read it as documentation -- the prose sections are human-readable design guidance.

---

## The Import and Enhance Workflow

Sometimes you start with a DESIGN.md someone else created -- from a Stitch export, a Figma plugin, another team, or a template. This bundle lets you import it, enhance it with design-intelligence depth, and re-export.

**Step 1: Import**

```
> I have a DESIGN.md from our Stitch session. Can we enhance it?
```

The `design-md-importer` agent reads the file and produces a structured analysis:

- **Token inventory** -- every color, typography style, spacing step, component, and their values
- **Quality assessment** -- broken references, low contrast pairs, inconsistent spacing grids, orphaned tokens
- **Aesthetic intent extraction** -- brand personality, visual metaphor, and design philosophy from the prose
- **DI agent mapping** -- which design-intelligence agent domain each part of the file maps to
- **Gap analysis** -- what the DESIGN.md does NOT express that design-intelligence can add

**Step 2: Enhance with design-intelligence agents**

The importer tells you exactly what is missing and which agent to work with:

```
Gap Analysis:
  - No motion/animation guidance --> animation-choreographer
  - Typography scale has only 3 styles (h1, body-md, caption) --> design-system-architect
  - No responsive strategy --> responsive-strategist
  - Do's and Don'ts section is empty --> voice-strategist
  - Components section has only button-primary, no variants --> component-designer
```

You work with the relevant agents to fill the gaps:

```
> Let's flesh out the typography scale. We need heading levels, body sizes,
  labels, and a monospace style for code.
```

```
> Design hover, disabled, and focus variants for the button. Also add card,
  input, and badge components.
```

```
> What motion guidelines should we establish for this aesthetic?
```

**Step 3: Re-export**

When enhancements are complete:

```
> Re-generate the DESIGN.md with our improvements.
```

The generator writes an updated file incorporating the new tokens and prose while preserving the original design intent. The reviewer validates it. Motion and responsive decisions go into companion files.

---

## The Recipe Workflow

The `design-md-export` recipe automates the full pipeline for when you want to go from brief to validated DESIGN.md without interactive back-and-forth.

**Execute the recipe:**

```
> Run the design-md-export recipe with brief_path="brief.md",
  project_name="Tidal", output_path="DESIGN.md"
```

Or programmatically:

```python
recipes(
    operation="execute",
    recipe_path="design-md:recipes/design-md-export",
    context={
        "brief_path": "docs/project-brief.md",
        "output_path": "DESIGN.md",
        "project_name": "Tidal"
    }
)
```

**Recipe parameters:**

| Parameter | Required | Default | Description |
|-----------|----------|---------|-------------|
| `brief_path` | Yes | -- | Path to a project brief (markdown or text file) |
| `project_name` | Yes | -- | Name for the design system (`name:` field in DESIGN.md) |
| `output_path` | No | `DESIGN.md` | Where to write the output file |

**Recipe steps and agent routing:**

| Step | Agent | What It Does |
|------|-------|-------------|
| read-brief | foundation:explorer | Parses brief into structured JSON (product, audience, mood, constraints, key components) |
| establish-aesthetic | design-intelligence:art-director | Produces 400-600 word aesthetic direction document |
| architect-tokens | design-intelligence:design-system-architect | Creates complete token system as JSON (colors, typography, spacing, rounded, rationale) |
| design-components | design-intelligence:component-designer | Designs 5-8 key components with token references |
| generate-design-md | design-md:design-md-generator | Assembles YAML frontmatter + 8 prose sections, writes to disk |
| validate-design-md | design-md:design-md-reviewer | Runs all lint rules, produces structured validation report |

The brief does not need to be formal. A few sentences about your product, audience, and aesthetic preferences are enough. The art director extrapolates from whatever you provide.

---

## Export Formats

The reviewer agent can export your DESIGN.md to multiple formats via the `@google/design.md` CLI:

| Command | Format | Output | Use For |
|---------|--------|--------|---------|
| `export --format json-tailwind` | JSON | `theme.extend` object | Tailwind v3 `tailwind.config.js` |
| `export --format css-tailwind` | CSS | `@theme { ... }` block | Tailwind v4 CSS custom properties |
| `export --format dtcg` | JSON | W3C tokens.json | Style Dictionary, Tokens Studio, spec-compliant tooling |

You can also diff two versions of a DESIGN.md to detect regressions:

```bash
npx @google/design.md diff DESIGN-v1.md DESIGN-v2.md
```

This reports added, removed, and modified tokens and exits with code 1 if the new version has more errors or warnings than the old one.

---

## Architecture

### Layering, not forking

This bundle does not duplicate design-intelligence. It includes it as an upstream dependency and layers 3 new agents on top.

```yaml
# bundle.md
includes:
  - bundle: git+https://github.com/microsoft/amplifier-bundle-design-intelligence@main
  - bundle: design-md:behaviors/design-md
```

When you load this bundle, you get all 7 design-intelligence agents plus the 3 new DESIGN.md agents. The philosophy stack (Nine Dimensions of Aesthetic Expression, Five Pillars, design instructions, design framework) loads automatically through design-intelligence's own context.

### Context sink pattern

The full DESIGN.md specification is 390 lines. It is NOT loaded into root sessions. Instead:

- **Root sessions** get a 34-line awareness file (`context/design-md-awareness.md`) that says "these capabilities exist, delegate to these agents when triggered"
- **Agent sessions** load the full spec via `@mention` reference (`@design-md:context/design-md-spec.md`) only when an agent spawns

This keeps root sessions lightweight while giving agents complete spec knowledge when they need it.

### Agent responsibilities

| Agent | Direction | Responsibility |
|-------|-----------|---------------|
| `design-md-generator` | DI --> DESIGN.md | Collects design-intelligence outputs, maps them to DESIGN.md tokens and prose sections, validates references, writes the file |
| `design-md-importer` | DESIGN.md --> DI | Parses YAML frontmatter and prose, extracts token inventory and aesthetic intent, maps to DI agent domains, identifies gaps |
| `design-md-reviewer` | Validate / Export | Runs 8 lint rules (structural + CLI), diffs versions, exports to Tailwind/DTCG, routes findings to the right DI agent for fixes |

### What the agents know

Each of the 3 agents carries:

- The complete DESIGN.md format specification (token schema, section order, lint rules, CLI reference)
- The mapping table between design-intelligence agent outputs and DESIGN.md sections
- Knowledge of what DESIGN.md cannot express (motion, responsive, deep accessibility) and where to document those instead
- Their specific protocol (generation, import, or review) with step-by-step procedures

---

## Bundle Contents

```
amplifier-bundle-design-md/
|
|-- bundle.md                          Root bundle definition. Includes
|                                      design-intelligence upstream and
|                                      registers the behavior partial.
|
|-- behaviors/
|   +-- design-md.yaml                 Registers 3 agents + awareness context.
|
|-- agents/
|   |-- design-md-generator.md         276 lines. 6-step generation protocol:
|   |                                  collect inputs, assemble YAML, assemble
|   |                                  components, write prose, validate, write file.
|   |
|   |-- design-md-importer.md          236 lines. 6-step import protocol:
|   |                                  read file, extract tokens, extract aesthetic,
|   |                                  map to DI domains, gap analysis, deliver report.
|   |
|   +-- design-md-reviewer.md          304 lines. Lint workflow (structural +
|                                      CLI), diff workflow, export workflow,
|                                      finding-to-agent routing table.
|
|-- context/
|   |-- design-md-awareness.md         34 lines. Thin pointer loaded in root
|   |                                  sessions. Routing table for when to
|   |                                  delegate to which agent.
|   |
|   +-- design-md-spec.md              390 lines. Complete DESIGN.md format
|                                      specification. Token schema, section
|                                      definitions, lint rules, CLI reference,
|                                      DI mapping table, full example file.
|
+-- recipes/
    +-- design-md-export.yaml          400 lines. 6-step pipeline from brief
                                       to validated DESIGN.md. Each step
                                       delegates to the appropriate agent with
                                       structured prompts and output chaining.
```

---

## Relationship to Upstream Projects

### DESIGN.md (Google Labs)

[github.com/google-labs-code/design.md](https://github.com/google-labs-code/design.md) -- Apache 2.0

DESIGN.md is a **file format specification**. It defines:
- A YAML frontmatter schema for design tokens (colors, typography, spacing, border radius, components)
- 8 canonical prose sections for human-readable design rationale
- A CLI with lint, diff, and export commands
- Export targets: Tailwind v3/v4, DTCG/W3C tokens

This bundle consumes the spec as a reference document and wraps the CLI for validation and export. It does not fork or modify the spec.

### design-intelligence (Amplifier)

[github.com/microsoft/amplifier-bundle-design-intelligence](https://github.com/microsoft/amplifier-bundle-design-intelligence)

Design intelligence is a **multi-agent design system**. It provides:
- 7 specialized design agents with deep domain knowledge
- Nine Dimensions of Aesthetic Expression (Style, Motion, Voice, Space, Color, Typography, Proportion, Texture, Body)
- Five Pillars design philosophy (Purpose, Craft, Constraints, Intentional Incompleteness, Design for Humans)
- Knowledge base: color theory, typography, animation principles, accessibility
- Protocols for component creation, quality checklists, anti-pattern detection

This bundle includes design-intelligence as an upstream dependency. All 7 agents, the philosophy stack, and the knowledge base are available in every session. Nothing is duplicated.

### This bundle

This bundle is the **integration layer**. It answers three questions:
1. How do design-intelligence outputs become a portable DESIGN.md file? (generator)
2. How does an existing DESIGN.md file become design-intelligence context? (importer)
3. How do you know the DESIGN.md is correct? (reviewer)

---

## License

MIT
