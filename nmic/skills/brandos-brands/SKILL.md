---
name: brandos-brands
description: Use for any task about one of Nørgård Mikkelsen's clients' brands (today Molslinjen and its lines, KOMBARDO EXPRESSEN, Kombardo!). Design, copy, social media, ads, web, presentations, video, checks of existing material. Fetches the brand's guide through Brand OS instead of guessing.
---

# Client brands, through Brand OS

The MCP server `brandos` (Brand OS, NMIC's address) serves every brand you may use. Every tool takes a `brand`
argument. Call `list_brands` when unsure which id to use. If the server is missing or answers 401, tell the user to
make a key under Connector at https://brandos.nmic.dk and set it as `BRANDOS_KEY`.

## The workflow, every time

1. `list_brands`, then `get_brand_brief(brand, task, format, channel, line)` before designing or writing.
2. Build strictly from the brief; exact values from `get_colors`, `get_rule`, `get_assets`, `check_contrast`.
3. `check_design` and/or `check_copy` (with `brand`); fix every FAIL and run again.
4. Report with `report_template`: rules by id, what and why, deviations, pass/fail per rule, verdict.

Never quote brand values from memory, never skip the checks, never commit the key.
