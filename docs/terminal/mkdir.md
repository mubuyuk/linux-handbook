# mkdir

`mkdir` (*make directory*) skapar nya kataloger.

!!! warning "Ändrar filsystemet – låg risk"
    `mkdir` skapar kataloger men skriver aldrig över eller tar bort något som redan finns.

## Vad är det?

`mkdir` skapar en eller flera **directories (kataloger)**. Det används hela tiden, till exempel för att skapa en projektkatalog, en katalog för backuper eller en katalog för data som en Docker-container ska använda.

## Hur fungerar det?

`mkdir` skapar katalogen på den sökväg du anger. Finns katalogen redan, eller saknas en katalog högre upp i sökvägen, avbryts kommandot med ett fel – om du inte använder `-p`.

Den nya katalogen får **owner (ägare)** och **group (grupp)** efter den användare som kör kommandot. Standardbehörigheterna styrs av din **umask** och blir oftast `755` (`rwxr-xr-x`).

## Hur använder jag det?

### Grundläggande syntax

```bash
mkdir [OPTION]... DIRECTORY...
```

| Option (flagga) | Betydelse |
|-----------------|-----------|
| `-p` | Skapa saknade kataloger högre upp i sökvägen. Inget fel om katalogen redan finns |
| `-v` | Visa varje katalog som skapas (verbose) |
| `-m MODE` | Sätt permissions direkt, t.ex. `-m 700` |

### Exempel

Skapa en katalog:

```bash
mkdir projekt
```

Skapa en hel kedja av kataloger på en gång:

```bash
mkdir -p ~/backup/2026/oktober
```

Utan `-p` skulle det misslyckas om `~/backup/2026` inte redan finns.

Skapa flera kataloger samtidigt:

```bash
mkdir -v bilder dokument skript
```

```text
mkdir: created directory 'bilder'
mkdir: created directory 'dokument'
mkdir: created directory 'skript'
```

Skapa en privat katalog som bara du kommer åt:

```bash
mkdir -m 700 privat
```

Skapa en katalog utanför din home directory (kräver **root**):

```bash
sudo mkdir -p /srv/minapp/data
```

## Att tänka på

- **`-p` är säkert att köra flera gånger.** Det gör det lämpligt i script, eftersom det inte ger fel om katalogen redan finns.
- **Kataloger som skapas med `sudo` ägs av root.** Ska en vanlig användare eller en container skriva där behöver du ändra ägare med [chown](../filsystem/chown.md).
- **Undvik mellanslag och å, ä, ö i katalognamn** på servrar. Det fungerar, men kräver citattecken och ger lätt fel i script.

## Se även

- [cd](cd.md) – gå in i katalogen
- [chown](../filsystem/chown.md) – ändra ägare
- [chmod](../filsystem/chmod.md) – ändra permissions
