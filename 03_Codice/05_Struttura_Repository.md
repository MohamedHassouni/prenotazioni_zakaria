# Esempio guidato 5 — Creazione del repository di lavoro

Parte operativa della seconda lezione. Gli studenti conoscono già Git e GitHub: questa non è una
spiegazione di Git, è la procedura con cui si fissano il nome, la struttura e le convenzioni che
resteranno valide fino ad aprile. Al termine, ogni studente ha su GitHub un repository che il docente
può aprire.

## Struttura definitiva del repository

```text
prenotazioni/
├── web/                  # pagina statica HTML+CSS+JS servita da nginx (da M2)
│   └── .gitkeep
├── api/                  # servizio Node ed Express in TypeScript, sotto src/ (da M5)
│   └── .gitkeep
├── db/                   # script di inizializzazione di PostgreSQL, sotto init/ (da M6)
│   └── .gitkeep
├── docs/                 # architettura.md (M0) e api.md, contratto dell'API (da M4)
│   ├── ai/               # elaborati sull'uso degli strumenti di AI
│   │   └── .gitkeep
│   └── .gitkeep
├── .gitignore
└── README.md
```

Tutte e cinque le cartelle nascono vuote: Git versiona i file e non le cartelle, quindi ciascuna contiene
un file vuoto `.gitkeep`, che serve soltanto a farla esistere nel repository. Vale anche per `docs/`, che
al termine di questa lezione contiene solo l'ossatura del documento di architettura.

Ciò che comparirà più avanti va nominato adesso, perché la struttura non cambierà: nella cartella
principale il `docker-compose.yml` e il file `.env.example` in M3; in `docs/` il contratto dell'API come
`api.md` in M4; dentro `api/` il codice sotto `src/`, con le sottocartelle `modelli/`, `rotte/` e
`middleware/`, a partire da M5; dentro `db/` gli script sotto `init/` a partire da M6. In M0 non si crea
nessuna di queste cose.

## Procedura

1. Su GitHub, con l'account d'istituto, creare un repository di nome `prenotazioni`, privato, spuntando
   l'aggiunta del file `README.md` iniziale e scegliendo il modello di `.gitignore` per Node.
2. In `Settings` → `Collaborators`, aggiungere l'utenza GitHub del docente.
3. Sulla postazione, aprire PowerShell e clonare il repository in `C:\tpsit`, che è il percorso di lavoro
   del corso, sostituendo `<utente>` con il proprio nome utente GitHub:

   ```powershell
   New-Item -ItemType Directory -Path C:\tpsit -Force
   Set-Location C:\tpsit
   git clone https://github.com/<utente>/prenotazioni.git
   Set-Location C:\tpsit\prenotazioni
   ```

4. Creare le cartelle e i file segnaposto:

   ```powershell
   New-Item -ItemType Directory -Path web, api, db, docs, docs\ai
   New-Item -ItemType File -Path web\.gitkeep, api\.gitkeep, db\.gitkeep, docs\.gitkeep, docs\ai\.gitkeep
   ```

5. Creare il ramo del modulo:

   ```powershell
   git switch -c m0
   ```

6. Creare in Visual Studio Code il file `docs\architettura.md` con la sola ossatura: il titolo del
   progetto e i cinque titoli di sezione elencati più sotto, ciascuno vuoto. Il contenuto è il lavoro
   autonomo dell'esercizio 7 di `04_Esercizi.md` e non si scrive adesso.
7. Registrare e inviare il lavoro:

   ```powershell
   git add .
   git commit -m "M0: struttura del repository e ossatura del documento di architettura"
   git push -u origin m0
   ```

8. Verificare su GitHub che il ramo `m0` esista e che le cartelle `web/`, `api/`, `db/`, `docs/` e
   `docs/ai/` siano tutte visibili. Se una cartella non compare, manca il suo `.gitkeep`.

Questo è il risultato verificabile della lezione: la pagina del repository su GitHub mostra le cinque
cartelle e l'ossatura del documento di architettura.

## Ossatura del documento `docs/architettura.md`

I cinque titoli di sezione da creare in laboratorio, e che l'esercizio 7 riempirà a casa, sono: il
problema che il sistema risolve; le entità del dominio; l'architettura di arrivo; la responsabilità sui
dati di ciascun servizio; ciò che il sistema non farà. Il documento finito è di circa una pagina e chiude
con la dichiarazione d'uso degli strumenti di intelligenza artificiale nella forma prevista da
`05_Scheda_AI.md`.

## Convenzione di consegna, valida per tutto l'anno

Si lavora su un ramo per modulo, chiamato `m0`, `m1`, `m2` e così via. Al termine del modulo il ramo
viene unito a `main` e si applica un'etichetta con un nome diverso da quello del ramo:

```powershell
git switch main
git merge m0
git tag consegna-m0
git push origin main --tags
```

L'etichetta si chiama `consegna-m0` e non `m0` perché Git accetta un ramo e un'etichetta omonimi, ma da
quel momento ogni comando che li nomini stampa `warning: refname 'm0' is ambiguous.` e il collegamento
consegnato non direbbe più se punta al ramo o all'etichetta.

Su Google Classroom si consegna il collegamento al commit etichettato, nella forma
`https://github.com/<utente>/prenotazioni/tree/consegna-m0`, non i file. Ogni consegna contiene la
dichiarazione d'uso degli strumenti di intelligenza artificiale.

## Se la rete o GitHub non sono disponibili

Il lavoro si svolge in un repository locale creato con `git init`, completando la stessa struttura di
cartelle e gli stessi commit; l'origine remota si aggiunge alla lezione successiva con
`git remote add origin <indirizzo>` seguito da `git push -u origin m0`. In alternativa, l'intera attività
si può svolgere in un Codespace aperto sul repository, con gli stessi comandi eseguiti nel terminale
integrato, tenendo presente che lì la shell è `bash` e i percorsi usano la barra normale.
