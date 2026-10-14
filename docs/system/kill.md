# kill

`kill` skickar en **signal** till en process – oftast för att be den avsluta.

!!! danger "Ändrar systemet"
    `kill` kan avsluta program och tjänster. Osparat arbete kan gå förlorat, och fel PID kan stoppa fel program. Kontrollera alltid PID först.

## Vad är det?

Trots namnet skickar `kill` egentligen **signals (signaler)** – korta meddelanden till en process. De flesta signaler betyder "avsluta", men på olika sätt. `kill` används när ett program har hängt sig eller inte går att stoppa på vanligt sätt.

För tjänster som hanteras av systemd är [systemctl](systemctl.md) rätt verktyg – `kill` är för enskilda processer.

## Hur fungerar det?

Du anger vilken process som ska få signalen med dess **PID (process-ID)**. Vanliga signaler:

| Signal | Nummer | Betydelse |
|--------|--------|-----------|
| `SIGTERM` | 15 | Be processen avsluta snyggt. **Standard** |
| `SIGINT` | 2 | Avbryt – samma som `Ctrl+C` i terminalen |
| `SIGHUP` | 1 | Används av många tjänster för att läsa om konfigurationen |
| `SIGKILL` | 9 | Avsluta omedelbart. Kan inte fångas eller ignoreras |

Skillnaden mellan `SIGTERM` och `SIGKILL` är viktig:

- **`SIGTERM`** låter programmet spara data, stänga filer och städa upp.
- **`SIGKILL`** stoppas av kärnan direkt. Programmet hinner inte städa, vilket kan ge trasiga filer eller låsfiler som blir kvar.

## Hur använder jag det?

### Grundläggande syntax

```bash
kill [-SIGNAL] PID...
```

| Variant | Betydelse |
|---------|-----------|
| `kill PID` | Skicka `SIGTERM` |
| `kill -TERM PID` eller `kill -15 PID` | Samma sak, uttryckligen |
| `kill -KILL PID` eller `kill -9 PID` | Tvinga processen att avsluta |
| `kill -HUP PID` | Be processen läsa om konfigurationen |
| `kill -l` | Lista alla signaler |

Relaterade kommandon som väljer process efter namn:

| Kommando | Betydelse |
|----------|-----------|
| `pgrep -a NAMN` | Visa PID och kommando för processer med ett visst namn (läser bara) |
| `pkill NAMN` | Skicka `SIGTERM` till alla processer med det namnet |

### Exempel

Säkert arbetssätt – hitta, kontrollera, avsluta:

```bash
pgrep -a python3           # 1. vilka processer heter python3?
kill 12345                 # 2. be rätt process avsluta (SIGTERM)
pgrep -a python3           # 3. är den borta?
```

Om processen fortfarande finns kvar efter några sekunder:

```bash
kill -9 12345
```

Avsluta en process som ägs av en annan användare (kräver root):

```bash
sudo kill 12345
```

## Att tänka på

!!! warning "Börja alltid med `SIGTERM`"
    Använd `kill -9` bara när en vanlig `kill` inte hjälper. En databas eller ett
    program som skriver till disk kan lämna trasiga filer efter sig om den stoppas
    med `SIGKILL`.

- **Tjänster startar ofta om automatiskt.** Avslutar du en process som systemd hanterar kan den startas igen direkt. Använd `sudo systemctl stop TJÄNST` i stället.
- **Docker-containrar** stoppas med `docker stop`, inte med `kill` från värddatorn.
- **`pkill` matchar på namn** – kontrollera med `pgrep -a` först, så att du inte träffar fler processer än du tänkt.
- **Du kan bara skicka signaler till dina egna processer** utan `sudo`.

## Se även

- [ps](ps.md) – hitta processer och PID
- [top](top.md) – se processer i realtid
- [systemctl](systemctl.md) – stoppa och starta tjänster
