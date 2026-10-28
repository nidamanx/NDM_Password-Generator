# NDM Password Generator

[English](README.md) · **Italiano**

[![Python](https://img.shields.io/badge/python-3.8%2B-blue.svg)](https://www.python.org/)
[![License: AGPL v3+](https://img.shields.io/badge/license-AGPL--3.0--or--later-blue.svg)](https://www.gnu.org/licenses/agpl-3.0.html)
[![Dependencies](https://img.shields.io/badge/dependencies-none-brightgreen.svg)](#requisiti)
![Version](https://img.shields.io/badge/version-1.1.0-informational)

Un generatore di password da riga di comando, in un solo file e senza
dipendenze, che crea **password ad alta entropia sicure da digitare su tastiere
mobili e da usare in URL, shell e file di configurazione**.

Le password sono alfanumeriche per impostazione predefinita (senza caratteri
che si confondono), divise in gruppi per leggerle facilmente, e **ogni risultato
ha la sua entropia misurata e un livello di robustezza** (`WEAK`, `STRONG`,
`EXCELLENT`, ...). Si possono aggiungere simboli, un prefisso fisso e chiedere
un livello di robustezza invece di una lunghezza.

> **Licenza:** AGPL-3.0-or-later

---

## Indice

- [Funzionalità](#funzionalità)
- [Requisiti](#requisiti)
- [Installazione](#installazione)
  - [Aggiornamento](#aggiornamento)
- [Avvio rapido](#avvio-rapido)
- [Utilizzo](#utilizzo)
- [Come leggere l'output](#come-leggere-loutput)
- [Entropia e livelli di robustezza](#entropia-e-livelli-di-robustezza)
- [Simboli e caratteri a rischio](#simboli-e-caratteri-a-rischio)
- [Note di sicurezza](#note-di-sicurezza)
- [Servizi esterni contattati](#servizi-esterni-contattati)
- [A cosa fare attenzione](#a-cosa-fare-attenzione)
- [Controllo dello script](#controllo-dello-script)
- [Codici di uscita](#codici-di-uscita)
- [Struttura del progetto](#struttura-del-progetto)
- [Versioni](#versioni)
- [Controlli di qualità](#controlli-di-qualità)
- [Avvertenze](#avvertenze)
- [Licenza](#licenza)
- [Autore](#autore)

---

## Funzionalità

- **Crittograficamente sicuro**: la casualità viene dal CSPRNG del sistema
  operativo tramite il modulo `secrets` di Python. Nessun seed, nessuno stato
  salvato, nessuna rete, nessun file.
- **Sicuro da digitare e incollare**: per impostazione predefinita solo lettere
  e cifre, senza `I`, `O`, `l`, `0` e `1`, che si confondono facilmente
  (alfabeto di 57 caratteri).
- **Leggibile**: l'output è diviso in gruppi (predefinito: 4 gruppi da 4) da
  dettare o digitare su un telefono. Il separatore serve solo alla
  visualizzazione e non fa mai parte della password.
- **Entropia misurata**: esatta per il modo in cui la password è generata,
  indicata in bit insieme a un livello di robustezza.
- **Scegli la robustezza, non la lunghezza**:
  `--level strong|very-strong|excellent|...` usa il minor numero di gruppi
  interi che la raggiunge.
- **Simboli facoltativi**: un insieme prudente predefinito (`-_.`), mai in prima
  o ultima posizione, con la loro posizione conteggiata esattamente
  nell'entropia.
- **Prefisso fisso facoltativo** (es. `ndm-`), indicato come privo di entropia.
- **Avviso sui caratteri a rischio**: segnala se una password contiene
  caratteri che richiedono attenzione in shell, file di configurazione o URL.
- **Controllo dei requisiti**: se manca qualcosa, esce con un messaggio chiaro.

## Requisiti

- **Python 3.8 o successivo** (usa `math.comb`).
- Solo la libreria standard di Python (`argparse`, `math`, `re`, `secrets`,
  ...). Non c'è niente da installare con `pip`.
- Una sorgente casuale del sistema operativo funzionante (`os.urandom`).

Se manca qualcosa, lo script indica cosa ed esce con codice `3`.

> Python più vecchio della 3.7 non riesce nemmeno a leggere il file, quindi lì
> il messaggio non può essere mostrato.

## Installazione

Clona il repository:

```bash
git clone https://github.com/nidamanx/NDM_Password-Generator.git
cd NDM_Password-Generator
```

oppure scarica solo lo script di una release (sostituisci `vX.Y.Z` con la
versione, per esempio `v1.1.0`):

```bash
curl -fsSLO https://raw.githubusercontent.com/nidamanx/NDM_Password-Generator/vX.Y.Z/ndm_password-generator.py
chmod +x ndm_password-generator.py
```

Nel repository lo script è già eseguibile; `chmod +x` serve solo per una copia
scaricata. Senza, si avvia con `python3 ndm_password-generator.py`. Per
controllare che la tua copia sia quella pubblicata, vedi
[Controllo dello script](#controllo-dello-script).

### Aggiornamento

Le novità di ogni versione sono in [CHANGELOG.md](CHANGELOG.md) (in inglese).
In un clone:

```bash
git pull
```

Per una copia scaricata, scarica di nuovo lo script della nuova versione come
sopra. Lo script non conserva impostazioni né file, quindi non c'è altro da
migrare.

## Avvio rapido

```bash
# Facoltativo: annota il checksum per accorgerti di modifiche successive
sha256sum ndm_password-generator.py

# Genera una password (4 gruppi da 4, 93.3 bit, VERY STRONG)
./ndm_password-generator.py
```

Esempio di output:

```text
Password : 85bkDQbZwJCK9hkt
Readable : 85bk DQbZ wJCK 9hkt
Entropy  : 93.3 bits (16 chars x 5.83 bits/char, alphabet 57)
Strength : VERY STRONG
Safety   : no known risky characters for shell, config files or URLs
```

**Usa la riga `Password`.** `Readable` esiste solo per leggere o dettare la
password più facilmente.

## Utilizzo

```text
ndm_password-generator.py [-h] [-g GROUPS] [-l LEVEL] [-w WIDTH]
                          [-c {mixed,lower-digits,lower,digits}]
                          [-y SYMBOLS] [-Y SYMBOL_SET] [-p PREFIX]
                          [-n COUNT] [-s SEPARATOR] [-q | -v] [-V]
```

### Opzioni

<!-- Column 1 is kept wide on purpose by the &nbsp; padding in the header cell,
     so option names never wrap. Do not remove it. -->
| Opzione&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Predefinito | Descrizione |
|---|---|---|
| `-g, --groups N` | `4` | Numero di gruppi (1-64). Non si può combinare con `--level`. |
| `-w, --width N` | `4` | Caratteri per gruppo (1-64). |
| `-l, --level L` | - | Robustezza desiderata: `very-weak`, `weak`, `fair`, `strong`, `very-strong`, `excellent`. Usa il minor numero di gruppi interi che la raggiunge. |
| `-c, --charset C` | `mixed` | `mixed` (A-Z a-z 2-9), `lower-digits`, `lower`, `digits`. |
| `-y, --symbols N` | `0` | Numero di simboli da includere (0-64). Mai primi o ultimi. `0` = solo alfanumerici. |
| `-Y, --symbol-set S` | `-_.` | Simboli tra cui scegliere. Richiede `-y N` con N > 0. ASCII stampabile, niente lettere, cifre o spazi. |
| `-p, --prefix TEXT` | - | Testo fisso messo davanti alla password: lettere, cifre, `.` `_` `-`; 1-32 caratteri; deve iniziare con una lettera o una cifra. Non aggiunge entropia. |
| `-n, --count N` | `1` | Quante password generare (1-1000). |
| `-s, --separator S` | spazio | Separatore solo per la riga `Readable` (messo anche dopo il prefisso). Mai parte della password. |
| `-q, --quiet` | no | Stampa solo le password, una per riga (per le pipe). Gli avvisi vanno su stderr. |
| `-v, --verbose` | no | Mostra anche i dettagli del generatore, la scala di robustezza e un riepilogo di sicurezza. |
| `-V, --version` | - | Mostra versione, data di rilascio, copyright e licenza. |
| `-h, --help` | - | Mostra l'aiuto, con le avvertenze di sicurezza complete. |

`-q` e `-v` si escludono a vicenda.

### Esempi

```bash
# Predefinito: 16 caratteri, ~93 bit
./ndm_password-generator.py

# Chiedi una robustezza invece di una lunghezza
./ndm_password-generator.py -l excellent

# 5 gruppi da 5, mostrati con i trattini (i trattini NON fanno parte della password)
./ndm_password-generator.py -g 5 -w 5 -s -

# Aggiungi 2 simboli (mai primi o ultimi)
./ndm_password-generator.py -y 2

# Insieme di simboli personalizzato (vedi le note sui caratteri a rischio)
./ndm_password-generator.py -y 3 -Y '+='

# Prefisso fisso, mostrato come "ndm- xxxx xxxx xxxx"
./ndm_password-generator.py -p ndm- -g 3

# Solo cifre, almeno STRONG
./ndm_password-generator.py -c digits -l strong

# Cinque password, solo le password (per script e pipe)
./ndm_password-generator.py -q -n 5

# Mostra la scala di robustezza completa e il riepilogo di sicurezza
./ndm_password-generator.py -v
```

> **Metti tra virgolette i caratteri speciali della shell** in `-s` e `-Y`, per
> esempio `-s '*'`. Un `*` senza virgolette viene espanso dalla shell, e un `;`
> senza virgolette divide il comando. Gli apici singoli sono la scelta più
> sicura.

## Come leggere l'output

| Riga | Significato |
|---|---|
| `Password` | La password vera. È quella da impostare sul servizio. |
| `Readable` | La stessa password divisa in gruppi (e il prefisso seguito dal separatore). Solo per leggerla e dettarla. |
| `Prefix` | Solo con `-p`: testo fisso, non conteggiato nell'entropia. |
| `Entropy` | Entropia in bit, con il dettaglio del calcolo. |
| `Strength` | Livello di robustezza dell'entropia (vedi sotto). |
| `Caution` / `Safety` | Se la password contiene caratteri che richiedono attenzione in shell, file di configurazione o URL. |

Quando imposti la password da qualche parte, **inserisci la riga `Password`,
senza separatori.** Se digitassi i separatori, diventerebbero parte della
password (spazi compresi).

## Entropia e livelli di robustezza

L'entropia è calcolata dal processo di generazione ed è esatta per una
generazione casuale uniforme:

```text
entropy = (length - symbols) * log2(alphanumeric alphabet size)
        + symbols * log2(symbol set size)
        + log2( C(length - 2, symbols) )        # where the symbols are placed
```

Senza simboli si riduce a `length * log2(alphabet size)`. Un prefisso fisso non
viene **mai** conteggiato.

### Classificazione

| Entropia (bit) | Robustezza |
|---|---|
| < 28 | `VERY WEAK` |
| da 28 a < 36 | `WEAK` |
| da 36 a < 60 | `FAIR` |
| da 60 a < 80 | `STRONG` |
| da 80 a < 100 | `VERY STRONG` |
| >= 100 | `EXCELLENT` |

Queste soglie sono una convenzione pratica, **non** uno standard ufficiale. Si
possono modificare nella tupla `RATINGS`.

### Risultati tipici (charset `mixed`, alfabeto di 57 caratteri, larghezza 4)

| Comando | Lunghezza | Entropia | Robustezza |
|---|---|---|---|
| `-l strong` | 12 | 70.0 bit | `STRONG` |
| *(predefinito)* / `-l very-strong` | 16 | 93.3 bit | `VERY STRONG` |
| `-l excellent` | 20 | 116.7 bit | `EXCELLENT` |
| `-y 2` | 16 | 91.3 bit | `VERY STRONG` |
| `-c digits` | 16 | 48.0 bit | `FAIR` |

### Come funziona `--level`

- Sceglie il **minor numero di gruppi interi** che raggiunge la fascia
  richiesta. Ogni gruppo ha sempre esattamente `--width` caratteri, mai un
  ultimo gruppo più corto.
- Il livello è quindi un **minimo**. Con alcune larghezze il risultato finisce
  in una fascia più alta, e su stderr compare una nota, per esempio:
  `note: whole groups of 4 give FAIR (requested WEAK)`.
- `very-weak`, `weak` e `fair` stampano un avviso su stderr
  (`level '...' is below STRONG`). Usali solo se sai perché.
- `--level` sostituisce `--groups`; usarli insieme è un errore.

## Simboli e caratteri a rischio

I simboli sono **disattivati per impostazione predefinita**. Con `-y N` lo
script aggiunge N simboli dall'insieme dei simboli, in posizioni interne
casuali (mai prima o ultima). L'insieme predefinito è `-_.`, scelto perché
questi caratteri sono "unreserved" negli URL (RFC 3986) e non rompono le
credenziali negli URL, i commenti nei file di configurazione, le virgolette
della shell o l'interpolazione INI. La tilde è esclusa di proposito perché è
difficile da digitare su molti layout di tastiera.

Nessun insieme di simboli è accettato ovunque. Se un sito rifiuta quello
predefinito, usa `-Y` con un altro e leggi gli avvisi.

Per ogni password generata lo script cerca i caratteri che notoriamente creano
problemi e spiega perché:

<details>
<summary>Caratteri segnalati come a rischio (clic per espandere)</summary>

| Carattere | Perché richiede attenzione |
|---|---|
| `!` | espansione della cronologia di bash (shell interattive) |
| `"` | rompe le virgolette doppie (shell, JSON, INI, YAML) |
| `'` | rompe gli apici singoli (shell, YAML, SQL) |
| `` ` `` | sostituzione di comando (shell) |
| `#` | inizio commento (shell, INI, env, YAML); frammento negli URL |
| `$` | espansione di variabili (shell, file env, file compose) |
| `%` | percent-encoding (URL); interpolazione INI; stringhe di formato |
| `&` | operatore di controllo (shell); separatore della query (URL) |
| `(` `)` | raggruppamento / subshell (shell) |
| `*` | glob (shell) |
| `?` | glob (shell); inizio della query (URL) |
| `[` `]` | glob (shell); intestazione di sezione (INI, TOML) |
| `{` `}` | brace expansion (shell); sintassi di template / flow (YAML, JSON) |
| `;` | separatore di comandi (shell); separatore di parametri (URL) |
| `<` `>` | redirezione (shell); markup (HTML, XML) |
| `\|` | pipe (shell) |
| `\` | carattere di escape (shell, JSON, INI, percorsi Windows) |
| `^` | carattere di escape (cmd di Windows); ancora nelle regex |
| `@` | separatore credenziali / host (URL) |
| `:` | separatore user:password e della porta (URL); key: value (YAML) |
| `/` | separatore di percorso (URL, filesystem) |
| `=` | delimitatore key=value (file env e INI, assegnazioni in shell) |
| `+` | indica uno spazio nelle query string degli URL |
| `,` | separatore di elenchi (CSV, molti formati di configurazione) |
| `~` | difficile da digitare su molti layout di tastiera |

</details>

Con `-q`, alla fine compare su stderr un solo avviso con l'elenco dei caratteri
trovati, così le pipe restano pulite. L'elenco **non è esaustivo**: "no known
risky characters" significa nessuno dei caratteri qui sopra, non una garanzia
per ogni contesto.

## Note di sicurezza

### Come funziona

- Ogni carattere è estratto con `secrets.choice`, che usa il CSPRNG del sistema
  operativo (`getrandom(2)` su Linux). **Non c'è seed né stato interno**:
  `SystemRandom.seed()` non ha effetto e `getstate()` non è implementato. Una
  password non può essere ricondotta a un seed.
- Lo script non legge l'ora, il nome host, file o la rete, quindi una password
  non contiene informazioni sulla macchina, sull'utente o sul momento in cui è
  stata creata.
- I caratteri sono scelti con il rejection sampling, quindi non c'è bias da
  modulo. Anche la regola "almeno una maiuscola, una minuscola e una cifra" usa
  il rejection sampling; la perdita di entropia è trascurabile per le lunghezze
  normali.
- Le posizioni dei simboli sono estratte in modo uniforme tra le posizioni
  interne.
- Verificato con `strace` su Linux (Python 3.13): nessuna system call di rete, e
  i byte casuali vengono da `getrandom`. Puoi ripetere il controllo:

  ```bash
  strace -f -qq -e trace=getrandom,socket,connect ./ndm_password-generator.py -q
  ```

- Durante lo sviluppo sono stati fatti controlli statistici (posizioni dei
  simboli uniformi, nessun simbolo agli estremi, tutte le classi di caratteri
  presenti). Sono controlli di buon senso, non una prova di casualità: la
  garanzia viene dal CSPRNG del sistema operativo.

## Servizi esterni contattati

**Nessuno.** Lo script non apre mai una connessione di rete: non cerca
aggiornamenti, non invia statistiche e non scarica niente. Puoi verificarlo con
il comando `strace` qui sopra.

## A cosa fare attenzione

1. **Usa la riga `Password`**, non `Readable`. I separatori servono solo alla
   visualizzazione.
2. **L'output può restare in giro.** Lo scrollback del terminale, i gestori
   degli appunti e i log di sessione (`tmux`, `script`) possono conservarlo.
   Preferisci `-q` inviato direttamente alla destinazione, e conserva le
   password in un password manager:

   ```bash
   ./ndm_password-generator.py -q | wl-copy
   ./ndm_password-generator.py -q | pass insert -e site/name
   ```

   Sono esempi; adattali alla tua piattaforma e ai tuoi strumenti.
3. **`-p`, `-s` e `-Y` sono visibili** nell'elenco dei processi
   (`/proc/PID/cmdline` è leggibile da tutti per impostazione predefinita) e
   nella cronologia della shell. Per come sono pensati non sono segreti:
   **non mettere mai segreti nel prefisso.** La password invece non compare mai
   nella riga di comando né nell'ambiente. Per tenere un comando fuori dalla
   cronologia di bash, usa `HISTCONTROL=ignorespace` e inizia il comando con
   uno spazio.
4. **Un prefisso non aggiunge entropia.** È testo fisso e indovinabile. La
   robustezza è calcolata solo sulla parte casuale.
5. **L'entropia vale solo per le password generate da questo script.** Se
   modifichi o scegli tu una password, il valore non vale più.
6. **Non riusare mai una password generata.** Una password per ogni servizio.
7. **Controlla le regole del sito.** Alcuni siti limitano la lunghezza,
   rifiutano alcuni simboli o ne richiedono di specifici. Nessun insieme di
   simboli è accettato ovunque.
8. **Verifica l'integrità dello script.** Se qualcuno sostituisse `secrets` con
   un `random` con seed, l'output sembrerebbe identico ma sarebbe prevedibile.
   Conserva un checksum o un tag git firmato, e limita i permessi di scrittura
   sul file (vedi [Controllo dello script](#controllo-dello-script)).
9. **VM, snapshot e cloni.** Il comportamento dipende dalle versioni del kernel
   e dell'hypervisor. Verifica che il tuo ambiente reinizializzi l'RNG del
   kernel dopo un ripristino o una clonazione.
10. **Un host compromesso vanifica qualsiasi password**: malware, keylogger,
    cattura dello schermo o una sorgente di entropia guasta.
11. **I livelli sono minimi.** Un livello può finire in una fascia più alta
    perché i gruppi sono sempre interi. Leggi la riga `Strength`.

## Controllo dello script

Lo script non si controlla da solo: uno script modificato potrebbe
semplicemente mentire. Confronta il suo SHA-256 con quello della stessa
versione pubblicata su GitHub (la versione la mostra `-V`):

```bash
./ndm_password-generator.py -V
sha256sum ndm_password-generator.py
curl -fsSL https://raw.githubusercontent.com/nidamanx/NDM_Password-Generator/vX.Y.Z/ndm_password-generator.py | sha256sum
```

I due hash devono essere identici. Su macOS usa `shasum -a 256` al posto di
`sha256sum`. In un clone, `git status` e
`git diff vX.Y.Z -- ndm_password-generator.py` mostrano eventuali modifiche
locali.

Leggi lo script prima di fidarti: è un solo file, e le parti che contano per la
sicurezza sono `_RNG = secrets.SystemRandom()` e `generate()`.

## Codici di uscita

| Codice | Significato |
|---|---|
| `0` | Successo |
| `2` | Errore di utilizzo (opzioni non valide o in conflitto) |
| `3` | Requisiti mancanti (versione di Python, modulo della libreria standard, sorgente casuale del sistema operativo) |

## Struttura del progetto

```text
NDM_Password-Generator/
├── ndm_password-generator.py   il programma (un solo file, solo libreria standard)
├── README.md                   questo file, in inglese
├── README.it.md                questo file
├── CHANGELOG.md                le modifiche di ogni versione
├── CITATION.cff                come citare questo software
├── SECURITY.md                 come segnalare una vulnerabilità
├── LICENSE                     GNU AGPL v3
└── .github/                    workflow della CI e Dependabot
```

## Versioni

Il progetto segue il [Semantic Versioning](https://semver.org/lang/it/). La
versione e la data di rilascio sono `__version__` e `__release_date__` nello
script (mostrate da `-V` e `-h`); ogni versione ha un tag `vX.Y.Z` e una voce in
[CHANGELOG.md](CHANGELOG.md). La CI controlla che la versione coincida nello
script, nei due README, in `CITATION.cff`, in `CHANGELOG.md` e nel tag.

## Controlli di qualità

Lo script è stato controllato con questi strumenti, tutti superati al momento
del rilascio (eseguiti con Python 3.13):

| Strumento | Impostazione | Risultato |
|---|---|---|
| `pycodestyle` | PEP 8, 79 colonne | pulito |
| `flake8` | predefinito + complessità <= 10 | 0 segnalazioni |
| `pylint` | predefinito | 10.00 / 10 |
| `mypy` | `--strict` | nessun problema |
| `ruff` | predefinito | pulito |
| `bandit` | analisi di sicurezza | 0 problemi |
| `vermin` | versione minima di Python | 3.8 |

Il file è anche analizzabile con la grammatica di Python 3.8. Non è stato
eseguito su un vero interprete 3.8.

## Avvertenze

> **Avvertenze di sicurezza**
>
> - Le password sono generate in locale con il CSPRNG del sistema operativo
>   (modulo `secrets`). Questo script non le salva, non le registra e non le
>   trasmette.
> - Il valore di entropia e il livello di robustezza descrivono il processo di
>   generazione (dimensione degli alfabeti più posizione dei simboli). Sono
>   indicativi, non una garanzia, e non valgono per password scelte o
>   modificate da te.
> - Un `--prefix` è testo fisso e indovinabile: non viene mai conteggiato
>   nell'entropia.
> - L'output può restare nello scrollback del terminale, nei log o nei gestori
>   degli appunti. Cancellalo, e conserva la password in un password manager.
> - Non riusare mai una password generata su più servizi.
> - Un host compromesso, una sorgente di entropia guasta o la cattura di
>   schermo o tastiera vanificano qualsiasi password.
> - Fornito "così com'è", **SENZA ALCUNA GARANZIA** (vedi la licenza AGPL-3.0).

## Licenza

Copyright (C) 2026 Nicola Davide Mannarelli - @nidamanx

Questo programma è software libero: puoi ridistribuirlo e/o modificarlo secondo
i termini della **GNU Affero General Public License** pubblicata dalla Free
Software Foundation, nella versione 3 della licenza o (a tua scelta) in una
versione successiva.

Questo programma è distribuito nella speranza che sia utile, ma **SENZA ALCUNA
GARANZIA**; senza neppure la garanzia implicita di COMMERCIABILITÀ o di
IDONEITÀ A UN PARTICOLARE SCOPO. Vedi la GNU Affero General Public License per
maggiori dettagli.

Dovresti aver ricevuto una copia della GNU Affero General Public License insieme
a questo programma. In caso contrario, vedi <https://www.gnu.org/licenses/>.

SPDX-License-Identifier: `AGPL-3.0-or-later`

> Questa è una traduzione di cortesia: fa fede il testo inglese della licenza
> (`LICENSE`). Se distribuisci lo script, includi una copia del testo completo:
> <https://www.gnu.org/licenses/agpl-3.0.txt>

## Autore

**Nicola Davide Mannarelli** - @nidamanx
