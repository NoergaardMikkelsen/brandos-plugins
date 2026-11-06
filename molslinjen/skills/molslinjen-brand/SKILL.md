---
name: molslinjen-brand
description: Use for anything about Molslinjen or its brand (rederiet Molslinjen; the lines MOLSLINJEN, BORNHOLMSLINJEN, ØRESUNDSLINJEN, ALSLINJEN, LANGELANDSLINJEN, FANØLINJEN, SAMSØLINJEN, KOSTERLINJEN, KOMBARDO EXPRESSEN; Kombardo!). Posters, mockups, social media, ads, web and app UI, emails, copy and tone of voice, presentations, signage, video, merch, AI images, sound. Also when asked to check existing Molslinjen material against the guide.
---

# Molslinjen's brand, through Brand OS

The brand guide is served by the MCP server `brandos-molslinjen` (Brand OS). Everything below depends on it. If the
server is missing or answers 401, stop and tell the user: make a key under Connector at https://brandos.nmic.dk and
set it as `BRANDOS_KEY`, then restart.

## The workflow, every time

1. **Brief first.** Call `get_brand_brief(task, format, channel, line)` before you design or write anything.
   Use `get_rule`, `search_guide`, `get_colors`, `check_contrast` and `get_assets` for details and exact values.
   Never invent brand values. If the guide is silent on something, say so and state your interpretation.
2. **Build strictly from the brief.** Colours as hex from `get_colors`; only approved text/background pairs; logo
   choice, size and placement as the brief says; Korolev weights as given.
3. **Check the result.** Run `check_design` with a structured description of what you made, and `check_copy` on all
   text (give the situation: marketing, praktisk, kundeservice, sikkerhed, forsinkelse, aflysning, klage). Fix
   every FAIL and run again until it passes.
4. **Report.** Use `report_template`: rules followed by id, what you did and why ("text set to white because F3 only
   allows white on Havblå"), deviations and no-gos, pass/fail per rule, overall verdict.

A verdict of `not_evaluated` is not a pass. It means nothing could be judged: describe more and run it again.

## Files

Logo and file links from `get_assets` and the brief only answer with the key, as a header. They give 401 without it, also
in a signed-in browser.

```bash
curl -fsS -H "Authorization: Bearer $BRANDOS_KEY" -o logo.svg "<the link>"
```

Never put the key in an address, a file or your answer. If `BRANDOS_KEY` isn't in your shell, that doesn't mean the key is
missing: the MCP connection may hold it. While the MCP tools work, don't ask for a new key. Ask the user to set
`BRANDOS_KEY`, or to fetch the file themselves. A file is 4 MB at most.

## Posters

These actions are there if the person has the poster tool in Brand OS. If they aren't listed, say so, and point to the person's administrator.

1. `plakater_list_templates`. Use only the template, format and fields it returns. Never guess one.
2. `plakater_make` with a text for every field. A field you don't want goes under `remove`. Sample text never comes along.
3. If it breaks a rule, you get the rules back and no PDF. Fix the text and try again.
4. `status: running` gives you a job. Call `plakater_result` with that job, a limited number of times, and say so if it doesn't finish.
5. Give the person the PDF link. It needs no key, anyone with it can fetch the file, and it works for 24 hours.

If the tool doesn't answer, say what it answered. Don't make up a result, and don't pass off `plakater_open` as the
finished poster: it only gives the address for doing it by hand.

## Tone of voice in short

Plain, warm, everyday Danish with a glint in the eye, never at the expense of seriousness. "Du" to the reader, "vi"
for rederiet Molslinjen. The more a situation affects the guest, the less room for wordplay: safety, delays,
cancellations and complaints get no humour at all. Maritime words are a light seasoning, one image is enough; no puns
about bad weather, seasickness or running aground. The company is "rederiet Molslinjen" with a small r; the lines are
written in capitals.

## Don'ts

- Don't quote brand values from memory. Fetch them.
- Don't skip the checks because the task is small.
- Don't put the key in files you commit; it belongs in the environment.
