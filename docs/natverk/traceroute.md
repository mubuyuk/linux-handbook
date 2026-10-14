# traceroute

`traceroute` visar vägen som nätverkstrafiken tar till en adress – router för router.

!!! info "Läskommando"
    `traceroute` skickar bara testpaket och ändrar ingenting på systemet.

## Vad är det?

Trafik till internet passerar många routrar på vägen. Varje router kallas ett **hop (hopp)**. `traceroute` listar alla hopp och hur lång tid det tar att nå dem. Det hjälper dig avgöra *var* ett problem finns:

- i ditt eget nätverk
- hos din internetleverantör
- längre ut på internet eller hos mottagaren

`traceroute` ingår inte alltid i en grundinstallation av Debian:

```bash
sudo apt install traceroute
```

## Hur fungerar det?

Varje IP-paket har ett fält som heter **TTL (time to live)**. Varje router minskar värdet med ett, och när det når noll kastas paketet och routern skickar tillbaka ett felmeddelande.

`traceroute` utnyttjar det:

1. Skicka ett paket med TTL 1 → första routern svarar.
2. Skicka ett paket med TTL 2 → andra routern svarar.
3. Fortsätt tills målet nås eller max antal hopp uppnås.

Varje hopp testas tre gånger, därför visas tre tider per rad:

```text
traceroute to example.com (203.0.113.10), 30 hops max, 60 byte packets
 1  192.168.1.1     0.512 ms  0.480 ms  0.455 ms
 2  198.51.100.1    8.210 ms  8.105 ms  8.330 ms
 3  * * *
 4  203.0.113.1    15.420 ms 15.380 ms 15.510 ms
 5  203.0.113.10   16.020 ms 15.990 ms 16.100 ms
```

| Rad | Tolkning |
|-----|----------|
| `1` | Din egen router |
| `2` | Oftast internetleverantörens första router |
| `* * *` | Routern svarade inte – vanligt och ofta ofarligt |
| Sista raden | Målet nåddes |

## Hur använder jag det?

### Grundläggande syntax

```bash
traceroute [OPTION]... HOST
```

| Option (flagga) | Betydelse |
|-----------------|-----------|
| `-n` | Visa bara IP-adresser, slå inte upp namn – snabbare |
| `-m N` | Max antal hopp (standard 30) |
| `-q N` | Antal försök per hopp (standard 3) |
| `-w SEK` | Hur länge du väntar på svar per försök |
| `-I` | Använd ICMP i stället för UDP – kräver root |
| `-T` | Använd TCP – kräver root. Kombineras med `-p PORT` |

### Exempel

Följ vägen till en webbplats:

```bash
traceroute -n example.com
```

Om många rader blir `* * *` blockeras troligen UDP-paketen. Prova ICMP eller TCP:

```bash
sudo traceroute -n -I example.com
sudo traceroute -n -T -p 443 example.com
```

## Att tänka på

- **Enstaka `* * *` är normalt.** Många routrar svarar inte på den här typen av paket. Det är bara ett problem om *alla* rader efter en viss punkt saknar svar och målet aldrig nås.
- **Tiden kan öka på ett hopp utan att något är fel.** Routrar prioriterar vanlig trafik före att svara `traceroute`. Titta på om tiden *stannar kvar* på en högre nivå i de följande hoppen.
- **Alternativ:** `tracepath` (paketet `iputils-tracepath`) kräver aldrig root, och `mtr` (paketet `mtr-tiny`) kombinerar `ping` och `traceroute` i en löpande vy.
- **Börja med [ping](ping.md).** `traceroute` är nästa steg när du vet att något inte fungerar.

## Se även

- [ping](ping.md) – testa om målet svarar
- [ip](ip.md) – din egen router (`ip route`)
- [dig](dig.md) – om namnet inte går att slå upp
