---
bundle:
  name: design-md
  version: 1.0.0
  description: >
    DESIGN.md portable format integration for design-intelligence.
    Generates, imports, and validates Google DESIGN.md files — encoding
    the full output of design-intelligence agents into a portable design
    system token file understood by Stitch, Claude Code, Cursor, and Copilot.

includes:
  - bundle: git+https://github.com/microsoft/amplifier-bundle-design-intelligence@main
  - bundle: design-md:behaviors/design-md
---
