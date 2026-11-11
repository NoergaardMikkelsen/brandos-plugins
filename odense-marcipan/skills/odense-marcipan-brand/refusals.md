# When Brand OS refuses

Brand OS answers a refusal in Danish. Depending on the AI client you see the text itself, or only that the server
failed to connect or needs authentication (in Claude Code, `/mcp` shows the server's status). Tell the person what it
means and what they can do, in Danish, and don't retry a refusal in a loop. Never ask for the key in the chat.

## The key

Keys are made under **Connector** at https://brandos.nmic.dk. A key is personal, starts with `bos_`, is shown once, and
holds 90 days. **Forny** under Connector gives it 90 days again with the same value, so nothing in the setup changes.
Someone with an authenticator app confirms making or renewing a key with a code on the login page; someone who only
signs in with Microsoft does it directly. A key stops when its owner deletes it, is removed from Brand OS, has their
login reset or their Microsoft account disabled, and while their company is suspended.

| Brand OS says | Means | Tell the person |
|---|---|---|
| `Brug en nøgle fra Connector i Brand OS (…) som bearer token.` | No key reached the server, or not one from Connector | Make a key under Connector, set it as `BRANDOS_KEY` and restart the AI tool |
| `Nøglen udløb <dato>. Nøgler til Connector holder 90 dage, …` | The key has run out | Sign in to Brand OS and press **Forny** under Connector. It works again at once with the same value |
| `Nøglen er sat på pause, fordi din Microsoft-konto ikke er bekræftet længe. …` | A Microsoft-only person hasn't been confirmed by Microsoft for 14 days | Sign in to Brand OS with Microsoft. Then it works again |
| `Nøglen er ukendt eller slettet. Lav en ny under Connector i Brand OS.` | The key was deleted, mistyped or stopped | Make a new key under Connector and put it where the old one was |
| `For mange forsøg med en nøgle, der ikke virker. Prøv igen om et minut.` | Many wrong keys from this address | Wait a minute, then check the key |
| `Brand OS svarer ikke lige nu. Prøv igen om lidt; din nøgle er i orden.` | Trouble at Brand OS, not the key | Try again in a little while. Don't make or ask for a new key |

## The brand

| Brand OS says | Means | Tell the person |
|---|---|---|
| `Din nøgle virker, men du har ikke brandguiden for <brand>, fordi du ikke har en rolle i et af de værktøjer, den hører til. Bed Nørgård Mikkelsen om adgang til værktøjet.` | A client's person gets the brand only as the client's administrator or with a role in one of the brand's tools (the brand's digital guide). The key itself is valid | Ask Nørgård Mikkelsen for access to one of the brand's tools. The key works again when the role is there |
| `Nøglen giver ikke adgang til <brand>.` | The key belongs to someone at another company, or the address is NMIC's joined server, which only NMIC staff can use | Use the plugin and address of your own brand |

## Limits

| Brand OS says | Means | What to do |
|---|---|---|
| `Du har brugt værktøjerne mange gange den sidste time. Prøv igen senere.` | The person's 100 actions an hour are used | Wait, then try again later |
| `Din virksomhed har brugt Connector meget den sidste time. Prøv igen senere.` | The company's 6,000 calls an hour are used | Stop calling, and tell the person to try again later |
| `Højst 20 kald i én forespørgsel.` | A batch of more than 20 messages | Send calls one at a time |
| `… kan tilsammen tage længere tid, end en forespørgsel må vare. Send dem hver for sig. Intet er sat i gang.` | Several slow actions in one request (together over 25 seconds) | Send them one at a time. Nothing was started or charged |
| `Forespørgslen er for stor.` | A request over 256 KB | Send less at once, for example `check_copy` in parts |
| `Teksten er for lang: højst 20000 tegn ad gangen. …` | `check_copy` takes 20,000 characters at most | Split the text and check each part |
| `Denne side må ikke kalde Brand OS fra en browser.` | A web page outside the allowed AI products called the server | Use a supported AI tool, not a web page |

## Files

| Brand OS says | Means | What to do |
|---|---|---|
| 401 without a key | File links need the key as a header | See "Files" in the skill. Don't open the link in a browser |
| `Filen findes ikke.` | No such file, or not one of the brand's files | Check the link from `get_assets` |
| `Filen er for stor til at hente her. Hent den i guiden i en browser.` | Over 4 MB | Tell the person to download it from the guide while signed in |
| `Filen kunne ikke hentes lige nu. Prøv igen om lidt.` | The guide didn't answer | Try once more later |

## The joined server

| Brand OS says | What to do |
|---|---|
| `Vælg et brand: …` or `Say which brand: …` | NMIC's joined server needs `brand`: call `list_brands` |

In every case: say what it answered, don't make up a result, and don't pass a refusal off as a success.
