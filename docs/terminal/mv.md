# mv

`mv` (*move*) flyttar filer och kataloger – och används också för att byta namn på dem.

!!! warning "Ändrar filsystemet"
    `mv` skriver **över befintliga filer utan att fråga**. Använd `-i` eller `-n` när du är osäker.

## Vad är det?

I Linux finns inget separat kommando för att byta namn. Att byta namn är samma sak som att flytta en fil till ett nytt namn i samma katalog. Därför gör `mv` båda sakerna.

## Hur fungerar det?

| Om målet... | händer detta |
|-------------|--------------|
| inte finns | Källan flyttas och får det nya namnet |
| är en befintlig katalog | Källan flyttas in i katalogen och behåller sitt namn |
| är en befintlig fil | Filen **skrivs över** |

Inom samma **filesystem (filsystem)** är `mv` blixtsnabbt, eftersom bara katalogposten ändras – datan flyttas inte. Mellan olika filsystem (t.ex. till en USB-disk) kopieras datan och originalet tas bort efteråt.

## Hur använder jag det?

### Grundläggande syntax

```bash
mv [OPTION]... SOURCE DEST
mv [OPTION]... SOURCE... DIRECTORY
```

| Option (flagga) | Betydelse |
|-----------------|-----------|
| `-i` | Fråga innan en befintlig fil skrivs över (interactive) |
| `-n` | Skriv aldrig över befintliga filer (no-clobber) |
| `-v` | Visa vad som flyttas (verbose) |

### Exempel

Byta namn på en fil:

```bash
mv rapport.txt rapport-2026.txt
```

Flytta en fil till en annan katalog:

```bash
mv rapport-2026.txt ~/dokument/
```

Flytta flera filer på en gång:

```bash
mv -v *.jpg ~/bilder/
```

Byta namn på en katalog:

```bash
mv gamla-projektet nya-projektet
```

Flytta utan risk att skriva över något:

```bash
mv -n nedladdning.iso ~/iso/
```

## Att tänka på

- **Ingen ångra-knapp.** En fil som skrivs över av `mv` är borta.
- **Ett stavfel i målet kan ge ett oväntat namn.** `mv fil.txt ~/dokumnet` skapar en *fil* som heter `dokumnet` om katalogen inte finns. Avsluta målkataloger med `/` (`~/dokument/`) så får du ett fel i stället.
- **Ägare och permissions följer med** när filen flyttas inom samma filsystem.
- **Var försiktig i systemkataloger.** Att flytta eller byta namn på filer i `/etc` eller `/usr` kan få tjänster att sluta fungera.

## Se även

- [cp](cp.md) – kopiera i stället för att flytta
- [rm](rm.md) – ta bort filer
- [ls](ls.md) – kontrollera resultatet
