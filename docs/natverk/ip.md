# ip

`ip` visar och ändrar nätverksinställningar: IP-adresser, nätverkskort och routing.

!!! warning "Läser eller ändrar"
    `ip address show`, `ip route show` och liknande läser bara. `ip address add`, `ip link set` och `ip route add` ändrar nätverket direkt – på en server kan det bryta din egen SSH-anslutning.

## Vad är det?

`ip` (från paketet `iproute2`) är det moderna verktyget för nätverk i Linux och ersätter äldre kommandon som `ifconfig` och `route`. Det används främst för att svara på:

- Vilken **IP address (IP-adress)** har datorn?
- Är **network interface (nätverksgränssnittet)** uppe?
- Vilken **default gateway (standardgateway)** används för att nå internet?

## Hur fungerar det?

`ip` är uppdelat i **objects (objekt)**. Det första ordet efter `ip` anger vad du vill arbeta med:

| Objekt | Kort form | Handlar om |
|--------|-----------|------------|
| `address` | `a` | IP-adresser på gränssnitten |
| `link` | `l` | Själva nätverksgränssnitten (upp/ner, MAC-adress) |
| `route` | `r` | **Routing table (routingtabell)** – vart trafiken skickas |
| `neighbour` | `n` | Kända grannar på det lokala nätet (ARP) |

Utan ytterligare kommando visar varje objekt information (`show`).

En adress skrivs ofta i **CIDR notation (CIDR-notation)**, t.ex. `192.168.1.10/24`. `/24` anger storleken på **subnet (subnät)** – här alla adresser `192.168.1.0`–`192.168.1.255`.

## Hur använder jag det?

### Grundläggande syntax

```bash
ip [OPTION]... OBJECT [COMMAND]
```

| Option (flagga) | Betydelse |
|-----------------|-----------|
| `-br` | Kort format, en rad per gränssnitt (brief) |
| `-4` / `-6` | Bara IPv4 / IPv6 |
| `-c` | Färglagd utdata |

### Exempel

Kort översikt över alla gränssnitt och adresser:

```bash
ip -br address
```

```text
lo               UNKNOWN        127.0.0.1/8 ::1/128
eth0             UP             192.168.1.10/24 fe80::1234:56ff:fe78:9abc/64
docker0          DOWN           172.17.0.1/16
```

- `lo` är **loopback** – datorns kontakt med sig själv.
- `eth0` är det fysiska nätverkskortet. Namnet varierar, t.ex. `enp3s0` eller `wlan0`.
- `docker0` och gränssnitt som börjar på `br-` eller `veth` skapas av Docker.

Fullständig information om ett gränssnitt:

```bash
ip address show eth0
```

Visa routingtabellen:

```bash
ip route
```

```text
default via 192.168.1.1 dev eth0 proto dhcp src 192.168.1.10 metric 100
192.168.1.0/24 dev eth0 proto kernel scope link src 192.168.1.10
```

Raden `default via 192.168.1.1` visar din default gateway – routern.

Vilken väg tar trafiken till en viss adress?

```bash
ip route get 1.1.1.1
```

Visa grannar på det lokala nätet:

```bash
ip neighbour
```

## Att tänka på

!!! danger "Ändringar via SSH kan låsa ute dig"
    `sudo ip link set eth0 down` eller en felaktig adress kopplar direkt bort servern
    från nätverket – inklusive din SSH-session. Gör sådana ändringar bara när du har
    fysisk tillgång till datorn.

- **Ändringar med `ip` är tillfälliga.** De försvinner vid omstart. Permanent konfiguration görs i systemets nätverkskonfiguration, t.ex. NetworkManager eller `/etc/network/interfaces`, beroende på installation.
- **Adresser som börjar på `169.254.`** betyder att datorn inte fick någon adress från **DHCP**.
- **`ifconfig` finns ofta inte** på nyare Debian-installationer. Använd `ip`.

## Se även

- [ping](ping.md) – testa om en adress svarar
- [ss](ss.md) – visa öppna portar och anslutningar
- [traceroute](traceroute.md) – följa vägen till en adress
