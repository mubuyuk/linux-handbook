# Linux Handbook

Ett personligt uppslagsverk för Linux – en samling kunskap, kommandon och praktiska exempel som jag använder och lär mig längs vägen.

Varje artikel förklarar ett kommando från grunden: vad det är, hur det fungerar, hur det används och vad man bör tänka på. Använd **sökfältet** högst upp för att hitta ett kommando eller begrepp direkt, eller börja i någon av kategorierna nedan.

## Kategorier

### Terminal & kommandon

Grunderna i terminalen: navigera i katalogträdet, lista innehåll och skapa, kopiera, flytta och ta bort filer.

[pwd](terminal/pwd.md) · [ls](terminal/ls.md) · [cd](terminal/cd.md) · [mkdir](terminal/mkdir.md) · [cp](terminal/cp.md) · [mv](terminal/mv.md) · [rm](terminal/rm.md)

### Filsystem & rättigheter

Vem som får göra vad: file permissions (filbehörigheter), ägare, administratörsbehörighet med `sudo`, att söka efter filer och att skapa länkar.

[chmod](filsystem/chmod.md) · [chown](filsystem/chown.md) · [sudo](filsystem/sudo.md) · [find](filsystem/find.md) · [ln](filsystem/ln.md)

### System & processer

Övervaka och styra systemet: processer, tjänster, loggar, diskutrymme och minne.

[top](system/top.md) · [ps](system/ps.md) · [kill](system/kill.md) · [systemctl](system/systemctl.md) · [journalctl](system/journalctl.md) · [df](system/df.md) · [free](system/free.md)

### Nätverk & felsökning

Förstå och felsöka nätverket: IP-adresser, anslutningar, öppna portar, DNS och vägen till andra datorer.

[ip](natverk/ip.md) · [ping](natverk/ping.md) · [curl](natverk/curl.md) · [ss](natverk/ss.md) · [dig](natverk/dig.md) · [traceroute](natverk/traceroute.md)

### Paket & fjärråtkomst

Installera och uppdatera program, logga in och kopiera filer mellan datorer, packa arkiv och söka i text.

[apt](paket-fjarratkomst/apt.md) · [ssh](paket-fjarratkomst/ssh.md) · [scp](paket-fjarratkomst/scp.md) · [tar](paket-fjarratkomst/tar.md) · [grep](paket-fjarratkomst/grep.md)

## Säkerhetsmarkeringar

Överst i varje artikel visar en färgad ruta hur riskfyllt kommandot är:

| Markering | Betyder |
|-----------|---------|
| Blå (info) | Kommandot läser eller visar bara information |
| Orange (warning) | Kommandot kan ändra filer, konfiguration eller systemtillstånd |
| Röd (danger) | Särskilt hög risk för skada eller dataförlust |

Risken beror ofta på *hur* ett kommando används – rutan förklarar vad som gäller.

!!! info "Under uppbyggnad"
    Handboken byggs ut successivt i takt med att jag lär mig nya saker och
    stöter på verkliga behov. Den gör inte anspråk på att täcka hela Linux.
