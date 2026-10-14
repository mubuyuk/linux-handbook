# systemctl

`systemctl` styr **systemd** – programmet som startar och övervakar tjänsterna på ett Linux-system.

!!! warning "Läser eller ändrar"
    `systemctl status`, `list-units` och `is-enabled` läser bara. `start`, `stop`, `restart`, `enable` och `disable` ändrar systemet och kräver `sudo`.

## Vad är det?

På Debian är **systemd** den första process som startar (PID 1). Den startar och övervakar alla **services (tjänster)** – bakgrundsprogram som SSH-servern, Docker och tidsynkronisering. `systemctl` är kommandot för att prata med systemd.

Du använder det för att:

- se om en tjänst körs
- starta, stoppa och starta om tjänster
- välja vilka tjänster som ska starta automatiskt vid boot

## Hur fungerar det?

Allt systemd hanterar kallas **units (enheter)**. En tjänst är en unit av typen `.service`, t.ex. `ssh.service`. Ändelsen kan oftast utelämnas.

Två saker är viktiga att skilja på:

| Begrepp | Betyder | Styrs med |
|---------|---------|-----------|
| **Active (aktiv)** | Tjänsten körs *just nu* | `start` / `stop` |
| **Enabled (aktiverad)** | Tjänsten startar *automatiskt vid boot* | `enable` / `disable` |

En tjänst kan alltså vara enabled men stoppad, eller köra men vara disabled.

## Hur använder jag det?

### Grundläggande syntax

```bash
systemctl [OPTION]... COMMAND [UNIT]...
```

| Kommando | Typ | Betydelse |
|----------|-----|-----------|
| `status UNIT` | Läser | Visa status och de senaste loggraderna |
| `is-active UNIT` | Läser | Svara `active` eller `inactive` |
| `is-enabled UNIT` | Läser | Svara `enabled` eller `disabled` |
| `list-units --type=service` | Läser | Lista aktiva tjänster |
| `--failed` | Läser | Lista units som har misslyckats |
| `start UNIT` | Ändrar | Starta tjänsten |
| `stop UNIT` | Ändrar | Stoppa tjänsten |
| `restart UNIT` | Ändrar | Stoppa och starta igen |
| `reload UNIT` | Ändrar | Läsa om konfigurationen utan omstart (om tjänsten stöder det) |
| `enable UNIT` | Ändrar | Starta automatiskt vid boot |
| `enable --now UNIT` | Ändrar | Starta automatiskt vid boot *och* starta nu |
| `disable UNIT` | Ändrar | Starta inte automatiskt vid boot |
| `daemon-reload` | Ändrar | Läs in ändrade unit-filer |

### Exempel

Kontrollera om SSH-servern körs:

```bash
systemctl status ssh
```

```text
● ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/usr/lib/systemd/system/ssh.service; enabled; preset: enabled)
     Active: active (running) since Mon 2026-10-12 08:00:00 CEST; 2 days ago
   Main PID: 812 (sshd)
```

- `Loaded: ... enabled` – startar vid boot
- `Active: active (running)` – körs nu

Se om något har misslyckats:

```bash
systemctl --failed
```

Starta om en tjänst efter en ändring i dess konfiguration:

```bash
sudo systemctl restart ssh
```

Aktivera och starta en tjänst i ett steg:

```bash
sudo systemctl enable --now docker
```

## Att tänka på

- **Läs alltid loggen när en tjänst inte startar.** `systemctl status` visar de senaste raderna. Mer finns i [journalctl](journalctl.md): `journalctl -u TJÄNST`.
- **`restart` avbryter tjänsten kort.** Starta om SSH via SSH fungerar oftast, men ett fel i konfigurationen kan låsa ute dig. Testa konfigurationen först – för SSH med `sudo sshd -t`.
- **`stop` räcker inte för att hålla en tjänst borta** efter omstart. Använd även `disable`.
- **Docker-containrar är inte systemd-tjänster.** `systemctl` hanterar Docker-tjänsten själv (`docker.service`), men containrarna hanteras med `docker` och `docker compose`.
- **Tryck `q`** för att lämna visningen om utdata öppnas i en pager.

## Se även

- [journalctl](journalctl.md) – läsa loggar från tjänster
- [ps](ps.md) – se processerna bakom en tjänst
- [sudo](../filsystem/sudo.md) – behövs för ändrande kommandon
