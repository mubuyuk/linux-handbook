# ssh

`ssh` (*Secure Shell*) loggar in på en annan dator via nätverket och ger dig en krypterad terminal där.

!!! warning "Fjärråtkomst"
    `ssh` ändrar inget i sig, men allt du kör efter inloggningen körs **på den andra datorn**. Kontrollera alltid vilken dator du är inloggad på innan du kör ändrande kommandon.

## Vad är det?

`ssh` är det vanligaste sättet att administrera en Linux-server. All trafik – lösenord, kommandon och utdata – är krypterad. Du använder det för att:

- arbeta i terminalen på en server från din egen dator
- köra enstaka kommandon på servern
- kopiera filer, med [scp](scp.md)

## Hur fungerar det?

SSH består av två delar:

| Del | Program | Körs på |
|-----|---------|---------|
| **Client (klient)** | `ssh` | Din dator |
| **Server** | `sshd` | Datorn du loggar in på |

Du kan logga in med lösenord eller, bättre, med ett **key pair (nyckelpar)**:

- **Private key (privat nyckel)**, t.ex. `~/.ssh/id_ed25519` – stannar på din dator och delas aldrig.
- **Public key (publik nyckel)**, t.ex. `~/.ssh/id_ed25519.pub` – läggs i `~/.ssh/authorized_keys` på servern.

Första gången du ansluter till en server visas dess **fingerprint (fingeravtryck)**. Svarar du `yes` sparas det i `~/.ssh/known_hosts`. Vid nästa anslutning kontrollerar `ssh` att det är samma server.

## Hur använder jag det?

### Grundläggande syntax

```bash
ssh [OPTION]... [ANVÄNDARE@]HOST [KOMMANDO]
```

| Option (flagga) | Betydelse |
|-----------------|-----------|
| `-p PORT` | Anslut till en annan port än 22 |
| `-i FIL` | Använd en viss privat nyckel |
| `-v` | Visa felsökningsinformation (verbose) |
| `-L LOKALPORT:MÅL:MÅLPORT` | Vidarebefordra en lokal port via servern (**port forwarding**, portvidarebefordran) |

### Exempel

Logga in:

```bash
ssh anna@192.168.1.10
```

Avsluta sessionen med `exit` eller `Ctrl+D`.

Kör ett enskilt kommando och kom tillbaka direkt:

```bash
ssh anna@192.168.1.10 'df -h /'
```

### Logga in med nyckel i stället för lösenord

Gör en gång, på **din egen dator**:

```bash
ssh-keygen -t ed25519 -C "anna@laptop"          # 1. skapa ett nyckelpar
ssh-copy-id anna@192.168.1.10                   # 2. kopiera publika nyckeln till servern
ssh anna@192.168.1.10                           # 3. logga in – nu med nyckel
```

Välj gärna en **passphrase (lösenfras)** för nyckeln i steg 1. Då är den skyddad även om någon kommer över filen.

### Kortnamn med `~/.ssh/config`

I stället för att skriva användare, adress och port varje gång:

```text
Host server
    HostName 192.168.1.10
    User anna
    IdentityFile ~/.ssh/id_ed25519
```

Nu räcker det med:

```bash
ssh server
```

Kortnamnet fungerar även med `scp` och andra verktyg som använder SSH.

### Nå ett webbgränssnitt som bara lyssnar lokalt på servern

```bash
ssh -L 8080:localhost:8080 server
```

Öppna sedan `http://localhost:8080` i webbläsaren på din egen dator.

## Att tänka på

!!! danger "`REMOTE HOST IDENTIFICATION HAS CHANGED`"
    Varningen betyder att serverns fingerprint inte stämmer med det som sparats. Det
    händer när servern installerats om – men kan också betyda att någon försöker
    avlyssna anslutningen. Ta bara bort den gamla posten (`ssh-keygen -R 192.168.1.10`)
    om du vet varför den har ändrats.

- **Dela aldrig din private key** och lägg den aldrig i ett Git-repo.
- **Permissions måste vara strikta.** `~/.ssh` ska ha `700`, och den privata nyckeln och `authorized_keys` ska ha `600`. Annars kan SSH vägra använda dem. Se [chmod](../filsystem/chmod.md).
- **Stäng inte av lösenordsinloggning förrän nyckelinloggning fungerar.** Ändringar i `/etc/ssh/sshd_config` testas med `sudo sshd -t`. Behåll en inloggad session öppen medan du testar en ny anslutning – annars riskerar du att låsa ute dig.
- **`-v` är första steget vid felsökning.** Utdata visar vilka nycklar som provas och var det stoppar.

## Se även

- [scp](scp.md) – kopiera filer via SSH
- [systemctl](../system/systemctl.md) – SSH-serverns status (`systemctl status ssh`)
- [journalctl](../system/journalctl.md) – inloggningsförsök (`journalctl -u ssh`)
