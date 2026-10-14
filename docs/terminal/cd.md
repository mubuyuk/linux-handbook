# cd

`cd` (*change directory*) byter katalog i terminalen.

!!! info "Läskommando"
    `cd` ändrar bara var du står i ditt eget skal. Inga filer eller inställningar på systemet påverkas.

## Vad är det?

`cd` flyttar din **working directory (arbetskatalog)** – den katalog som kommandon utgår från. Det är grunden för all navigering i terminalen.

`cd` är ett **shell builtin (inbyggt kommando i skalet)**, inte ett separat program. Det måste vara så, eftersom ett separat program inte kan ändra skalets egen working directory.

## Hur fungerar det?

Du anger vart du vill gå med en **path (sökväg)**. Det finns två sorter:

| Typ | Börjar med | Exempel | Utgår från |
|-----|------------|---------|------------|
| **Absolute path (absolut sökväg)** | `/` | `/var/log` | Filsystemets rot |
| **Relative path (relativ sökväg)** | Allt annat | `projekt/docs` | Din nuvarande working directory |

Några specialtecken gör navigeringen snabbare:

| Tecken | Betyder |
|--------|---------|
| `~` | Din **home directory (hemkatalog)**, t.ex. `/home/anna` |
| `.` | Den aktuella katalogen |
| `..` | Katalogen ovanför (parent directory) |
| `-` | Katalogen du var i senast |

## Hur använder jag det?

### Grundläggande syntax

```bash
cd [DIR]
```

### Exempel

| Kommando | Resultat |
|----------|----------|
| `cd` | Gå till din home directory |
| `cd ~` | Samma sak |
| `cd /var/log` | Gå till `/var/log` (absolute path) |
| `cd projekt` | Gå till underkatalogen `projekt` (relative path) |
| `cd ..` | Gå upp en nivå |
| `cd ../..` | Gå upp två nivåer |
| `cd -` | Gå tillbaka till föregående katalog |

Ett typiskt arbetsflöde:

```bash
cd /etc/ssh      # gå till SSH:s konfiguration
ls -l            # se vad som finns
cd -             # tillbaka dit du var
pwd              # kontrollera var du står
```

Katalognamn med mellanslag måste citeras:

```bash
cd "Mina dokument"
```

## Att tänka på

- **`Permission denied`** betyder att du saknar execute (`x`) på katalogen. Se [chmod](../filsystem/chmod.md).
- **`No such file or directory`** beror oftast på stavfel eller att du står på fel ställe – kontrollera med `pwd` och `ls`.
- **Använd Tab-tangenten.** Skalets **tab completion (automatisk komplettering)** fyller i katalognamn och minskar stavfel.
- `cd` med `sudo` fungerar inte (`sudo cd /root`), eftersom `cd` är inbyggt i skalet. Behöver du arbeta i en skyddad katalog, se [sudo](../filsystem/sudo.md).

## Se även

- [pwd](pwd.md) – visa var du står
- [ls](ls.md) – lista innehållet
- [mkdir](mkdir.md) – skapa kataloger
