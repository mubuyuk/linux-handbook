# journalctl

`journalctl` läser loggarna som samlas in av systemd – från kärnan, tjänsterna och systemet.

!!! info "Läskommando – med ett undantag"
    `journalctl` läser bara loggar. Undantaget är `--vacuum-*`, som tar bort gamla loggar permanent.

## Vad är det?

systemd samlar loggmeddelanden från hela systemet i **the journal (journalen)** – en central, sökbar logg. I stället för att leta i många textfiler kan du filtrera på tjänst, tid och allvarlighetsgrad med ett enda kommando.

Det är det första verktyget att ta till när:

- en tjänst inte startar
- något hände under natten och du vill se vad
- servern har startat om oväntat

## Hur fungerar det?

Varje loggrad sparas tillsammans med metadata: tid, vilken **unit (enhet)** den kommer från, process-ID och **priority (allvarlighetsgrad)**.

| Priority | Nummer | Betydelse |
|----------|--------|-----------|
| `emerg` | 0 | Systemet är obrukbart |
| `alert` | 1 | Åtgärd krävs omedelbart |
| `crit` | 2 | Kritiskt fel |
| `err` | 3 | Fel |
| `warning` | 4 | Varning |
| `notice` | 5 | Normalt men viktigt |
| `info` | 6 | Information |
| `debug` | 7 | Felsökningsinformation |

Filtrerar du på `-p err` visas `err` och allt som är allvarligare (0–3).

## Hur använder jag det?

### Grundläggande syntax

```bash
journalctl [OPTION]...
```

| Option (flagga) | Betydelse |
|-----------------|-----------|
| `-u UNIT` | Bara loggar från en tjänst |
| `-f` | Följ loggen i realtid (follow) – avsluta med `Ctrl+C` |
| `-n N` | Visa de senaste N raderna |
| `-r` | Nyaste först (reverse) |
| `-b` | Bara sedan senaste boot. `-b -1` = föregående boot |
| `-p PRIORITY` | Filtrera på allvarlighetsgrad, t.ex. `-p err` |
| `-k` | Bara meddelanden från kärnan (kernel) |
| `--since` / `--until` | Tidsintervall, t.ex. `"1 hour ago"` eller `"2026-10-14 08:00"` |
| `--no-pager` | Skriv ut allt direkt, utan bläddring |
| `--disk-usage` | Visa hur mycket diskutrymme journalen tar |

### Exempel

De senaste 50 raderna från SSH-tjänsten:

```bash
journalctl -u ssh -n 50
```

Följ en tjänsts logg i realtid medan du testar:

```bash
journalctl -u docker -f
```

Alla fel sedan senaste boot:

```bash
journalctl -b -p err
```

Vad hände under den senaste timmen?

```bash
journalctl --since "1 hour ago"
```

Varför startade servern om? Titta i slutet av förra bootens logg:

```bash
journalctl -b -1 -n 100
```

Hur mycket utrymme tar loggarna?

```bash
journalctl --disk-usage
```

## Att tänka på

- **Du kan behöva `sudo`.** En vanlig användare ser bara sina egna loggar, om den inte är med i gruppen `adm` eller `systemd-journal`.
- **I bläddringsläget (pager)** navigerar du med piltangenterna, söker med `/` och avslutar med `q`.
- **`-b -1` fungerar bara om journalen sparas på disk** mellan omstarter. Annars finns bara nuvarande boot.
- **`--vacuum-time` och `--vacuum-size` raderar loggar permanent**, t.ex. `sudo journalctl --vacuum-time=4weeks`. Kontrollera först med `--disk-usage`.
- **Docker-containrars loggar** läses med `docker logs CONTAINER`, inte med `journalctl`.

## Se även

- [systemctl](systemctl.md) – status för tjänster
- [grep](../paket-fjarratkomst/grep.md) – söka i utdata
- [df](df.md) – kontrollera diskutrymme
