# ln

`ln` (*link*) skapar länkar – extra namn som pekar på en fil eller katalog.

!!! warning "Ändrar filsystemet – låg risk"
    `ln` skapar nya länkar. Med `-f` kan en befintlig fil med samma namn skrivas över.

## Vad är det?

En länk gör att samma fil eller katalog kan nås från flera ställen. Det används till exempel för att:

- ge en lång sökväg ett kort namn, t.ex. `~/data` → `/srv/minapp/data`
- peka på "aktuell version", t.ex. `current` → `release-2026-10`
- aktivera konfiguration genom att länka in den, vilket många program gör

## Hur fungerar det?

Det finns två sorters länkar:

| | **Symbolic link (symbolisk länk)** | **Hard link (hård länk)** |
|---|---|---|
| Skapas med | `ln -s` | `ln` |
| Pekar på | En sökväg (ett namn) | Samma data på disken (samma **inode**) |
| Fungerar för kataloger | Ja | Nej |
| Fungerar mellan filsystem | Ja | Nej |
| Om originalet tas bort | Länken blir trasig (**dangling link**) | Datan finns kvar via länken |
| Syns i `ls -l` som | `l` och `namn -> mål` | En vanlig fil |

En symbolic link fungerar ungefär som en genväg. I vardagen är det nästan alltid den du vill ha.

```text
~/data  ──►  /srv/minapp/data  ──►  (filernas data på disken)
symbolic link    riktig katalog
```

## Hur använder jag det?

### Grundläggande syntax

```bash
ln [OPTION]... TARGET LINK_NAME
```

Ordningen är samma som för `cp`: **först det som redan finns (target), sedan den nya länken**.

| Option (flagga) | Betydelse |
|-----------------|-----------|
| `-s` | Skapa en symbolic link (utan `-s` blir det en hard link) |
| `-f` | Ersätt en befintlig fil eller länk med samma namn (force) |
| `-n` | Behandla en befintlig länk till en katalog som en fil – används med `-f` |
| `-r` | Gör länkens mål relativt till länkens placering |
| `-v` | Visa vad som skapas (verbose) |

### Exempel

Skapa en genväg till en datakatalog:

```bash
ln -s /srv/minapp/data ~/data
ls -l ~/data
```

```text
lrwxrwxrwx 1 anna anna 16 okt 14 10:00 /home/anna/data -> /srv/minapp/data
```

Se vart en länk till slut pekar:

```bash
readlink -f ~/data
```

Byt mål på en befintlig länk till en katalog:

```bash
ln -sfn /srv/minapp/release-2 ~/current
```

Utan `-n` skulle den nya länken hamna *inuti* katalogen som `~/current` pekar på.

Hitta trasiga symboliska länkar:

```bash
find ~ -xtype l
```

## Att tänka på

- **Relativa mål räknas från länkens plats, inte från där du står.** `ln -s data/fil.txt /tmp/länk` pekar på `/tmp/data/fil.txt`. Använd absoluta sökvägar eller `-r` för att undvika misstag.
- **`lrwxrwxrwx` är normalt för symboliska länkar.** Länkens egna permissions används inte – det är målets permissions som gäller.
- **Ta bort en länk utan snedstreck på slutet.** `rm ~/data` tar bort bara länken och lämnar målet orört.

!!! danger "`rm -r länk/` raderar målets innehåll"
    Med ett avslutande snedstreck följer `rm` länken in i målkatalogen.
    `rm -r ~/data/` raderar därför **allt innehåll i `/srv/minapp/data`** och
    misslyckas sedan med att ta bort själva länken (`Not a directory`).
- **Hard links är ovanliga i vardagen** men förklarar varför `ls -l` visar ett "antal länkar" och varför `rm` egentligen tar bort ett namn, inte datan.

## Se även

- [ls](../terminal/ls.md) – se länkar med `ls -l`
- [find](find.md) – hitta länkar och trasiga länkar
- [rm](../terminal/rm.md) – ta bort länkar
