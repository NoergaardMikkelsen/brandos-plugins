# Molslinjen i Brand OS

Pluginet lærer en AI at bruge Molslinjens brandguide gennem Brand OS: brief først, byg efter reglerne, tjek resultatet,
og rapportér pr. regel. Har du Plakater, kan AI'en også lave plakater med deres PDF. Selve guiden ligger ikke her. Den
kommer fra MCP-serveren med din personlige nøgle.

- MCP-server: `https://brandos.nmic.dk/api/mcp/molslinjen` (Streamable HTTP)
- Servernavn: `brandos-molslinjen`
- Login: headeren `Authorization: Bearer <din nøgle>`
- Skill: [`skills/molslinjen-brand`](skills/molslinjen-brand/SKILL.md)

Du får brandet, hvis du er Molslinjens administrator i Brand OS eller har en rolle i det digitale CVI eller i Plakater.

## Til en AI, der sætter det op

Følg trinnene for det værktøj, du kører i. Nøglen gemmes, hvor værktøjet gemmer indstillinger for personen alene: aldrig
i et projekt, der deles eller ligger i git, og aldrig i dit svar. Tjek til sidst, at serverens værktøjer er der, og kald
`get_brand_brief` med en lille opgave.

## 1. Lav en nøgle

Log ind på https://brandos.nmic.dk, og lav en nøgle under **Connector**. Den vises én gang og holder 90 dage. Har du en
autentificeringsapp, bekræfter du med en kode på login-siden.

## 2. Claude Code

```bash
claude plugin marketplace add NoergaardMikkelsen/brandos-plugins
claude plugin install molslinjen@brandos
```

Sæt nøglen i din shell-profil (for eksempel `~/.zshrc`), og genstart Claude Code:

```bash
export BRANDOS_KEY=bos_…
```

Pluginet forbinder selv til serveren med `BRANDOS_KEY`. Har du tidligere tilføjet serveren med `claude mcp add` fra
Connector, så fjern den ene igen (`claude mcp remove brandos-molslinjen`), så værktøjerne ikke står der to gange.
`/mcp` viser, om serveren er forbundet. Skillen hedder `/molslinjen:molslinjen-brand`, men den starter af sig selv, når
en opgave handler om Molslinjen.

Opdatér senere med `claude plugin marketplace update brandos` og `claude plugin update molslinjen@brandos`.

## 3. Codex

```bash
git clone https://github.com/NoergaardMikkelsen/brandos-plugins ~/.brandos-plugins
mkdir -p ~/.agents/skills && cp -R ~/.brandos-plugins/molslinjen/skills/* ~/.agents/skills/
export BRANDOS_KEY=bos_…        # i din shell-profil
codex mcp add brandos-molslinjen --url https://brandos.nmic.dk/api/mcp/molslinjen --bearer-token-env-var BRANDOS_KEY
```

Opdatér skillen senere med `git -C ~/.brandos-plugins pull` og samme `cp`.

## 4. Claude Desktop

Claude Desktop starter kun lokale servere, så den bruger broen `mcp-remote`. I `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "brandos-molslinjen": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://brandos.nmic.dk/api/mcp/molslinjen", "--header", "Authorization:${AUTH_HEADER}"],
      "env": { "AUTH_HEADER": "Bearer bos_…" }
    }
  }
}
```

Der må ikke være mellemrum inde i et argument (Windows deler det), derfor står nøglen i `env`. Genstart Claude Desktop.
Har din konto Skills, kan du uploade mappen `skills/molslinjen-brand` som ZIP under Skills i indstillingerne.

## 5. Cursor

I din egen `~/.cursor/mcp.json`, ikke projektets:

```json
{
  "mcpServers": {
    "brandos-molslinjen": {
      "url": "https://brandos.nmic.dk/api/mcp/molslinjen",
      "headers": { "Authorization": "Bearer bos_…" }
    }
  }
}
```

Kan Cursor læse skills hos dig, så kopiér mappen `skills/molslinjen-brand` dertil.

## Når noget ikke virker

- **Udløbet:** Tryk **Forny** under Connector. Nøglen virker igen med det samme og med samme værdi.
- **Ukendt eller slettet:** Lav en ny nøgle, og sæt den ind, hvor den gamle stod.
- **"Din nøgle virker, men du har ikke brandguiden":** Bed Nørgård Mikkelsen om adgang til det digitale CVI eller Plakater.
- **Ingen plakathandlinger:** Du har ikke Plakater. Bed Nørgård Mikkelsen om adgang, og forbind serveren igen bagefter.
- **"Brand OS svarer ikke lige nu":** Prøv igen om lidt. Din nøgle er i orden.

Alle svar og hvad AI'en gør ved dem står i [refusals.md](skills/molslinjen-brand/refusals.md). Grænser, filer og
plakatens PDF står i [hovedmappens README](../README.md).
