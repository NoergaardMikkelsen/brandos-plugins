# Posters with Plakater, through Brand OS

Contents: when the actions are there · the order of calls · reading the answer of `plakater_make` · asking for a
result · giving the person the PDF · what each call costs · when to stop.

With NMIC's joined server every call below also takes `brand` (for example `"brand": "molslinjen"`).

## When the actions are there

The `plakater_*` tools are listed only when the person has a role in the poster tool (Plakater) in Brand OS. If they
aren't among your tools, don't guess them and don't call them by name. Tell the person, in Danish:

> Du har ikke plakatværktøjet (Plakater) i Brand OS, så jeg kan ikke lave plakaten herfra. Bed Nørgård Mikkelsen om
> adgang til det.

Only Nørgård Mikkelsen gives roles in a client's tools; don't send the person to their own administrator. The tool list
is read when the AI client connects, so once the role is there, the person reconnects the server or starts a new
conversation. Until then you can still write and check the texts (`check_copy`), so they are ready.

## The order of calls

Copy this and tick it off:

```
Plakat:
- [ ] 1. get_brand_brief (task "plakat ...", format)
- [ ] 2. plakater_list_templates
- [ ] 3. Texts for every field, checked with check_copy
- [ ] 4. plakater_check_text on the final texts
- [ ] 5. The person says go (template, format, texts)
- [ ] 6. plakater_make
- [ ] 7. plakater_result while it answers status running
- [ ] 8. The link to the person, with who can open it and for how long
```

1. **`get_brand_brief`** with the task and format, for the poster's rules.
2. **`plakater_list_templates`**, once per conversation. Use only the template keys, format keys, field names, limits
   and photo keys it returns. Formats are among A4, A3, A2, 50x70 and 70x100; the list decides.
3. **Write a text for every field** of the template. Check them with `check_copy` (the situation in
   `context.situation`). A field you don't want goes in `remove` (for example `"h2"` or `"btn"`). Sample text never
   comes along by itself, and a missing field is an error. Headlines break only at `\n`.
4. **`plakater_check_text`** once, on the final texts (at most 20 texts of 2,000 characters). It is the poster tool's
   own tone and spelling check and runs on paid services, so check final text, not every draft. `fix: true` also gives
   softer text when that is better.
5. **Ask before you make it**, unless the person already gave the texts and asked for the poster: show template,
   format and texts. A poster costs 5 of the person's 100 actions an hour.
6. **`plakater_make`** with `template`, `format` and `texts` (an object: field name to text). Optional: `orientation`
   (`portrait` by default, or `landscape`), `mode` (`print`: CMYK with bleed and crop marks, the default; `office`: RGB
   for an office printer), `remove`, `photo` (a photo key from the list), `elements` (shared images from
   `plakater_list_elements` by their `url`, with `x`, `y` and `w` in percent of the width) and `days` (see below).
7. **`plakater_result`** with the `job`, only if step 6 answered `status: running`.
8. **Give the person the link**, as described below.

## Reading the answer of plakater_make

| Answer | What to do |
|---|---|
| An error with details (the request can't be used) | Fix the arguments from what it says. Nothing started, so it cost 1. Try once more, then show the person the error |
| `days skal være et helt tal fra 1 til 90.` | Fix `days` or leave it out |
| A busy message from the tool, or `Plakatværktøjet svarer ikke lige nu. Prøv igen om lidt.` | Say so in Danish. Don't call again in a loop: once after a short while at most |
| `status: done`, `ok: false`, `violations` | The poster breaks the CVI, so there is no PDF. Fix what each violation says and call `plakater_make` again. That costs 5 again. After two more tries, show the person the violations instead |
| `status: running` and a `job` | Not done yet: ask with `plakater_result` |
| `status: done`, `ok: true`, `download` | Done: give the link |

## Asking for a result

- `plakater_result` waits up to 20 seconds on the server before it answers, so call it again straight away, without
  pausing, and only while it answers `status: running`.
- One call at a time. Never several `plakater_result` or `plakater_make` calls together.
- An error means the job is over. When it says `Kald ikke plakater_result igen med dette job`, don't. Offer a new
  poster only if the person wants one, since it costs 5 again.
- Stop after 8 calls for the same job (about three minutes). Tell the person it isn't finished and give them the job,
  so they can ask again later. The tool itself gives up on a job after a few minutes.
- `plakater_result` costs nothing against the limit, which is exactly why it must not run on forever.

## Giving the person the PDF

The answer's `download` and its `next` text say who can open the file and for how long. Follow `next`, and tell the
person that in Danish, as it stands. Don't promise more than it says. There are two cases, and the answer says which:

- **A public file** (`download.url` points straight at the file): anyone with the link can fetch it. It works for 24
  hours, and the file is deleted within 48 hours. `days` has no effect. Say: "Alle med linket kan hente filen, så del
  det kun med dem, der skal bruge det. Det virker i 24 timer."
- **A private file** (`download.url` is `https://brandos.nmic.dk/plakat/<job>`): it opens only for someone signed in to
  Brand OS with Plakater. It is kept `days` days (30 unless the person chose 1 to 90), until the date in the answer.
  You can't open it yourself. A printer needs the file sent on by the person.

`plakater_file_link` makes a link to a private file that works without login for ten minutes, and anyone holding it can
open the file in that time. Ask for it only when you must fetch the file yourself or the person can't sign in where
they are, and say it is public for those minutes. For a public file it answers that the file isn't private: then use
the link from `plakater_make`.

If `toneChecked` is false, tone and spelling weren't checked: run `plakater_check_text` and say what it found.

You have the link and the check results, not the poster itself. Don't describe how it looks beyond what you sent.

## Other poster tools

- `plakater_list_elements`: the shared images (name, kind `foto` or `grafik`, url). An empty list is a real answer: the
  templates' own photos still work.
- `plakater_open`: only the tool's address, for a person to change a poster by hand after signing in. It is never the
  finished poster.

## What each call costs

| Call | Counts against 100 an hour per person |
|---|---|
| `plakater_make` | 5. When the tool answers that nothing was started (a refusal or busy), 4 come back, so it counts as 1. A poster that ran and broke rules still counts 5 |
| `plakater_result` | 0 |
| `plakater_check_text`, `plakater_list_templates`, `plakater_list_elements`, `plakater_file_link`, `plakater_open` | 1 each |

The guide's own tools (`get_brand_brief`, `check_copy` and the rest) aren't actions and don't count here. All calls
through Connector, reads too, count towards 6,000 an hour for the whole client. When the hour's actions are used up,
the answer is `Du har brugt værktøjerne mange gange den sidste time. Prøv igen senere.`: stop making posters, tell the
person, and keep working with the guide.

## When to stop

Stop and tell the person instead of calling again when:

- the same call has failed twice with the same answer;
- `plakater_make` has given violations three times;
- `plakater_result` has answered running 8 times, or answered an error;
- the hourly limit is reached;
- the tool says it doesn't answer, after one retry.
