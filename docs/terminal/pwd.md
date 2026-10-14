# pwd

`pwd` (*print working directory*) visar vilken katalog du står i just nu.

!!! info "Läskommando"
    `pwd` visar bara information och ändrar ingenting på systemet.

## Vad är det?

I terminalen står du alltid i en **working directory (arbetskatalog)**. Alla relativa sökvägar och många kommandon utgår från den katalogen. `pwd` skriver ut den fullständiga sökvägen till din working directory.

Det är användbart för att:

- orientera sig efter flera `cd`
- dubbelkolla var man är innan man kör ett kommando som ändrar filer, t.ex. `rm` eller `mv`
- se var ett script körs ifrån

## Hur fungerar det?

`pwd` skriver ut en **absolute path (absolut sökväg)** – en sökväg som börjar med `/`, filsystemets rot:

```text
/home/anna/projekt
│ │    │    │
│ │    │    └── katalogen du står i
│ │    └─────── användarens home directory (hemkatalog)
│ └──────────── katalogen home
└────────────── roten (root directory)
```

Skalet sparar också din working directory i variabeln `$PWD`.

## Hur använder jag det?

### Grundläggande syntax

```bash
pwd [OPTION]...
```

| Option (flagga) | Betydelse |
|-----------------|-----------|
| `-L` | Visa sökvägen som du navigerat dit, inklusive symboliska länkar (standard) |
| `-P` | Visa den fysiska sökvägen, med alla symboliska länkar upplösta |

### Exempel

Visa var du står:

```bash
pwd
```

```text
/home/anna
```

Skillnaden mellan `-L` och `-P` syns när du gått in via en **symbolic link (symbolisk länk)**. Anta att `/home/anna/data` är en länk till `/srv/data`:

```bash
cd ~/data
pwd -L
pwd -P
```

```text
/home/anna/data
/srv/data
```

Använd värdet i ett kommando:

```bash
echo "Jag står i $PWD"
```

## Att tänka på

- **Kör `pwd` innan riskfyllda kommandon.** Ett `rm -r *` i fel katalog kan radera fel filer. Ett snabbt `pwd` först är en billig försäkring.
- **`pwd` finns i två varianter:** som inbyggt kommando i skalet (shell builtin) och som programmet `/usr/bin/pwd`. I praktiken fungerar de likadant.
- Prompten visar ofta en förkortad sökväg, där `~` betyder din home directory. `pwd` visar alltid hela sökvägen.

## Se även

- [cd](cd.md) – byta katalog
- [ls](ls.md) – lista innehållet i katalogen
- [ln](../filsystem/ln.md) – symboliska länkar
