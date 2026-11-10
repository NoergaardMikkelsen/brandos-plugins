# NMIC: alle brands i Brand OS

Til Nørgård Mikkelsens egne medarbejdere. Pluginet lærer en AI at bruge alle kunders brandguides gennem én MCP-server i
Brand OS: brief først, byg efter reglerne, tjek resultatet, og rapportér pr. regel. Hvert værktøj tager et `brand`, og
`list_brands` giver id'erne. Selve guiderne ligger ikke her. De kommer fra serveren med din personlige nøgle.

- MCP-server: `https://brandos.nmic.dk/api/mcp/nmic` (Streamable HTTP)
- Servernavn: `brandos`
- Login: headeren `Authorization: Bearer <din nøgle>`
- Skill: [`skills/brandos-brands`](skills/brandos-brands/SKILL.md)

Serveren svarer kun NMIC's medarbejdere. En kundes folk bruger deres eget brands plugin, for eksempel `molslinjen/`.

## Til en AI, der sætter det op

Følg trinnene for det værktøj, du kører i. Nøglen gemmes, hvor værktøjet gemmer indstillinger for personen alene: aldrig
i et projekt, der deles eller ligger i git, og aldrig i dit svar. Tjek til sidst, at serverens værktøjer er der, og kald
`list_brands`.

## 1. Lav en nøgle

Log ind på https://brandos.nmic.dk, og lav en nøgle under **Connector**. Den vises én gang og holder 90 dage. Har du en
autentificeringsapp, bekræfter du med en kode på login-siden.

## 2. Claude Code

```bash
claude plugin marketplace add NoergaardMikkelsen/brandos-plugins
claude plugin install nmic@brandos
```

Sæt nøglen i din shell-profil (for eksempel `~/.zshrc`), og genstart Claude Code:

```bash
export BRANDOS_KEY=bos_…
```

Pluginet forbinder selv til serveren med `BRANDOS_KEY`. Har du tidligere tilføjet serveren med `claude mcp add` fra
Connector, så fjern den ene igen (`claude mcp remove brandos`), så værktøjerne ikke står der to gange.
`/mcp` viser, om serveren er forbundet. Pluginet har tre skills, der starter af sig selv efter opgaven: `brandos-brands`
(brief, byg, rapportér), `brandos-check` (tjek mod guiden, forslag og ja'et) og `brandos-plakat` (plakater med Plakater).
De er korte; det rige indhold ligger på serveren som resources. Den første starter af sig selv, når en
opgave handler om en kundes brand.

**Slå automatisk opdatering til, én gang.** Markedspladser uden for Anthropics egne opdaterer ikke af sig selv. I Claude Code:
skriv `/plugin`, gå til fanen **Marketplaces**, vælg `brandos`, og vælg **Enable auto-update**. Derefter henter Claude Code
nye versioner af pluginet kort efter din første besked i en session (op til ti minutter senere), og den næste session bruger
dem. Uden det: `claude plugin marketplace update brandos` og `claude plugin update nmic@brandos`, når du vil have det nye.

## 3. Codex

```bash
git clone https://github.com/NoergaardMikkelsen/brandos-plugins ~/.brandos-plugins
mkdir -p ~/.agents/skills && cp -R ~/.brandos-plugins/nmic/skills/* ~/.agents/skills/
export BRANDOS_KEY=bos_…        # i din shell-profil
codex mcp add brandos --url https://brandos.nmic.dk/api/mcp/nmic --bearer-token-env-var BRANDOS_KEY
```

Opdatér skillen senere med `git -C ~/.brandos-plugins pull` og samme `cp`.

## 4. Claude Desktop

Claude Desktop starter kun lokale servere, så den bruger broen `mcp-remote`. I `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "brandos": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://brandos.nmic.dk/api/mcp/nmic", "--header", "Authorization:${AUTH_HEADER}"],
      "env": { "AUTH_HEADER": "Bearer bos_…" }
    }
  }
}
```

Der må ikke være mellemrum inde i et argument (Windows deler det), derfor står nøglen i `env`. Genstart Claude Desktop.
Har din konto Skills, kan du uploade mappen `skills/brandos-brands` som ZIP under Skills i indstillingerne.

## 5. Cursor

I din egen `~/.cursor/mcp.json`, ikke projektets:

```json
{
  "mcpServers": {
    "brandos": {
      "url": "https://brandos.nmic.dk/api/mcp/nmic",
      "headers": { "Authorization": "Bearer bos_…" }
    }
  }
}
```

Kan Cursor læse skills hos dig, så kopiér mappen `skills/brandos-brands` dertil.

## Når noget ikke virker

- **Udløbet:** Tryk **Forny** under Connector. Nøglen virker igen med det samme og med samme værdi.
- **Ukendt eller slettet:** Lav en ny nøgle, og sæt den ind, hvor den gamle stod.
- **"Nøglen giver ikke adgang til nmic":** Nøglen er ikke en NMIC-medarbejders. Brug dit eget brands plugin.
- **Ingen plakathandlinger:** NMIC's medarbejdere har alle kunders aktive værktøjer, så handlingerne mangler kun, hvis Plakater ikke er aktivt i Brand OS, eller nøglen ikke er en NMIC-medarbejders. Forbind serveren igen, og tjek nøglen under Connector.
- **"Brand OS svarer ikke lige nu":** Prøv igen om lidt. Din nøgle er i orden.

Alle svar og hvad AI'en gør ved dem står i [refusals.md](skills/brandos-brands/refusals.md). Grænser, filer og
plakatens PDF står i [hovedmappens AGENTS.md](../AGENTS.md).
