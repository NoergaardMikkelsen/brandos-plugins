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

A verdict of `not_evaluated` is not a pass: nothing could be judged, so describe more and run it again.

## Files

Links from `get_assets` and the brief point at the brand's own address, `/api/mcp/<brand>/files/...`. Use them as they
come: `/api/mcp/nmic/files/...` answers 403. They only answer with the key, as a header, and give 401 without it, also in
a signed-in browser.

```bash
curl -fsS -H "Authorization: Bearer $BRANDOS_KEY" -o file "<the link>"
```

If `BRANDOS_KEY` isn't in your shell, the MCP connection may still hold it. While the MCP tools work, don't ask for a new
key. Ask the user to set `BRANDOS_KEY`, or to fetch the file themselves. A file is 4 MB at most.

## Actions in a client's tools

The actions you see follow the tools you have, each with a `brand`. For a poster: `plakater_list_templates`, then
`plakater_make` with only what it returned and a text for every field. `status: running` gives a job for `plakater_result`,
called a limited number of times. Give the person the PDF link. It needs no key, anyone with it can fetch the file, and it
works for 24 hours. If the tool doesn't answer, say what it answered and don't make up a result.

Never quote brand values from memory, never skip the checks, never commit the key or put it in an address.
