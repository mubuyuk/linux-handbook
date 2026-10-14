# ping

`ping` testar om en annan dator går att nå över nätverket, och hur snabbt den svarar.

!!! info "Läskommando"
    `ping` skickar små testpaket men ändrar ingenting på systemet.

## Vad är det?

`ping` är det enklaste nätverkstestet. Det skickar en förfrågan och väntar på svar. Får du svar vet du att:

- nätverksvägen till datorn fungerar
- datorn är igång
- hur lång tid en tur och retur tar – **latency (fördröjning)**

## Hur fungerar det?

`ping` använder protokollet **ICMP (Internet Control Message Protocol)**. Det skickar ett *echo request* och mottagaren svarar med ett *echo reply*. Tiden mellan dem kallas **RTT (round-trip time, tur-och-retur-tid)**.

```text
PING 192.168.1.1 (192.168.1.1) 56(84) bytes of data.
64 bytes from 192.168.1.1: icmp_seq=1 ttl=64 time=0.512 ms
64 bytes from 192.168.1.1: icmp_seq=2 ttl=64 time=0.478 ms

--- 192.168.1.1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 0.478/0.495/0.512/0.017 ms
```

| Del | Betydelse |
|-----|-----------|
| `icmp_seq` | Paketets nummer – luckor betyder förlorade paket |
| `time` | RTT för paketet |
| `packet loss` | **Packet loss (paketförlust)** – andel paket utan svar |
| `rtt min/avg/max` | Snabbaste, genomsnittlig och långsammaste RTT |

## Hur använder jag det?

### Grundläggande syntax

```bash
ping [OPTION]... DESTINATION
```

| Option (flagga) | Betydelse |
|-----------------|-----------|
| `-c N` | Skicka N paket och avsluta (count). Utan `-c` körs `ping` tills du trycker `Ctrl+C` |
| `-i SEK` | Tid mellan paketen (interval) |
| `-W SEK` | Hur länge du väntar på svar (timeout) |
| `-4` / `-6` | Tvinga IPv4 / IPv6 |
| `-q` | Visa bara sammanfattningen (quiet) |

### Exempel

Testa fyra gånger och avsluta:

```bash
ping -c 4 192.168.1.1
```

### Felsöka nätverket steg för steg

Gå inifrån och ut. Där det slutar fungera ligger felet:

| Steg | Kommando | Om det misslyckas |
|------|----------|-------------------|
| 1. Routern | `ping -c 4 192.168.1.1` | Problem med datorns nätverk, kabel eller wifi |
| 2. Internet via IP | `ping -c 4 1.1.1.1` | Problem i routern eller hos internetleverantören |
| 3. Internet via namn | `ping -c 4 example.com` | Nätverket fungerar – men **DNS** gör det inte. Se [dig](dig.md) |

Adressen till din router hittar du med `ip route` (raden `default via`). Se [ip](ip.md).

## Att tänka på

- **Inget svar betyder inte alltid att datorn är nere.** Många brandväggar (firewall) blockerar ICMP. Testa då tjänsten direkt, t.ex. med [curl](curl.md) eller `ssh`.
- **`Destination Host Unreachable`** betyder att ingen på det lokala nätet svarade för adressen – datorn är avstängd eller har en annan adress.
- **Hög eller ojämn tid** (t.ex. 2 ms, 150 ms, 3 ms) tyder på överbelastning eller dåligt wifi.
- **Använd alltid `-c` i script**, annars avslutas `ping` aldrig.

## Se även

- [traceroute](traceroute.md) – var på vägen tar det stopp?
- [dig](dig.md) – felsöka DNS
- [ip](ip.md) – kontrollera adress och gateway
