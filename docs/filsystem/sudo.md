# sudo

`sudo` (*superuser do*) kör ett enskilt kommando med **root**-behörighet – administratörens fulla rättigheter.

!!! danger "Ger full kontroll över systemet"
    Allt du kör med `sudo` kan ändra eller radera vad som helst på systemet. Använd det bara när det behövs, och läs kommandot noga innan du kör det.

## Vad är det?

I Linux finns en särskild användare, **root (superanvändaren)**, som får göra allt. Av säkerhetsskäl arbetar man som en vanlig användare och använder `sudo` bara för de kommandon som verkligen kräver root, t.ex. att installera paket, ändra systemfiler eller starta om tjänster.

Fördelar jämfört med att logga in som root:

- du måste aktivt välja när du tar till dig full behörighet
- varje `sudo`-kommando loggas, så det går att se vem som gjorde vad
- du anger ditt eget lösenord, inte roots

## Hur fungerar det?

1. Du skriver `sudo kommando`.
2. `sudo` kontrollerar i sin konfiguration (`/etc/sudoers` och `/etc/sudoers.d/`) om du får köra kommandot.
3. Första gången ber det om **ditt eget** lösenord.
4. Kommandot körs som root.
5. Under en kort stund (som standard 15 minuter) behöver du inte ange lösenordet igen.

På Debian får medlemmar i gruppen `sudo` köra alla kommandon med `sudo`. Kontrollera dina grupper med `id` eller `groups`.

## Hur använder jag det?

### Grundläggande syntax

```bash
sudo [OPTION]... COMMAND
```

| Option (flagga) | Betydelse |
|-----------------|-----------|
| `-l` | Visa vilka kommandon du får köra med `sudo` (list) |
| `-u ANVÄNDARE` | Kör som en annan användare än root |
| `-i` | Starta ett interaktivt login shell som root |
| `-k` | Glöm sparade inloggningen, så att lösenord krävs nästa gång |

### Exempel

Uppdatera paketlistan (kräver root):

```bash
sudo apt update
```

Starta om en tjänst:

```bash
sudo systemctl restart ssh
```

Lista en katalog som bara root får läsa:

```bash
sudo ls -la /root
```

Kör ett kommando som en annan användare:

```bash
sudo -u www-data ls /var/www
```

Kontrollera dina rättigheter:

```bash
sudo -l
```

Skriv till en skyddad fil – med `tee`, eftersom omdirigering (`>`) inte fungerar med `sudo`:

```bash
echo "192.168.1.10 server.example" | sudo tee -a /etc/hosts
```

## Att tänka på

!!! warning "`sudo` och `>` fungerar inte ihop"
    I `sudo echo text > /etc/fil` körs bara `echo` som root. Omdirigeringen `>` görs av
    *ditt* skal, som saknar behörighet. Använd `| sudo tee fil` (skriv över) eller
    `| sudo tee -a fil` (lägg till).

- **Kör inte allt med `sudo`.** Om ett kommando ger `Permission denied` – fundera först på *varför*. Ofta är rätt lösning att rätta ägare eller permissions.
- **Filer som skapas med `sudo` ägs av root.** Det kan ge problem senare när en vanlig användare ska ändra dem. Se [chown](chown.md).
- **Kopiera aldrig `sudo`-kommandon från internet utan att förstå dem.** Ett enda kommando kan radera data eller öppna säkerhetshål.
- **Redigera aldrig `/etc/sudoers` direkt.** Använd `sudo visudo`, som kontrollerar syntaxen. Ett syntaxfel i filen kan låsa ute dig från `sudo` helt.
- **`sudo -i` gör dig till root tills du skriver `exit`.** Lätt att glömma – håll sådana sessioner korta.

## Se även

- [chown](chown.md) – ändra ägare
- [chmod](chmod.md) – ändra permissions
- [apt](../paket-fjarratkomst/apt.md) – installera paket (kräver `sudo`)
- [systemctl](../system/systemctl.md) – hantera tjänster
