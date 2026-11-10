# Proposals: the check proposes, the person says yes, then you apply

From decision 0049 in Brand OS, `check_design` and `check_copy` don't only say pass or fail. Their `structuredContent` has
`proposals`: one per fail or warning, with the rule, the element, the reason and, where the guide gives one value to move to,
the exact changes (`apply: auto`, `changes: [{path, from, to}]`). The rest are `apply: manual`: a person decides what to do.
Each proposal has a `text` in Danish. With NMIC's joined server every call below also takes `brand`.

## The rule

**Never change a design because a check said so. Show the proposals, wait for the yes, then apply.** This is how Brand OS
keeps the person in charge of their own material, and the server enforces it: `apply_proposals` refuses without the yes.

## The steps

1. Run `check_design` (or `check_copy`). Read `structuredContent.proposals`.
2. Show the person every proposal's `text` **verbatim**, one per line, auto and manual alike. Say which can be applied for
   them and which they have to make themselves. Don't paraphrase, shorten or merge them.
3. Wait for their answer. "Ja" means the auto proposals they named (or all of them, if they said all). "Nej" means none.
   "Ret selv" or a correction means: put their text in and check again, don't apply the proposal.
4. Call `apply_proposals` with the same `design` object you gave `check_design`, unchanged; `proposal_ids` for the ones they
   said yes to; `confirmed_by_person: true`; and `shown_to_person`: the texts exactly as you showed them.
5. Read the answer: `status` `applied`, `nothing_applied` or `confirmation_required`. `applied` lists what changed, `manual`
   what a person must do, `stale` ids the server didn't recognise (the design changed: run `check_design` again). `design`
   is the changed design and `check` its new check. `human_review` is still `outstanding`: a person has to look at it.
6. Use the returned `design` from then on. Report with `report_template` what was proposed, what the person said yes to,
   and what is still open.

## When the client shows a dialog instead

With a client that speaks Streamable HTTP and elicitation (Claude Code, Claude Desktop), `apply_proposals` without the yes
makes Brand OS ask the person itself: a dialog in the client with the proposals and one choice, "Anvend forslagene". Then:

- The person's answer in that dialog **is** the yes or the no. Don't ask again in the chat, and don't call
  `apply_proposals` again with `confirmed_by_person: true` for the same proposals.
- `status: applied` with `confirmed_via: 'person'` means they said yes in the dialog; report it as their choice.
- `status: declined` means they said no. Say so, change nothing, and don't propose the same again unless the design changes.
- `status: confirmation_required` means the client couldn't ask, or nobody answered within about 20 seconds: fall back to
  the steps above, show the texts and wait for the answer in the chat.
- With a progress token, `plakater_make` and `plakater_result` also report progress while they wait; show it if your client
  does, and don't call again while a stream is open.

## What you must not do

- Don't call `apply_proposals` with `confirmed_by_person: true` before the person has seen the texts and answered. If you
  haven't shown them, you have no yes.
- Don't apply a manual proposal yourself and call it done. Say what it asks and leave it to the person.
- Don't tell the person a design is approved, checked or compliant because the proposals were applied. The check passing
  means the described design follows the rules it could judge; a person still reviews it. Never set `human_reviewed`.
- Don't make up ids. They come from the very `check_design` answer for that design.

## Photos for a poster

`find_photo(query, people, orientation, time, template)` finds the poster tool's photos by motif and mood, in Danish or
English, and answers `key` (for `plakater_make`), motif, tags, where there is room for text, and thumbnails. Choose by motif
and mood and say why. `list_assets` (or the resource `molslinjen://katalog`) has photos, icons, logos and graphics together.

## Typed answers

Every tool declares an `outputSchema` and answers `structuredContent`; the text is also there under `markdown`. Read the
data when you compute on an answer (which rules failed, which proposals are auto), and the text when you talk to the
person. A refused call has `structuredContent.error` with `code`, `message` and `next`: do what `next` says, once.
