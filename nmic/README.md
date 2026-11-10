# NMIC: alle brands i Brand OS

Til Nørgård Mikkelsens egne medarbejdere. Pluginet lærer en AI at bruge alle kunders brandguides gennem én MCP-server i
Brand OS: brief først, byg efter reglerne, tjek resultatet, og rapportér pr. regel. Hvert værktøj tager et `brand`, og
`list_brands` giver id'erne. Selve guiderne ligger ikke her. De kommer fra serveren, når du er logget ind på Brand OS.

- MCP-server: `https://brandos.nmic.dk/api/mcp/nmic` (Streamable HTTP)
- Servernavn: `brandos`
- Login: Dit AI-værktøj sender dig til Brand OS, hvor du logger ind og trykker **Giv adgang**. Ingen nøgle.
- Skill: [`skills/brandos-brands`](skills/brandos-brands/SKILL.md)

Serveren svarer kun NMIC's medarbejdere. En kundes folk bruger deres eget brands plugin, for eksempel `molslinjen/`.

## Til en AI, der sætter det op

Følg trinnene for det værktøj, du kører i. Personen logger selv ind i browseren: Bed aldrig om adgangskode, kode eller
nøgle i chatten. Tjek til sidst, at serverens værktøjer er der, og kald `list_brands`.

## Sådan forbinder du

I alle værktøjer er det samme, der sker: Værktøjet åbner Brand OS i din browser, du logger ind, som du plejer, og trykker
**Giv adgang**. Adgangen holder, til du logger ud af Brand OS eller trykker **Fjern adgang** under **Connector** på
https://brandos.nmic.dk, hvor du også kan se, hvilke AI-klienter du har givet adgang.

## 1. Claude Code

```bash
claude plugin marketplace add NoergaardMikkelsen/brandos-plugins
claude plugin install nmic@brandos
```

Genstart Claude Code, skriv `/mcp`, vælg `brandos`, og vælg **Authenticate**. Log ind i browseren, og tryk **Giv adgang**.
`/mcp` viser bagefter, at serveren er forbundet.

Pluginet har tre skills, der starter af sig selv efter opgaven: brand (brief, byg, rapportér), check (tjek mod guiden,
forslag og ja'et) og plakat (plakater med Plakater). De er korte; det rige indhold ligger på serveren som resources.

**Slå automatisk opdatering til, én gang.** Markedspladser uden for Anthropics egne opdaterer ikke af sig selv. I Claude Code:
skriv `/plugin`, gå til fanen **Marketplaces**, vælg `brandos`, og vælg **Enable auto-update**. Derefter henter Claude Code
nye versioner af pluginet kort efter din første besked i en session (op til ti minutter senere), og den næste session bruger
dem. Uden det: `claude plugin marketplace update brandos` og `claude plugin update nmic@brandos`, når du vil have det nye.

**Kommer du fra en ældre version?** Før 1.6.0 forbandt pluginet med en nøgle i `BRANDOS_KEY`. Det gør det ikke længere:
Skriv `/mcp`, og log ind én gang. Har du også tilføjet serveren selv med `claude mcp add`, så fjern den
(`claude mcp remove brandos`), så værktøjerne ikke står der to gange. Nøglen kan du slette under Connector, hvis du ikke
bruger den til filer eller scripts.

## 2. Codex

```bash
git clone https://github.com/NoergaardMikkelsen/brandos-plugins ~/.brandos-plugins
mkdir -p ~/.agents/skills && cp -R ~/.brandos-plugins/nmic/skills/* ~/.agents/skills/
codex mcp add brandos --url https://brandos.nmic.dk/api/mcp/nmic
codex mcp login brandos
```

I Codex-appen: under **Plugins** vælger du **Connect to a custom MCP**, giver den navnet `brandos`, vælger
**Streamable HTTP**, sætter adressen ind, lader feltet til bearer token stå tomt og gemmer. Tryk så **Authorize** ud for
serveren. Opdatér skills senere med `git -C ~/.brandos-plugins pull` og samme `cp`.

## 3. claude.ai, Claude Desktop og ChatGPT

Tilføj adressen `https://brandos.nmic.dk/api/mcp/nmic` som en connector i indstillingerne (i Claude: **Connectors** → **Add custom connector**), og
log ind, når du bliver sendt til Brand OS. Har din konto Skills, kan du uploade mapperne under `skills/` som ZIP.

## 4. Cursor

I din egen `~/.cursor/mcp.json`, ikke projektets:

```json
{
  "mcpServers": {
    "brandos": { "url": "https://brandos.nmic.dk/api/mcp/nmic" }
  }
}
```

Cursor beder dig logge ind første gang. Kan Cursor læse skills hos dig, så kopiér mapperne under `skills/` dertil.

## Med en nøgle i stedet

En nøgle er til det, der ikke kan logge ind i en browser: et script, en server, eller når AI'en selv skal hente en fil
(logoer og andre filer kan kun hentes med en nøgle, se [hovedmappens AGENTS.md](../AGENTS.md)). Lav den under **Connector**
på https://brandos.nmic.dk; den vises én gang og holder 90 dage. Under **Sæt det selv op** samme sted står kommandoen for
hvert værktøj. Læg nøglen i din shell-profil som `BRANDOS_KEY`, aldrig i et projekt, der deles eller ligger i git, og
aldrig i chatten. Brug én vej pr. værktøj, login eller nøgle, så serveren ikke står der to gange.

## Når noget ikke virker

- **Ingen værktøjer, eller serveren "kræver login":** Log ind igen fra dit AI-værktøj (Claude Code: `/mcp`; Codex: **Authorize**). Det sker også, når du har logget ud af Brand OS eller fjernet adgangen under Connector.
- **"Ingen adgang" efter login:** Du har ikke brandguiden. Bed Nørgård Mikkelsen om adgang til et af brandets værktøjer.
- **AI'en kan ikke hente et logo eller en fil:** Filer kræver en nøgle. Se "Med en nøgle i stedet".

Bruger du en nøgle:

- **Udløbet:** Tryk **Forny** under Connector. Nøglen virker igen med det samme og med samme værdi.
- **Ukendt eller slettet:** Lav en ny nøgle, og sæt den ind, hvor den gamle stod.
- **"Nøglen giver ikke adgang til nmic":** Nøglen er ikke en NMIC-medarbejders. Brug dit eget brands plugin.
- **Ingen plakathandlinger:** NMIC's medarbejdere har alle kunders aktive værktøjer, så handlingerne mangler kun, hvis Plakater ikke er aktivt i Brand OS, eller nøglen ikke er en NMIC-medarbejders. Forbind serveren igen, og tjek nøglen under Connector.
- **"Brand OS svarer ikke lige nu":** Prøv igen om lidt. Din nøgle er i orden.

Alle svar og hvad AI'en gør ved dem står i [refusals.md](skills/brandos-brands/refusals.md). Grænser, filer og
plakatens PDF står i [hovedmappens AGENTS.md](../AGENTS.md).
