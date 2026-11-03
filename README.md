# Brand OS plugins

One plugin per brand for Claude Code and Codex. A plugin holds **instructions only**: when to use the brand's guide,
how to brief, build, check and report. The brand's rules, colours, logos and tone are **not** in this repository.
They come from Brand OS' MCP server, with a personal key you make under **Connector** at https://brandos.nmic.dk.

| Folder | For | MCP server |
|---|---|---|
| `molslinjen/` | Everyone who works on Molslinjen's brand | `https://brandos.nmic.dk/api/mcp/molslinjen` |
| `nmic/` | Nørgård Mikkelsen's own staff: every brand in one server | `https://brandos.nmic.dk/api/mcp/nmic` |

## Install

**1. Make a key** at https://brandos.nmic.dk → Connector. It is shown once.

**2. Claude Code**

```bash
claude plugin marketplace add NoergaardMikkelsen/brandos-plugins
claude plugin install molslinjen@brandos        # or nmic@brandos
export BRANDOS_KEY=bos_…                        # put it in your shell profile
```

The plugin's `.mcp.json` connects to the MCP server with `BRANDOS_KEY`. Restart Claude Code after setting it.

**3. Codex**

```bash
git clone https://github.com/NoergaardMikkelsen/brandos-plugins ~/.brandos-plugins
mkdir -p ~/.agents/skills && cp -R ~/.brandos-plugins/molslinjen/skills/* ~/.agents/skills/
codex mcp add brandos-molslinjen --url https://brandos.nmic.dk/api/mcp/molslinjen --bearer-token-env-var BRANDOS_KEY
export BRANDOS_KEY=bos_…
```

**Or let the AI do it:** paste the prompt shown under Connector in Brand OS; it points here.

## What a plugin does

Whenever a task touches the brand (posters, mockups, social media, ads, web, email, copy, presentations, video,
merch, AI images, sound), the skill makes the AI:

1. call `get_brand_brief` first and never invent brand values;
2. build strictly from the brief;
3. run `check_design` and/or `check_copy` on the result and fix every FAIL;
4. report which rules it followed, by id, what it changed and why, and pass/fail per rule.

## Keys

A key is personal. It gives an AI tool exactly what you may see in Brand OS, nothing more. Lost it? Delete it under
Connector and make a new one. Leaving the company? Your keys stop working when your access ends.
