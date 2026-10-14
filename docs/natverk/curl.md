# curl

`curl` hämtar data från en webbadress – och är ett av de bästa verktygen för att testa webbtjänster från terminalen.

!!! warning "Läser – men kan ändra"
    En vanlig `curl URL` läser bara. Med `-o`/`-O` skrivs filer på din dator, och med `-X POST`, `PUT` eller `DELETE` kan data ändras på servern du anropar.

## Vad är det?

`curl` (*client URL*) skickar **requests (förfrågningar)** till en server och visar svaret. Det stöder många protokoll, men används mest för **HTTP** och **HTTPS**.

På en hemmaserver används det för att:

- testa om en webbtjänst svarar, utan webbläsare
- se **HTTP status codes (HTTP-statuskoder)** och **headers (rubrikfält)**
- felsöka en **reverse proxy (omvänd proxy)**
- ladda ner filer

## Hur fungerar det?

`curl` skickar en request, tar emot en **response (svar)** och skriver svarets innehåll till terminalen. Svaret har en statuskod som visar hur det gick:

| Statuskod | Betydelse |
|-----------|-----------|
| `200` | OK |
| `301` / `302` | **Redirect (omdirigering)** till en annan adress |
| `401` / `403` | Inloggning krävs / åtkomst nekad |
| `404` | Sidan finns inte |
| `500` | Fel i servern |
| `502` / `504` | En proxy fick inget eller för sent svar från tjänsten bakom |

## Hur använder jag det?

### Grundläggande syntax

```bash
curl [OPTION]... URL
```

| Option (flagga) | Betydelse |
|-----------------|-----------|
| `-I` | Hämta bara headers (HEAD-request) |
| `-L` | Följ redirects |
| `-o FIL` | Spara svaret i en fil med valfritt namn |
| `-O` | Spara svaret med filnamnet från adressen |
| `-s` | Tyst läge – ingen förloppsindikator (silent) |
| `-S` | Visa fel även i tyst läge |
| `-f` | Avsluta med felkod vid HTTP-fel (fail) – bra i script |
| `-v` | Visa allt som skickas och tas emot (verbose) |
| `-w FORMAT` | Skriv ut vald information, t.ex. statuskoden |
| `-H "Namn: värde"` | Skicka en egen header |
| `--max-time SEK` | Avbryt om det tar för lång tid |

### Exempel

Svarar tjänsten, och med vilken statuskod?

```bash
curl -I http://192.168.1.10:8080
```

Bara statuskoden – praktiskt i script och vid snabba tester:

```bash
curl -s -o /dev/null -w "%{http_code}\n" http://192.168.1.10:8080
```

Följ redirects, t.ex. från HTTP till HTTPS:

```bash
curl -IL http://example.com
```

Testa en reverse proxy innan DNS är på plats, genom att skicka rätt `Host`-header direkt till proxyns IP:

```bash
curl -I -H "Host: app.home.arpa" http://192.168.1.10
```

Felsök en anslutning i detalj:

```bash
curl -v https://example.com
```

Ladda ner en fil:

```bash
curl -fLO https://example.com/fil.tar.gz
```

## Att tänka på

!!! danger "Kör inte nedladdade script blint"
    Instruktioner som `curl -fsSL https://... | sudo bash` kör kod från internet med
    root-behörighet utan att du ser den. Ladda hellre ner scriptet med `-o`, läs det,
    och kör det sedan.

- **`-k` (`--insecure`) stänger av certifikatkontrollen.** Använd det bara för att felsöka, aldrig i script som skickar lösenord.
- **`-o` skriver över befintliga filer** utan att fråga.
- **Utan `-f` returnerar `curl` felkod 0 även vid `404` eller `500`**, eftersom själva anslutningen lyckades.
- **`502 Bad Gateway`** från en reverse proxy betyder oftast att tjänsten bakom inte körs eller att proxyn pekar på fel adress eller port.

## Se även

- [ss](ss.md) – lyssnar tjänsten på porten?
- [dig](dig.md) – pekar namnet på rätt IP?
- [ping](ping.md) – går datorn att nå?
