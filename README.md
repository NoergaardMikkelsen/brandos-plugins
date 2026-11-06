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

A check's `verdict` is `pass`, `pass_with_warnings`, `fail` or `not_evaluated`. `not_evaluated` means there was nothing to
judge. It is not a pass: describe more of the work and run it again.

## Files need the key

Answers link to logos and other files at `https://brandos.nmic.dk/api/mcp/<brand>/files/...`. The address only answers
with the same key as the MCP server, sent as a header. Without it the answer is 401, also in a browser where you are
signed in to Brand OS.

```bash
curl -fsS -H "Authorization: Bearer $BRANDOS_KEY" -o logo.svg "https://brandos.nmic.dk/api/mcp/<brand>/files/..."
```

- The key goes in the header, never in the address, a file or the chat.
- A key opens only the brands it may use. A file is 4 MB at most.
- With NMIC's joined server, use the links as they come: they point at the brand's own address, and
  `/api/mcp/nmic/files/...` answers 403.

## Actions in the client's tools

A key can also act in the client's own tools. Which actions an AI sees follows the tools you have been given in Brand OS,
so an action that isn't listed means you don't have that tool, or that the brand has no such action. The guide itself
follows the key and is open for the whole brand. With NMIC's joined server, every action takes a `brand` as well.

| Action | Does |
|---|---|
| `plakater_list_templates` | The poster tool's templates and formats, with the text fields of each |
| `plakater_list_elements` | Its shared images |
| `plakater_check_text` | Its own check of tone and spelling |
| `plakater_make` | Makes a poster and its PDF, with the tool itself |
| `plakater_result` | Fetches a poster that wasn't finished when `plakater_make` answered |
| `plakater_open` | The tool's address, for changing a poster by hand. This one needs a browser and a login |

Actions cost money on the tool's own keys, so they are limited to 100 an hour per person. A poster counts as 5, and asking
for a result counts as none. All calls through a key are limited to 6,000 an hour per client.

## Keys

A key is personal. It opens the guide of the brands you may use and the actions of the tools you have, nothing more. Lost it? Delete it under
Connector and make a new one. Leaving the company? Your keys stop working when your access ends.
