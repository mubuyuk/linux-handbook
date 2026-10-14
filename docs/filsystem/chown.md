# chown

`chown` (*change owner*) ändrar vilken användare och grupp som äger en fil eller katalog.

!!! danger "Ändrar systemet – kräver försiktighet"
    `chown` kräver nästan alltid `sudo`. Fel ägare på systemfiler, SSH-nycklar eller programdata kan få tjänster att sluta fungera. Var särskilt försiktig med `-R`.

## Vad är det?

Varje fil har en **owner (ägare)** och en **group (grupp)**. Tillsammans med **file permissions (filbehörigheter)** avgör de vem som får läsa, skriva och köra filen. `chown` ändrar ägare och/eller grupp – medan [chmod](chmod.md) ändrar själva permissions.

Typiska tillfällen:

- en katalog som skapats med `sudo` ägs av root, men en vanlig användare ska kunna skriva i den
- en container eller tjänst kör som en viss användare och behöver äga sin datakatalog
- filer har kopierats från en annan dator och fått fel ägare

## Hur fungerar det?

Ägare och grupp lagras som numeriska ID:n: **UID (user ID, användar-ID)** och **GID (group ID, grupp-ID)**. Namnen, t.ex. `anna`, översätts via `/etc/passwd` och `/etc/group`.

Du ser ditt eget UID och GID med `id`:

```bash
id
```

```text
uid=1000(anna) gid=1000(anna) groups=1000(anna),27(sudo),100(users)
```

Regler:

- Bara **root** kan byta ägare på en fil.
- En vanlig användare kan byta grupp på sina egna filer, men bara till en grupp hen själv är medlem i.

## Hur använder jag det?

### Grundläggande syntax

```bash
chown [OPTION]... [OWNER][:[GROUP]] FILE...
```

| Form | Betydelse |
|------|-----------|
| `anna` | Byt bara ägare |
| `anna:www-data` | Byt ägare och grupp |
| `:www-data` | Byt bara grupp (samma som `chgrp www-data`) |
| `anna:` | Byt ägare, och sätt grupp till användarens primära grupp |
| `1000:1000` | Använd numeriska UID och GID |

| Option (flagga) | Betydelse |
|-----------------|-----------|
| `-R` | Ändra rekursivt, för katalogen och allt innehåll (recursive) |
| `-v` | Visa varje ändring (verbose) |
| `-c` | Visa bara filer som faktiskt ändrades |
| `-h` | Ändra själva den symboliska länken, inte filen den pekar på |
| `--reference=RFILE` | Kopiera ägare och grupp från en annan fil |

### Exempel

Kontrollera ägaren först:

```bash
ls -l /srv/minapp
```

Ge en vanlig användare äganderätt till en katalog som skapats med `sudo`:

```bash
sudo chown anna:anna /srv/minapp
```

Ändra rekursivt för en hel katalog och visa vad som ändras:

```bash
sudo chown -Rc anna:anna /srv/minapp/data
```

Ge en container äganderätt till sin datakatalog med numeriska ID:n (vanligt när containern kör som UID 1000):

```bash
sudo chown -R 1000:1000 /srv/minapp/data
```

Byt bara grupp, så att gruppens medlemmar kan dela filerna:

```bash
sudo chown -R :www-data /var/www/sajt
```

## Att tänka på

!!! danger "Undvik `chown -R` på systemkataloger"
    `sudo chown -R anna /etc`, `/usr` eller `/` kan göra systemet obrukbart, eftersom
    många program kräver att deras filer ägs av root. Det finns inget enkelt sätt att
    ångra en rekursiv ändring.

- **Kontrollera sökvägen två gånger** innan du kör `sudo chown -R`. Ett mellanslag på fel ställe (`/srv/ minapp`) ändrar hela `/srv`.
- **Rör inte ägaren på programdata i onödan.** Databaser och andra tjänster – även i Docker – kör ofta som en egen användare (t.ex. UID 999) och vägrar starta om deras filer får fel ägare.
- **SSH är känsligt.** `~/.ssh` och `authorized_keys` måste ägas av användaren själv, annars kan SSH vägra logga in med nyckel.
- **Fel ägare är ofta bättre att rätta än att öppna permissions.** Byt ägare med `chown` hellre än att köra `chmod 777`.

## Se även

- [chmod](chmod.md) – ändra permissions
- [sudo](sudo.md) – köra kommandon som root
- [ls](../terminal/ls.md) – se ägare med `ls -l`
