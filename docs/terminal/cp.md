# cp

`cp` (*copy*) kopierar filer och kataloger.

!!! warning "Ändrar filsystemet"
    `cp` skriver **över befintliga filer utan att fråga**. Använd `-i` när du är osäker.

## Vad är det?

`cp` skapar en kopia av en fil eller katalog. Originalet lämnas orört. Det används till exempel för att:

- ta en snabb **backup (säkerhetskopia)** av en konfigurationsfil innan du ändrar den
- kopiera filer mellan kataloger
- kopiera hela katalogträd med behörigheter och tidsstämplar bevarade

## Hur fungerar det?

`cp` läser **source (källan)** och skriver en ny fil på **destination (målet)**:

| Om målet... | händer detta |
|-------------|--------------|
| inte finns | En ny fil skapas med det namnet |
| är en befintlig katalog | Kopian läggs i katalogen med samma namn som källan |
| är en befintlig fil | Filen **skrivs över** |

Kopian ägs av användaren som kör `cp`, och får ny tidsstämpel – om du inte använder `-p` eller `-a`.

## Hur använder jag det?

### Grundläggande syntax

```bash
cp [OPTION]... SOURCE DEST
cp [OPTION]... SOURCE... DIRECTORY
```

| Option (flagga) | Betydelse |
|-----------------|-----------|
| `-i` | Fråga innan en befintlig fil skrivs över (interactive) |
| `-r` | Kopiera kataloger rekursivt, med allt innehåll |
| `-a` | Archive-läge: rekursivt och bevara permissions, ägare, tidsstämplar och länkar |
| `-p` | Bevara permissions, ägare och tidsstämplar |
| `-v` | Visa vad som kopieras (verbose) |
| `-u` | Kopiera bara om källan är nyare än målet, eller om målet saknas |

### Exempel

Backup av en konfigurationsfil innan ändring:

```bash
sudo cp -p /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
```

Kopiera en fil till en katalog:

```bash
cp rapport.txt ~/dokument/
```

Kopiera en hel katalog:

```bash
cp -r projekt/ projekt-kopia/
```

Kopiera en katalog och bevara allt (bra för backup):

```bash
cp -a ~/projekt /srv/backup/
```

Fråga innan något skrivs över:

```bash
cp -iv *.conf ~/backup/
```

## Att tänka på

- **Ingen ångra-knapp.** En fil som skrivs över av `cp` går inte att få tillbaka.
- **`-n` är föråldrad (deprecated) i nyare GNU coreutils.** Vill du hoppa över befintliga filer, använd `--update=none` i stället.
- **`-r` kopierar inte alltid ägare och permissions.** Använd `-a` när det spelar roll, t.ex. vid backup av data.
- **Snedstreck i slutet spelar roll för vissa verktyg** (t.ex. `rsync`), men för `cp -r` gör `projekt` och `projekt/` samma sak.
- **Kopiering mellan datorer** görs med [scp](../paket-fjarratkomst/scp.md).

## Se även

- [mv](mv.md) – flytta eller byta namn
- [rm](rm.md) – ta bort filer
- [tar](../paket-fjarratkomst/tar.md) – packa filer till ett arkiv
