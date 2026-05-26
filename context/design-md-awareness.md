## DESIGN.md Format Capabilities

This session includes support for Google's **DESIGN.md** portable design system
format — a standard for encoding visual identity in a single file that any AI
coding agent (Claude Code, Cursor, Copilot, Stitch) can read and apply.

### What DESIGN.md Is

A DESIGN.md file combines two layers:

- **YAML front matter** — Machine-readable design tokens: `colors`, `typography`,
  `spacing`, `rounded`, `components`
- **Markdown body** — Human-readable rationale in 8 ordered sections: Overview,
  Colors, Typography, Layout, Elevation & Depth, Shapes, Components, Do's and Don'ts

### Delegate to These Agents

| Task | Agent |
|------|-------|
| Serialize design decisions to a DESIGN.md file | `design-md:design-md-generator` |
| Parse an existing DESIGN.md and hydrate design context | `design-md:design-md-importer` |
| Validate, lint, or diff DESIGN.md files | `design-md:design-md-reviewer` |

### When to Trigger

- User says "export to DESIGN.md", "generate a DESIGN.md", or "make this portable" → `design-md-generator`
- User provides an existing DESIGN.md or says "I have a design file" → `design-md-importer`
- User asks "is this DESIGN.md valid?", "lint my design file", or "compare design versions" → `design-md-reviewer`
- Any recipe that ends in a portable design export → use `design-md-generator` then `design-md-reviewer`

**Do NOT attempt DESIGN.md authoring yourself.** The specialized agents carry the
full spec and know the exact mapping from design-intelligence concepts to tokens.
Motion (animation-choreographer) and responsive strategy are not expressible in
DESIGN.md — they live alongside it, not inside it.
