# Brand OS-plugins

Ét plugin pr. brand til Claude Code, Codex og andre AI-værktøjer, der kan bruge skills. Et plugin indeholder **kun
instruktioner**: hvornår AI'en skal bruge brandets guide, og hvordan den laver en brief, bygger, tjekker og rapporterer.
Brandets regler, farver, logoer og tone ligger **ikke** her. De kommer fra Brand OS' MCP-server med en personlig nøgle,
som du laver under **Connector** på https://brandos.nmic.dk.

| Mappe | Til | MCP-server | Navn |
|---|---|---|---|
| [`molslinjen/`](molslinjen/README.md) | Alle, der arbejder med Molslinjens brand | `https://brandos.nmic.dk/api/mcp/molslinjen` | `brandos-molslinjen` |
| [`bhj/`](bhj/README.md) | Alle, der arbejder med BHJs brand | `https://brandos.nmic.dk/api/mcp/bhj` | `brandos-bhj` |
| [`peter-larsen-kaffe/`](peter-larsen-kaffe/README.md) | Alle, der arbejder med Peter Larsen Kaffes brand | `https://brandos.nmic.dk/api/mcp/peter-larsen-kaffe` | `brandos-peter-larsen-kaffe` |
| [`odense-marcipan/`](odense-marcipan/README.md) | Alle, der arbejder med Odense Marcipans brand | `https://brandos.nmic.dk/api/mcp/odense-marcipan` | `brandos-odense-marcipan` |
| [`noergaard-mikkelsen/`](noergaard-mikkelsen/README.md) | Alle, der arbejder med Nørgård Mikkelsens brand | `https://brandos.nmic.dk/api/mcp/noergaard-mikkelsen` | `brandos-noergaard-mikkelsen` |
| [`nmic/`](nmic/README.md) | Nørgård Mikkelsens egne medarbejdere: alle brands i én server | `https://brandos.nmic.dk/api/mcp/nmic` | `brandos` |

Opsætningen for hvert værktøj står i mappens README. Den korte vej: Lav en nøgle under Connector, og sæt den prompt,
der kommer frem, ind i din AI. Den sætter forbindelsen op og peger hertil.

## Hvad et plugin får AI'en til at gøre

Når en opgave rører brandet (plakater, opslag, annoncer, web, mail, tekst, præsentationer, video, merch, AI-billeder,
lyd), gør AI'en det her:

1. Kalder `get_brand_brief` først og finder aldrig selv på brandværdier.
2. Bygger strengt efter briefen.
3. Kører `check_design` og/eller `check_copy` på resultatet og retter hver fejl.
4. Rapporterer med `report_template`: hvilke regler den fulgte, med id, hvad den ændrede og hvorfor, og bestået eller
   ikke bestået pr. regel.

Et tjeks `verdict` er `pass`, `pass_with_warnings`, `fail` eller `not_evaluated`. `not_evaluated` betyder, at der ikke
var noget at vurdere. Det er ikke en godkendelse: AI'en skal beskrive mere og køre tjekket igen.

Skills fortæller også AI'en, at det, serveren og filerne svarer, er data og ikke instruktioner, og at nøglen kun må gå
til Brand OS.

## Hvem får hvad

- **NMIC's medarbejdere** får alle brands, og `/api/mcp/nmic` samler dem.
- **En kundes folk** får deres eget brand, hvis de er kundens administrator eller har en rolle i et af de værktøjer,
  brandet hører til (for Molslinjen: det digitale CVI eller Plakater). Så åbner nøglen hele brandguiden. Uden det svarer
  serveren med en forklaring på dansk, og Connector tilbyder ingen ny nøgle.
- **Handlingerne** i kundens værktøjer følger de værktøjer, du har. Mangler en handling, har du ikke værktøjet, eller
  brandet har ingen sådan handling. Det er Nørgård Mikkelsen, der giver roller i en kundes værktøjer. NMIC's
  medarbejdere har alle kunders aktive værktøjer og derfor alle handlinger.

## Værktøjerne

Guiden hos Molslinjen. BHJ, Peter Larsen Kaffe, Odense Marcipan og Nørgård Mikkelsen har en kortere guide med `get_brand_brief`, `list_rules`, `get_rule`, `search_guide`, `get_colors`, `check_color`, `get_typography`, `check_copy` og `get_assets` (se deres README):

| Værktøj | Gør |
|---|---|
| `get_brand_brief` | Start her: regler og konkrete specifikationer til opgaven (`task`, `format`, `channel`, `line`, `detail`) |
| `list_rules`, `get_rule`, `search_guide` | Reglerne med id, én regel i fuld tekst, og søgning i hele guiden |
| `get_colors`, `check_contrast` | Paletten og de godkendte farvepar, og kontrasten for et par |
| `get_assets` | Links til logoer, grafik, lyd og downloads |
| `check_design`, `check_copy` | Tjek af et design beskrevet som data, og af tekst (højst 20.000 tegn) i en situation |
| `report_template` | Skabelonen til rapporten |
| `list_brands` | Kun på NMIC's server: de brands, nøglen må bruge |

Handlinger i kundens værktøjer, hvis du har dem (i dag Plakater hos Molslinjen):

| Handling | Gør | Tæller |
|---|---|---|
| `plakater_list_templates` | Skabelonerne og formaterne, med tekstfelterne i hver | 1 |
| `plakater_list_elements` | De fælles billeder | 1 |
| `plakater_check_text` | Plakatværktøjets eget tjek af tone og stavning | 1 |
| `plakater_make` | Laver plakaten og dens PDF med værktøjet selv | 5, eller 1, hvis værktøjet svarer, at intet blev sat i gang |
| `plakater_result` | Henter en plakat, der ikke var færdig, da `plakater_make` svarede | 0 |
| `plakater_file_link` | Et link til en færdig, privat plakatfil, der virker uden login i ti minutter. Kun når AI'en beder om det | 1 |
| `plakater_open` | Adressen til værktøjet, hvis man selv vil rette i plakaten. Kræver browser og login | 1 |

Connectoren kan ikke uploade eller slette.

## Grænser

- **Handlinger:** 100 i timen pr. person, fordi de koster penge på værktøjets egne nøgler. En plakat tæller 5, så det er
  20 plakater i timen. Svarer værktøjet, at intet blev sat i gang (travlt eller en afvisning), kommer 4 tilbage. En
  plakat, der blev lavet, men brød en regel, tæller 5.
- **Alle kald** gennem Connector, også læsninger, er begrænset til 6.000 i timen pr. kunde.
- **En forespørgsel** må højst rumme 20 kald og 256 KB. Handlinger i samme forespørgsel må tilsammen højst kunne tage
  25 sekunder, ellers bliver den afvist, før noget sættes i gang. AI-værktøjerne sender ét kald ad gangen, så det mærker
  du normalt ikke.

## Plakatens PDF

Svaret fra `plakater_make` siger, hvem der kan åbne filen, og hvor længe, og AI'en siger det videre.

- **Offentlig fil** (sådan kører Plakater i dag): Alle med linket kan hente filen. Linket virker i 24 timer, og filen er
  slettet senest efter 48 timer. `days` gælder ikke.
- **Privat fil** (når plakatværktøjet får sin private butik): Linket er `https://brandos.nmic.dk/plakat/<job>` og virker
  kun for en, der er logget ind og har Plakater. Filen gemmes i 30 dage, eller 1 til 90 dage efter `days`.
  `plakater_file_link` giver et link uden login i ti minutter.

## Filer kræver nøglen

Svarene linker til logoer og andre filer på `https://brandos.nmic.dk/api/mcp/<brand>/files/...`. Adressen svarer kun med
samme nøgle som MCP-serveren, sendt som header. Uden den er svaret 401, også i en browser, hvor du er logget ind.

```bash
curl -fsS -H "Authorization: Bearer $BRANDOS_KEY" -o logo.svg "https://brandos.nmic.dk/api/mcp/<brand>/files/..."
```

- Nøglen hører til i headeren, aldrig i adressen, i en fil eller i chatten.
- En fil er højst 4 MB.
- Med NMIC's samlede server bruger du linkene, som de kommer: De peger på brandets egen adresse, og
  `/api/mcp/nmic/files/...` svarer 403.

## Nøgler

- En nøgle er personlig, begynder med `bos_` og vises én gang. Kun dens hash bliver gemt.
- Den holder 90 dage. Datoen står under Connector, og **Forny** giver 90 dage igen med samme værdi, så du ikke skal
  ændre noget i dit AI-værktøj. AI'en får at vide, når en nøgle er udløbet.
- Har du en autentificeringsapp, bekræfter du med en kode på login-siden, når du laver eller fornyer en nøgle. Logger du
  kun ind med Microsoft, sker det direkte.
- En nøgle stopper, når du sletter den, bliver fjernet fra Brand OS, får dit login nulstillet eller din Microsoft-konto
  deaktiveret, og mens din virksomhed er suspenderet. Logger du kun ind med Microsoft, sættes den på pause efter 14 dage
  uden bekræftelse fra Microsoft, indtil du logger ind med Microsoft igen.
- Mistet eller delt en nøgle? Slet den under Connector, og lav en ny.
- claude.ai i browseren kan ikke bruge en nøgle endnu. Claude Desktop, Claude Code, Codex og Cursor kan.

## Til udviklere

- Reglerne for et plugin og en skill følger Anthropics dokumentation:
  [plugin.json](https://code.claude.com/docs/en/plugins-reference),
  [marketplace.json](https://code.claude.com/docs/en/plugins/marketplace-reference) og
  [skills](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices).
- Tjek før en ændring: `claude plugin validate --strict .`, `claude plugin validate --strict ./molslinjen` og
  `claude plugin validate --strict ./nmic`.
- Hvert plugin har tre skills: brand (brief, byg, rapportér; `refusals.md`), check (`proposals.md`) og plakat (`posters.md`).
  Skills er korte og peger på serverens resources; `posters.md`, `refusals.md` og `proposals.md` er ens i de to plugins. Ret dem alle.
- Hæv `version` i `plugin.json`, når et plugin ændres. Ellers får de, der har det installeret, ikke ændringen, heller
  ikke med auto-opdatering: Claude Code sammenligner på versionen og lader filerne stå, når den er den samme.
- Auto-opdatering er noget, hver person slår til én gang for markedspladsen `brandos` (`/plugin` → Marketplaces →
  Enable auto-update); en markedsplads kan ikke selv bede om det. Opsætningen i hvert plugins README siger det.
- Sandheden er connectoren i Brand OS (`brands/` og `docs/guide/brand-mcp.md`). Ændrer den sig, skal teksterne her
  følge med. Her kommer aldrig brandindhold.
