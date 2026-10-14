# dig

`dig` (*domain information groper*) frågar en DNS-server vilken IP-adress ett namn har – och visar exakt vad servern svarar.

!!! info "Läskommando"
    `dig` skickar bara frågor och ändrar ingenting på systemet.

## Vad är det?

**DNS (Domain Name System)** översätter namn som `example.com` till IP-adresser. När ett namn "inte fungerar" men IP-adressen gör det är felet nästan alltid DNS. `dig` är verktyget för att felsöka det.

På Debian ingår `dig` i paketet `bind9-dnsutils`:

```bash
sudo apt install bind9-dnsutils
```

## Hur fungerar det?

`dig` skickar en **DNS query (DNS-fråga)** till en **DNS server (namnserver)** och skriver ut svaret. Utan annat angivet används servern i `/etc/resolv.conf`.

DNS lagrar olika **record types (posttyper)**:

| Posttyp | Innehåller |
|---------|------------|
| `A` | IPv4-adress |
| `AAAA` | IPv6-adress |
| `CNAME` | Ett alias – pekar på ett annat namn |
| `MX` | E-postserver för domänen |
| `TXT` | Fri text, t.ex. för verifiering |
| `NS` | Domänens namnservrar |
| `PTR` | Omvänd uppslagning: IP → namn |

Svarets `status` visar hur det gick:

| Status | Betydelse |
|--------|-----------|
| `NOERROR` | Frågan besvarades |
| `NXDOMAIN` | Namnet finns inte |
| `SERVFAIL` | DNS-servern kunde inte svara |

## Hur använder jag det?

### Grundläggande syntax

```bash
dig [@SERVER] NAMN [TYP] [+ALTERNATIV]
```

| Del | Betydelse |
|-----|-----------|
| `@SERVER` | Fråga en viss DNS-server i stället för standardservern |
| `TYP` | Posttyp, t.ex. `A`, `AAAA`, `MX` (standard är `A`) |
| `+short` | Visa bara svaret |
| `+noall +answer` | Visa bara svarsdelen, i fullt format |
| `-x IP` | Omvänd uppslagning (IP → namn) |

### Exempel

Fullständigt svar:

```bash
dig example.com
```

Det viktigaste i utdata:

```text
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 12345
;; ANSWER SECTION:
example.com.        3600    IN      A       203.0.113.10
;; SERVER: 192.168.1.2#53(192.168.1.2) (UDP)
```

- `status: NOERROR` – frågan lyckades
- `ANSWER SECTION` – svaret: namnet har IP-adressen `203.0.113.10`, och `3600` är **TTL (time to live)** i sekunder
- `SERVER` – vilken DNS-server som svarade

Bara IP-adressen:

```bash
dig +short example.com
```

Fråga en specifik DNS-server, t.ex. din lokala DNS-server eller en publik:

```bash
dig @192.168.1.2 nas.home.arpa +short
dig @1.1.1.1 example.com +short
```

Jämför svaren. Svarar den lokala servern fel men den publika rätt, ligger felet i den lokala DNS-servern.

Vilken e-postserver har en domän?

```bash
dig example.com MX +short
```

Omvänd uppslagning:

```bash
dig -x 192.168.1.10 +short
```

## Att tänka på

- **`dig` frågar DNS direkt och hoppar över `/etc/hosts`.** Program på datorn använder systemets uppslagning, som också läser `/etc/hosts`. Testa den med `getent hosts NAMN`.
- **Svar cachas.** En ändring i DNS kan dröja upp till TTL-tiden innan den syns överallt.
- **`SERVFAIL` eller timeout** pekar på problem med DNS-servern eller nätverket till den, inte på namnet.
- **`.local` används av mDNS** och fungerar ofta inte i vanlig DNS. För egna namn hemma är `home.arpa` den reserverade standarden.

## Se även

- [ping](ping.md) – testa om en adress svarar
- [curl](curl.md) – testa en webbtjänst via namn
- [ip](ip.md) – kontrollera nätverksinställningar
