# BHJ i Brand OS

Pluginet lærer en AI at bruge BHJs brandguide gennem Brand OS: brief først, byg efter reglerne, tjek tekst og
farver, og rapportér pr. regel. Selve guiden ligger ikke her. Den kommer fra MCP-serveren med din personlige nøgle.

- MCP-server: `https://brandos.nmic.dk/api/mcp/bhj` (Streamable HTTP)
- Servernavn: `brandos-bhj`
- Login: headeren `Authorization: Bearer <din nøgle>`
- Skill: [`skills/bhj-brand`](skills/bhj-brand/SKILL.md)

Du får brandet, hvis du arbejder hos Nørgård Mikkelsen, er BHJs administrator i Brand OS eller har adgang til
BHJ's digital CVI.

## Til en AI, der sætter det op

Følg trinnene for det værktøj, du kører i. Nøglen gemmes, hvor værktøjet gemmer indstillinger for personen alene: aldrig
i et projekt, der deles eller ligger i git, og aldrig i dit svar. Tjek til sidst, at serverens værktøjer er der, og kald
`get_brand_brief` med en lille opgave.

## 1. Lav en nøgle

Log ind på https://brandos.nmic.dk, og lav en nøgle under **Connector**. Den vises én gang og holder 90 dage. Den
færdige prompt står lige under nøglen: sæt den ind i din AI, så sætter den det hele op.

## 2. Claude Code

```bash
claude plugin marketplace add NoergaardMikkelsen/brandos-plugins
claude plugin install bhj@brandos
```

Sæt nøglen i din shell-profil (for eksempel `~/.zshrc`), og genstart Claude Code:

```bash
export BRANDOS_KEY=bos_…
```

Pluginet forbinder selv til serveren med `BRANDOS_KEY`. Har du tidligere tilføjet serveren med `claude mcp add`, så
fjern den ene igen (`claude mcp remove brandos-bhj`). Slå automatisk opdatering til én gang: `/plugin` → **Marketplaces** →
`brandos` → **Enable auto-update**.

## 3. Codex

```bash
git clone https://github.com/NoergaardMikkelsen/brandos-plugins ~/.brandos-plugins
mkdir -p ~/.agents/skills && cp -R ~/.brandos-plugins/bhj/skills/* ~/.agents/skills/
export BRANDOS_KEY=bos_…        # i din shell-profil
codex mcp add brandos-bhj --url https://brandos.nmic.dk/api/mcp/bhj --bearer-token-env-var BRANDOS_KEY
```

## 4. Claude Desktop

I `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "brandos-bhj": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://brandos.nmic.dk/api/mcp/bhj", "--header", "Authorization:${AUTH_HEADER}"],
      "env": { "AUTH_HEADER": "Bearer bos_…" }
    }
  }
}
```

Genstart Claude Desktop. Har din konto Skills, kan du uploade mappen `skills/bhj-brand` som ZIP.

## 5. Cursor

I din egen `~/.cursor/mcp.json`, ikke projektets:

```json
{
  "mcpServers": {
    "brandos-bhj": {
      "url": "https://brandos.nmic.dk/api/mcp/bhj",
      "headers": { "Authorization": "Bearer bos_…" }
    }
  }
}
```

## Når noget ikke virker

- **Udløbet:** Tryk **Forny** under Connector.
- **Ukendt eller slettet:** Lav en ny nøgle, og sæt den ind, hvor den gamle stod.
- **"Din nøgle virker, men du har ikke brandguiden":** Bed Nørgård Mikkelsen om adgang til BHJ's digital CVI.
- **"Brand OS svarer ikke lige nu":** Prøv igen om lidt. Din nøgle er i orden.

Alle svar står i [refusals.md](skills/bhj-brand/refusals.md).
