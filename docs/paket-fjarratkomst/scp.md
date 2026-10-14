# scp

`scp` (*secure copy*) kopierar filer mellan datorer via SSH.

!!! warning "Ändrar filsystemet"
    `scp` skriver **över befintliga filer på målet utan att fråga** – både på din egen dator och på servern.

## Vad är det?

`scp` fungerar som [cp](../terminal/cp.md), men källan eller målet kan ligga på en annan dator. Överföringen är krypterad och använder samma inloggning som [ssh](ssh.md) – fungerar `ssh` så fungerar `scp`.

Typiska användningsområden:

- skicka en konfigurationsfil till servern
- hämta en backup eller en loggfil från servern
- kopiera en hel katalog

## Hur fungerar det?

En fil på en annan dator skrivs med ett kolon mellan värd och sökväg:

```text
anna@192.168.1.10:/srv/backup/data.tar.gz
│    │            │
│    │            └── sökväg på servern
│    └─────────────── serverns adress (eller kortnamn från ~/.ssh/config)
└──────────────────── användarnamn på servern
```

En sökväg utan `/` i början efter kolonet utgår från användarens home directory (hemkatalog) på servern.

I nyare versioner av OpenSSH använder `scp` protokollet **SFTP** under ytan. För dig fungerar kommandot likadant.

## Hur använder jag det?

### Grundläggande syntax

```bash
scp [OPTION]... SOURCE... TARGET
```

| Option (flagga) | Betydelse |
|-----------------|-----------|
| `-r` | Kopiera kataloger rekursivt |
| `-P PORT` | Anslut till en annan port – stort `P`, till skillnad från `ssh -p` |
| `-p` | Bevara tidsstämplar och permissions |
| `-C` | Komprimera under överföringen |
| `-O` | Använd det äldre SCP-protokollet, för gamla servrar utan SFTP |

### Exempel

Skicka en fil från din dator till servern:

```bash
scp docker-compose.yml anna@192.168.1.10:/home/anna/minapp/
```

Hämta en fil från servern till den aktuella katalogen (`.`):

```bash
scp anna@192.168.1.10:/srv/backup/data.tar.gz .
```

Kopiera en hel katalog:

```bash
scp -r anna@192.168.1.10:/srv/minapp/config ./config-backup
```

Med kortnamn från `~/.ssh/config` (se [ssh](ssh.md)):

```bash
scp server:minapp/docker-compose.yml .
```

Till en server som lyssnar på port 2222:

```bash
scp -P 2222 fil.txt anna@192.168.1.10:
```

## Att tänka på

- **Glöm inte kolonet.** `scp fil.txt anna@192.168.1.10` utan `:` skapar en *lokal* fil som heter `anna@192.168.1.10`.
- **Inga varningar vid överskrivning.** Kontrollera målet först, t.ex. med `ssh server ls -l /sökväg`.
- **Behörighet på servern gäller.** Du kan bara skriva där din användare har write. Att kopiera direkt till t.ex. `/etc` fungerar inte – kopiera till din home directory och flytta sedan med `sudo mv` på servern.
- **För stora eller återkommande kopieringar är `rsync` bättre.** Det skickar bara ändringar och kan återuppta avbrutna överföringar (`rsync -av källa/ server:mål/`).

## Se även

- [ssh](ssh.md) – inloggning och kortnamn
- [cp](../terminal/cp.md) – kopiera lokalt
- [tar](tar.md) – packa ihop många filer före överföring
