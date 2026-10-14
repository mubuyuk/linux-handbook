# apt

`apt` installerar, uppdaterar och tar bort program på Debian och Debian-baserade system.

!!! warning "Läser eller ändrar"
    `apt search`, `show`, `list` och `policy` läser bara. `update`, `upgrade`, `install`, `remove`, `purge` och `autoremove` ändrar systemet och kräver `sudo`.

## Vad är det?

På Debian installeras program som **packages (paket)**. `apt` är **package manager (pakethanteraren)** som hämtar paketen, installerar dem och håller dem uppdaterade. Den löser också **dependencies (beroenden)** – andra paket som ett program behöver – automatiskt.

## Hur fungerar det?

Paketen hämtas från **repositories (paketkällor)** – servrar med färdiga paket. Vilka paketkällor som används står i `/etc/apt/sources.list` och i filer under `/etc/apt/sources.list.d/`. Debian 13 använder det nyare formatet `*.sources`, t.ex. `debian.sources`.

En uppdatering sker i två steg:

| Steg | Kommando | Vad som händer |
|------|----------|----------------|
| 1 | `sudo apt update` | Hämtar den senaste **listan** över paket. Ingenting installeras |
| 2 | `sudo apt upgrade` | Installerar nyare versioner av paketen du redan har |

Glömmer du steg 1 vet `apt` inte om att det finns uppdateringar.

## Hur använder jag det?

### Grundläggande syntax

```bash
apt [OPTION]... COMMAND [PAKET]...
```

| Kommando | Typ | Betydelse |
|----------|-----|-----------|
| `search ORD` | Läser | Sök bland tillgängliga paket |
| `show PAKET` | Läser | Visa information om ett paket |
| `list --installed` | Läser | Lista installerade paket |
| `list --upgradable` | Läser | Lista paket som kan uppdateras |
| `policy PAKET` | Läser | Visa installerad version och var den kommer ifrån |
| `update` | Ändrar | Uppdatera paketlistan |
| `upgrade` | Ändrar | Uppdatera paket, utan att ta bort något |
| `full-upgrade` | Ändrar | Uppdatera paket, och ta bort paket om det krävs |
| `install PAKET` | Ändrar | Installera ett paket |
| `remove PAKET` | Ändrar | Ta bort ett paket, men behåll dess konfiguration |
| `purge PAKET` | Ändrar | Ta bort ett paket inklusive konfiguration |
| `autoremove` | Ändrar | Ta bort beroenden som inte längre behövs |

### Exempel

Den vanliga uppdateringsrutinen:

```bash
sudo apt update
apt list --upgradable
sudo apt upgrade
```

Hitta och installera ett program:

```bash
apt search htop
apt show htop
sudo apt install htop
```

Vilken version är installerad, och från vilken paketkälla?

```bash
apt policy docker-ce
```

Ta bort ett program och städa upp:

```bash
sudo apt remove htop
sudo apt autoremove
```

## Att tänka på

!!! warning "Läs listan innan du svarar `Y`"
    `apt` visar alltid vad som ska installeras, uppgraderas och **tas bort** innan det
    frågar om du vill fortsätta. Står det många paket under *will be REMOVED* – avbryt
    och ta reda på varför.

- **Uppdatera regelbundet.** Säkerhetsuppdateringar är det viktigaste skyddet för en server.
- **Kärnuppdateringar kräver omstart** innan de börjar gälla.
- **`full-upgrade` kan ta bort paket.** På en server: kör `upgrade` som rutin och `full-upgrade` när du har tid att kontrollera resultatet.
- **Var försiktig med externa paketkällor.** De ger tredje part möjlighet att installera kod med root-behörighet. Lägg bara till källor du litar på, t.ex. den officiella från Docker.
- **`apt` är gjort för människor.** I script används `apt-get`, som har ett stabilare utdataformat.

## Se även

- [sudo](../filsystem/sudo.md) – krävs för ändrande kommandon
- [df](../system/df.md) – kontrollera diskutrymme innan stora uppdateringar
- [systemctl](../system/systemctl.md) – starta om tjänster efter uppdatering
