---
name: brandos-plakat
description: Makes posters with their print PDF for a client brand through Brand OS' joined server for NMIC staff and the Plakater tool (plakater_* tools, with brand) - one poster, several takes to choose between (plakater_variants), fetching them, the link and what it costs. Use when asked for a plakat, poster, print, A3, A2, 50x70, 70x100, tryk, PDF, skilt or opslag til print for Molslinjen or another Brand OS brand, or when a plakater_make, plakater_variants or plakater_result answer is on the table.
---

# Posters for a client brand, with Plakater

Every call takes a `brand` (for example `"brand": "molslinjen"`).

The `plakater_*` tools are listed only when the person has Plakater in Brand OS. If they aren't among your tools, don't
guess them: tell the person, in Danish, that they need access to Plakater from Nørgård Mikkelsen, and offer the texts
checked meanwhile (`check_copy`). The server's resources carry what a good poster looks like and why
(`molslinjen://eksempler`), the photos by motif and mood (`molslinjen://katalog`, `find_photo`), and the order of calls
and costs (`molslinjen://guide-for-agenter`). Read them before you choose template, photo and text.

```
Plakat:
- [ ] 1. get_brand_brief (task "plakat ...", format, line)
- [ ] 2. plakater_list_templates (once); molslinjen://eksempler for what works and why
- [ ] 3. Texts for every field, checked with check_copy; plakater_check_text on the final texts
- [ ] 4. The person says go (template, format, line, texts; 5 of 100 an hour per poster)
- [ ] 5. plakater_make with line - or plakater_variants for two to five takes
- [ ] 6. plakater_result (job, or jobs) while it answers status running
- [ ] 7. Read check and state; fix and make again if a failure is yours to fix
- [ ] 8. The link to the person: who can open it, for how long; human review outstanding
```

Everything about the calls, the answers, the link, variants, costs and when to stop is in [posters.md](posters.md).
Follow it as written: `line` is the route whose logo the poster carries; the template's sample text never comes along by
itself; `plakater_result` is free but must not run forever; the poster's `check` is the tool's own and is not described
again to `check_design`; never call a poster approved.
