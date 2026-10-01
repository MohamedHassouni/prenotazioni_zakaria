# Materiale guidato del modulo M0

Il modulo M0 è di inquadramento e non prevede esecuzione di container: Docker entra in modo operativo
dal modulo M1. Questa cartella non contiene quindi programmi da eseguire, ma i documenti di analisi che
il docente costruisce e commenta con la classe durante la seconda parte di ciascuna lezione. Ogni file
si legge e si annota; il prodotto dell'attività è un documento scritto dallo studente, non un comando
lanciato.

Nessun file di questa cartella richiede software oltre a Visual Studio Code, Git e un browser. Nessuna
immagine va recuperata da Docker Hub per il lavoro degli studenti.

La sola eccezione è la dimostrazione facoltativa descritta in `01_Schema_Monolite.md`, proiettata dalla
cattedra e mai replicata dalle ventiquattro postazioni. Richiede l'immagine `nginx:alpine`, occupa la
porta 8080 della postazione del docente e si esegue così:

```powershell
docker container run -d --name dimostrazione-m0 -p 8080:80 nginx:alpine
# si apre http://localhost:8080 nel browser, poi si ripulisce:
docker container stop dimostrazione-m0
docker container rm dimostrazione-m0
```

L'immagine va scaricata in anticipo sulla postazione del docente. In alternativa si recupera dal registro
locale del server d'istituto, `registry.marconi.lan:5000/nginx:alpine`, oppure si carica dall'archivio
`\\server.marconi.lan\didattica\tpsit5\immagini\`. I due indirizzi sono segnaposto: vanno sostituiti con
quelli effettivi prima della lezione.

## Ordine d'uso

| File | Lezione | Descrizione |
|---|---|---|
| `01_Schema_Monolite.md` | 1 | Il sistema di prenotazione disegnato come monolite, con l'analisi dei suoi limiti |
| `02_Schema_Servizi.md` | 1 | Lo stesso sistema scomposto in servizi, con i confini e i costi introdotti |
| `03_Progetto_Annuale.md` | 2 | Descrizione del progetto "Prenotazione delle aule e dei laboratori" e architettura di arrivo |
| `04_Sistema_Da_Analizzare.md` | 2 | Un sistema reale (il registro elettronico d'istituto) da analizzare in classe |
| `05_Struttura_Repository.md` | 2 | Struttura del repository, procedura di creazione e convenzione di consegna |

## Come si usano a lezione

**Prima lezione.** Dopo la spiegazione, il docente proietta `01_Schema_Monolite.md` e chiede alla classe
di individuare quali modifiche al sistema obbligano a ridistribuire l'intera applicazione. Si passa poi
a `02_Schema_Servizi.md`, che mostra la stessa funzionalità scomposta: l'esercizio guidato consiste nel
tracciare sul foglio i confini fra i servizi e nel dire, per ciascuna freccia dello schema, che cosa
succede se quella comunicazione fallisce. L'esercizio autonomo analogo è l'esercizio 1 di
`04_Esercizi.md`.

**Seconda lezione.** Si legge `03_Progetto_Annuale.md` e si colloca ogni modulo dell'anno sullo schema
di arrivo. Si analizza poi `04_Sistema_Da_Analizzare.md` in gruppi da due, con la consegna di
classificare ciascun componente come servizio distinto o come parte interna di un altro. La lezione si
chiude con `05_Struttura_Repository.md`, che è l'unica parte operativa del modulo: ogni studente crea il
repository, vi carica la struttura di cartelle e l'ossatura del documento di architettura, e verifica che
su GitHub compaiano tutte le cartelle previste. Questo è il risultato verificabile della lezione.

## Che cosa resta al termine del modulo

Il repository `prenotazioni` esistente su GitHub, privato, con il docente fra i collaboratori, clonato in
`C:\tpsit\prenotazioni`, con le cartelle `web/`, `api/`, `db/`, `docs/` e `docs/ai/` e con l'ossatura del
documento di architettura in `docs/architettura.md`, che l'esercizio 7 riempie a casa. Il modulo M1 parte
da questo repository e non da una cartella nuova.
