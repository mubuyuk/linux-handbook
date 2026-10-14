# rm

`rm` (*remove*) tar bort filer och kataloger – permanent.

!!! danger "Ändrar systemet – kan inte ångras"
    `rm` har **ingen papperskorg**. Det som tas bort är borta. Läs kommandot en extra gång innan du trycker Enter, särskilt med `-r`, `-f`, `sudo` eller wildcards.

## Vad är det?

`rm` raderar filer. Med `-r` raderar det även kataloger med allt innehåll. Det är ett av de få kommandona där ett litet misstag kan få stora följder.

## Hur fungerar det?

`rm` tar bort filens namn från katalogen. När inga namn ([hard links](../filsystem/ln.md)) längre pekar på datan frigörs utrymmet. Det finns ingen inbyggd möjlighet att återställa filen.

Viktigt att förstå:

- Rätten att ta bort en fil styrs av **write (skrivbehörighet)** på *katalogen*, inte på själva filen.
- Är filen skrivskyddad frågar `rm` först – om du inte använder `-f`.

## Hur använder jag det?

### Grundläggande syntax

```bash
rm [OPTION]... FILE...
```

| Option (flagga) | Betydelse |
|-----------------|-----------|
| `-i` | Fråga före varje borttagning (interactive) |
| `-I` | Fråga en gång om fler än tre filer tas bort eller vid `-r` |
| `-r` | Ta bort kataloger och allt innehåll (recursive) |
| `-f` | Fråga aldrig och ignorera filer som inte finns (force) |
| `-v` | Visa vad som tas bort (verbose) |
| `-d` | Ta bort en tom katalog |

### Exempel

Ta bort en fil:

```bash
rm gammal-rapport.txt
```

Ta bort med bekräftelse:

```bash
rm -i *.log
```

Ta bort en katalog och allt innehåll – men fråga en gång först:

```bash
rm -rI gamla-projektet/
```

Säkert arbetssätt innan en större borttagning:

```bash
pwd                      # 1. står jag rätt?
ls *.tmp                 # 2. vilka filer matchar?
rm -v *.tmp              # 3. ta bort och visa vad som togs bort
```

Ta bort en fil vars namn börjar med bindestreck:

```bash
rm -- -konstigt-namn.txt
```

`--` betyder "inga fler flaggor", så `-konstigt-namn.txt` tolkas som ett filnamn.

## Att tänka på

!!! danger "Farliga mönster"
    - `rm -rf` med fel sökväg raderar allt utan att fråga.
    - `sudo rm -rf` kan radera systemfiler och göra servern obrukbar.
    - `rm -rf $KATALOG/` i ett script: om variabeln är tom blir det `rm -rf /`.
    - Ett mellanslag för mycket: `rm -rf ~/ projekt` raderar hela din home directory.

- **Testa wildcards med `ls` först.** `ls *.log` visar exakt vilka filer `rm *.log` kommer att ta bort.
- **Undvik `-f` som vana.** Det stänger av de varningar som skyddar dig.
- **`rm` skyddar `/` som standard** (`--preserve-root`), men inte andra viktiga kataloger som `/etc` eller `/srv`.
- **Var extra försiktig med persistent data**, t.ex. Docker-volymer och data under `/srv`. Ta en backup med [cp](cp.md) eller [tar](../paket-fjarratkomst/tar.md) först.

## Se även

- [ls](ls.md) – kontrollera vad som matchar innan du tar bort
- [find](../filsystem/find.md) – hitta filer att ta bort, med förhandsgranskning
- [mv](mv.md) – flytta undan i stället för att radera
