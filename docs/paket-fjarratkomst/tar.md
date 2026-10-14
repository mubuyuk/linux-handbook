# tar

`tar` packar ihop många filer och kataloger till en enda fil – ett arkiv – och packar upp dem igen.

!!! warning "Läser eller ändrar"
    `tar -t` (lista innehåll) läser bara. `tar -c` skapar en ny arkivfil, och `tar -x` packar upp filer och **skriver över befintliga filer med samma namn**.

## Vad är det?

`tar` (*tape archive*) skapar ett **archive (arkiv)** – en fil som innehåller många filer, med katalogstruktur, ägare och permissions bevarade. Arkivet kan dessutom komprimeras för att ta mindre plats.

Det används till exempel för att:

- göra en backup av en katalog
- flytta många filer på en gång, t.ex. med [scp](scp.md)
- packa upp program och källkod som laddats ner som `.tar.gz`

## Hur fungerar det?

`tar` styrs av en **operation** och några tillägg. Operationen anges först:

| Operation | Betydelse |
|-----------|-----------|
| `-c` | **Create** – skapa ett arkiv |
| `-x` | **Extract** – packa upp ett arkiv |
| `-t` | **List** – visa innehållet utan att packa upp |

Filändelsen visar vilken **compression (komprimering)** som används:

| Ändelse | Komprimering | Flagga |
|---------|--------------|--------|
| `.tar` | Ingen | – |
| `.tar.gz` eller `.tgz` | gzip – snabb, vanligast | `-z` |
| `.tar.xz` | xz – mindre filer, långsammare | `-J` |
| `.tar.bz2` | bzip2 | `-j` |

## Hur använder jag det?

### Grundläggande syntax

```bash
tar [OPERATION][OPTION]... -f ARKIV [FIL]...
```

| Option (flagga) | Betydelse |
|-----------------|-----------|
| `-f ARKIV` | Arkivfilens namn. Står sist bland flaggorna, direkt före namnet |
| `-v` | Visa varje fil (verbose) |
| `-z` / `-J` / `-j` | gzip / xz / bzip2 |
| `-a` | Välj komprimering automatiskt utifrån filändelsen |
| `-C KATALOG` | Byt till katalogen först – var filerna packas från eller till |
| `--exclude=MÖNSTER` | Hoppa över filer som matchar |

Flaggorna skrivs ofta ihop: `-czvf` är samma sak som `-c -z -v -f`.

### Exempel

Skapa en komprimerad backup av en katalog:

```bash
tar -czf minapp-backup.tar.gz -C /srv minapp
```

`-C /srv minapp` gör att arkivet innehåller `minapp/...` i stället för hela sökvägen `srv/minapp/...`.

Utan vissa filer:

```bash
tar -czf projekt.tar.gz --exclude='*.log' --exclude='.git' projekt/
```

Titta i arkivet innan du packar upp:

```bash
tar -tzf minapp-backup.tar.gz
```

Packa upp i en separat katalog:

```bash
mkdir -p /tmp/aterstall
tar -xzf minapp-backup.tar.gz -C /tmp/aterstall
```

GNU tar känner igen komprimeringen själv vid uppackning, så `tar -xf fil.tar.gz` fungerar också.

## Att tänka på

!!! warning "Packa upp i en tom katalog först"
    `tar -x` skriver över befintliga filer utan att fråga. Lista innehållet med `-t`
    och packa upp till en tom katalog innan du flyttar filerna dit de ska.

- **Ordningen spelar roll för `-f`.** `tar -czf arkiv.tar.gz katalog/` fungerar. `tar -cfz arkiv.tar.gz katalog/` skapar ett okomprimerat arkiv som heter `z`, eftersom `-f` tar nästa tecken som filnamn.
- **Backup av databaser och containrar:** stoppa tjänsten först, eller använd databasens eget backupverktyg. Annars kan filerna sparas mitt i en skrivning och bli obrukbara.
- **Ägare bevaras när root packar upp.** Det är bra vid återställning, men kan ge oväntade ägare om du packar upp ett arkiv från en annan dator med `sudo`.
- **``Removing leading `/' from member names``** är ingen varning att oroa sig för – `tar` sparar sökvägar relativt, så att ett arkiv aldrig skriver över systemfiler av misstag.
- **Testa din backup.** En backup är bara värd något om den går att packa upp.

## Se även

- [scp](scp.md) – skicka arkivet till en annan dator
- [cp](../terminal/cp.md) – kopiera utan att packa
- [df](../system/df.md) – finns det plats för arkivet?
