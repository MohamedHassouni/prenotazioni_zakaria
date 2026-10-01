# Esempio guidato 3 — Il progetto annuale: Prenotazione delle aule e dei laboratori

Documento di riferimento del corso, presentato nella seconda lezione del modulo M0 e ripreso all'inizio
di ogni modulo successivo. Descrive che cosa il sistema deve fare e quale forma avrà quando sarà finito.

## Il problema

L'istituto dispone di aule ordinarie e di laboratori con dotazioni diverse. I docenti prenotano
un'aula per una fascia oraria di una giornata indicando il motivo della prenotazione. Oggi la
prenotazione avviene con un foglio appeso in sala docenti: due persone possono scrivere nella stessa
casella, nessuno sa in anticipo quali laboratori sono liberi, e a fine anno non esiste alcun dato sul
grado di utilizzo delle aule.

Il sistema da costruire sostituisce il foglio. Deve mostrare la disponibilità, impedire la doppia
prenotazione della stessa aula nella stessa fascia, conservare le prenotazioni in modo che sopravvivano
al riavvio del sistema e permettere soltanto a chi è autorizzato di modificare o cancellare una
prenotazione altrui.

## Le entità del dominio

L'**aula** ha un codice che la identifica, per esempio `LAB3` o `A21`, una descrizione, una capienza in
posti e l'indicazione se si tratta di un laboratorio o di un'aula ordinaria.

Il **docente** ha un nome, un cognome e l'indirizzo di posta d'istituto, che è anche l'identificativo
con cui accede al sistema.

La **prenotazione** collega un docente a un'aula in una data, con un'ora di inizio, un'ora di fine e un
motivo. Una prenotazione è valida se l'aula esiste, se l'ora di fine è successiva all'ora di inizio e se
nell'intervallo richiesto non esiste già un'altra prenotazione per la stessa aula.

Nessun dato personale reale entra nel sistema. I docenti di prova hanno indirizzi nella forma
`m.rossi@marconirovereto.it`, cioè il dominio d'istituto, che è la forma usata dai dati di esempio di
tutto il corso. I cognomi `Rossi` e `Bianchi` sono convenzionali, come `Mario Rossi` sui moduli
prestampati, e non si riferiscono ad alcuna persona reale: se un cognome coincide con quello di un
docente dell'istituto, resta un dato inventato e va trattato come tale.

## L'architettura di arrivo

```text
                          BROWSER
                             │
                             │  HTTP sulla porta 8080
                             ▼
   ┌──────────────────────────────────────────────────────────┐
   │  servizio  web                                            │
   │  nginx che serve la pagina statica e inoltra              │
   │  le richieste che cominciano con /api verso il servizio   │
   └────────────────────────────┬─────────────────────────────┘
                                │  rete interna, porta 3000
                                ▼
   ┌──────────────────────────────────────────────────────────┐
   │  servizio  api                                            │
   │  Node 22 ed Express 5 in TypeScript                       │
   │  espone le operazioni sulle prenotazioni e verifica       │
   │  il token e il ruolo di chi le richiede                   │
   └────────────────────────────┬─────────────────────────────┘
                                │  rete interna, porta 5432
                                ▼
   ┌──────────────────────────────────────────────────────────┐
   │  servizio  db                                             │
   │  PostgreSQL 16 con volume persistente                     │
   │  nessuna porta pubblicata verso l'esterno                 │
   └──────────────────────────────────────────────────────────┘
```

Tre proprietà di questo schema vanno fissate subito, perché guidano tutte le scelte dell'anno. Il
browser non conosce l'indirizzo del servizio `api`: chiama sempre `web`, che inoltra. Il servizio `db`
non pubblica alcuna porta verso la macchina che ospita il sistema, quindi è raggiungibile soltanto dai
container che stanno sulla sua stessa rete interna. L'unico componente che possiede i dati è `api`, e
nessun altro apre una connessione al database.

## Che cosa aggiunge ciascun modulo

| Modulo | Contributo al progetto |
|---|---|
| M0 | Il repository, la struttura delle cartelle e il documento di architettura |
| M1 | I primi container avviati da immagini pronte, con la porta pubblicata verso l'host |
| M2 | La rete dedicata su cui i container si chiamano per nome e il volume che conserva i dati |
| M3 | L'immagine del servizio `web` costruita da un Dockerfile e il primo `docker-compose.yml` |
| M4 | Il contratto dell'API delle prenotazioni in `docs/api.md`, e come interfacce TypeScript |
| M5 | Il servizio `api` che risponde alle operazioni CRUD con i dati tenuti in memoria |
| M6 | La persistenza reale su PostgreSQL attraverso l'ORM |
| M7 | Il token e i ruoli che proteggono le operazioni di scrittura |
| M8 | Lo stack completo avviabile con un comando e la pagina che consuma l'API |

Il repository cresce e non viene mai ricostruito da zero: ciò che è stato consegnato in un modulo resta
e viene ampliato dal successivo.

## Che cosa il progetto non farà

Non gestisce l'orario scolastico né le sostituzioni. Non invia messaggi di posta. Non produce statistiche
per la dirigenza. Non è multi-istituto. Questi confini vanno dichiarati adesso perché un progetto
didattico che cresce senza limiti non si chiude entro aprile, e perché saper dire che cosa un sistema non
fa è parte della progettazione quanto saper dire che cosa fa.
