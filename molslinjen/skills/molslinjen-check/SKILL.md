---
name: molslinjen-check
description: Checks Molslinjen material against the brand guide through Brand OS - a design described as data (check_design), text and tone of voice (check_copy), the proposals a check makes and the person's yes before anything is applied (apply_proposals), also when the client shows the question as a dialog. Use when asked to tjek, check, review, gennemgå, rette, godkende or compare Molslinjen material (any line, Kombardo!) with the CVI or the guide, or when a check_design or check_copy result is on the table.
---

# Checking against Molslinjen's guide

The rules live on the server, not here: `molslinjen://regler/{id}` has every rule, `molslinjen://guide-for-agenter` the
order of calls. A check proposes; the person decides; a person still reviews the result. Never set or claim
`human_reviewed`, and never call a design approved because a check passed.

```
Tjek:
- [ ] 1. check_design (a structured description) and/or check_copy (the text, with context.situation)
- [ ] 2. Read structuredContent: verdict, state, per rule, proposals, not_checked
- [ ] 3. Show every proposal's text verbatim; wait for the yes (or the client's dialog does it)
- [ ] 4. apply_proposals with the ids they said yes to, confirmed_by_person: true, shown_to_person
- [ ] 5. Check again; report with report_template
```

## check_design

A structured description of what was made: `format`, `channel`, `orientation`, `line`, `background`, `logo` (present,
line, colours, width_pct, placement), `texts` (each with role, content, color, background, font, weight, size,
line_height, box), `images`, `splashes`, `buttons`. What you leave out isn't judged (`not_checked` lists it), so describe
everything you can. `verdict` is `pass`, `pass_with_warnings`, `fail` or `not_evaluated`; `not_evaluated` is not a pass:
describe more and run it again. `state` is `draft_requires_fix` or `awaiting_human_review`; neither means approved. A
poster made with `plakater_make` already carries its check: don't describe it again.

## check_copy

`text` (at most 20,000 characters) and `context.situation`: `marketing`, `velkomst`, `praktisk`, `kundeservice`,
`sikkerhed`, `forsinkelse`, `aflysning`, `klage` or `intern`. The situation decides how strict the tone rules are: no
wordplay where the guest is affected. Longer text goes in parts.

## Proposals and the yes

Follow [proposals.md](proposals.md): show the texts verbatim, wait for the answer, apply only what they said yes to, and
know that a dialog in the client (Claude Code, Claude Desktop) is the person's own answer. If the same rule still fails
after three rounds, stop and report it as open rather than looping.

## Report

`report_template` with `task` and the check's markdown: rules by id, what was done and why, deviations, pass or fail per
rule, the verdict, and that a person reviews it.
