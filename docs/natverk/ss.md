# ss

`ss` (*socket statistics*) visar vilka portar som är öppna och vilka nätverksanslutningar som finns.

!!! info "Läskommando"
    `ss` visar bara information och ändrar ingenting på systemet.

## Vad är det?

När ett program tar emot anslutningar, t.ex. en webbserver, sägs det **listen (lyssna)** på en **port**. `ss` visar vilka portar som lyssnar och vilka anslutningar som är aktiva. Det är modernare och snabbare än det äldre `netstat`.

Typiska frågor som `ss` besvarar:

- Körs tjänsten verkligen, och på vilken port?
- Är porten redan upptagen av något annat?
- Lyssnar tjänsten på hela nätverket eller bara lokalt?

## Hur fungerar det?

En nätverksanslutning går genom en **socket** – en kombination av IP-adress, port och protokoll (**TCP** eller **UDP**).

```text
Netid State  Recv-Q Send-Q Local Address:Port  Peer Address:Port Process
tcp   LISTEN 0      128          0.0.0.0:22         0.0.0.0:*     users:(("sshd",pid=812,fd=3))
tcp   LISTEN 0      4096       127.0.0.1:5432       0.0.0.0:*
udp   UNCONN 0      0            0.0.0.0:53         0.0.0.0:*
```

Det viktigaste är **Local Address** – på vilken adress tjänsten lyssnar:

| Lokal adress | Betyder | Nås från |
|--------------|---------|----------|
| `0.0.0.0:22` | Alla IPv4-adresser | Hela nätverket |
| `[::]:22` eller `*:22` | Alla adresser, inklusive IPv6 | Hela nätverket |
| `127.0.0.1:5432` | Bara loopback | Endast datorn själv |
| `192.168.1.10:80` | En specifik adress | Bara via den adressen |

## Hur använder jag det?

### Grundläggande syntax

```bash
ss [OPTION]... [FILTER]
```

| Option (flagga) | Betydelse |
|-----------------|-----------|
| `-t` | TCP |
| `-u` | UDP |
| `-l` | Bara lyssnande sockets (listening) |
| `-n` | Visa portnummer, inte namn (numeric) |
| `-p` | Visa vilken process som äger socketen |
| `-a` | Alla sockets, både lyssnande och anslutna |

Den vanligaste kombinationen är `-tulpn` – TCP och UDP, lyssnande, numeriskt, med process.

### Exempel

Vilka portar lyssnar på servern?

```bash
sudo ss -tulpn
```

`sudo` behövs för att se processnamn för tjänster som körs av andra användare.

Vem använder port 80?

```bash
sudo ss -tlnp 'sport = :80'
```

Aktiva TCP-anslutningar, t.ex. inloggade SSH-sessioner:

```bash
ss -tn state established
```

## Att tänka på

- **Lyssnar på `127.0.0.1` men går inte att nå från nätverket?** Det är avsiktligt – tjänsten är bara tillgänglig lokalt. Ändringen görs i tjänstens egen konfiguration.
- **`Address already in use`** när en tjänst startar betyder att porten är upptagen. Ta reda på vem som använder den med `sudo ss -tlnp 'sport = :PORT'`.
- **Docker-portar** som publicerats med `ports:` syns ofta som en process som heter `docker-proxy`. Containrar utan publicerade portar syns inte här.
- **En lyssnande port är inte samma sak som en nåbar port.** En brandvägg (firewall) kan fortfarande blockera trafiken.

## Se även

- [curl](curl.md) – testa att tjänsten svarar
- [ip](ip.md) – vilka IP-adresser har datorn?
- [systemctl](../system/systemctl.md) – kör tjänsten?
