# df

`df` (*disk free*) visar hur mycket diskutrymme som är använt och ledigt på varje filsystem.

!!! info "Läskommando"
    `df` visar bara information och ändrar ingenting på systemet.

## Vad är det?

`df` svarar på frågan "håller disken på att bli full?". En full disk är en vanlig orsak till att tjänster slutar fungera, databaser kraschar eller att uppdateringar misslyckas – så det är ett av de första kommandona att köra vid felsökning.

## Hur fungerar det?

`df` listar varje monterat **filesystem (filsystem)** – en disk, en partition eller ett virtuellt filsystem – och var det är kopplat i katalogträdet, dess **mount point (monteringspunkt)**.

```text
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda2       916G   45G  825G   6% /
/dev/sda1       510M   75M  436M  15% /boot/firmware
tmpfs           3.9G     0  3.9G   0% /dev/shm
```

| Kolumn | Betydelse |
|--------|-----------|
| `Filesystem` | Disken eller partitionen (`/dev/...`), eller ett virtuellt filsystem som `tmpfs` |
| `Size` / `Used` / `Avail` | Total storlek / använt / tillgängligt |
| `Use%` | Hur fullt filsystemet är |
| `Mounted on` | Var i katalogträdet filsystemet finns |

`tmpfs` ligger i RAM och försvinner vid omstart – det tar inget diskutrymme.

## Hur använder jag det?

### Grundläggande syntax

```bash
df [OPTION]... [FILE]...
```

| Option (flagga) | Betydelse |
|-----------------|-----------|
| `-h` | Läsbara storlekar (K, M, G) – human-readable |
| `-T` | Visa filsystemstyp, t.ex. `ext4` |
| `-x TYP` | Uteslut en filsystemstyp, t.ex. `-x tmpfs` |
| `-i` | Visa **inodes** i stället för utrymme |

### Exempel

Översikt över alla filsystem:

```bash
df -h
```

Utan de virtuella filsystemen, med typ:

```bash
df -hT -x tmpfs -x devtmpfs
```

Vilket filsystem ligger en viss katalog på, och hur mycket plats finns där?

```bash
df -h /srv
```

Kontrollera inodes – om de tar slut går det inte att skapa nya filer, även om det finns utrymme kvar:

```bash
df -i /
```

## Att tänka på

- **Håll ett öga på `Use%`.** Över ungefär 90 % är det dags att städa eller utöka.
- **`df` visar filsystem, inte kataloger.** För att se *vad* som tar plats, använd `du`: `sudo du -sh /var/* | sort -h`.
- **Avail + Used blir ofta mindre än Size.** Ext4 reserverar normalt några procent för root, så att systemet fungerar även när disken är full för vanliga användare.
- **Vanliga platsätare på en server:** loggar (se [journalctl](journalctl.md)), Docker-images och -volymer (`docker system df`) och gamla backuper.

## Se även

- [free](free.md) – minnesanvändning
- [journalctl](journalctl.md) – hur mycket loggarna tar
- [find](../filsystem/find.md) – hitta stora filer
