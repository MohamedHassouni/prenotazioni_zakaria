# Esempio guidato 2 — Lo stesso sistema scomposto in servizi

Documento da proiettare e annotare durante la prima lezione, subito dopo `01_Schema_Monolite.md`. La
funzionalità è identica; cambia il modo in cui il sistema viene rilasciato ed eseguito.

## Lo schema

```text
                           BROWSER DEL DOCENTE
                                   │
                                   │  HTTP
                                   ▼
        ┌──────────────────────────────────────────────────────┐
        │  PUNTO DI INGRESSO (proxy inverso)                    │
        └───┬──────────────┬───────────────┬───────────────────┘
            │              │               │
            │ HTTP         │ HTTP          │ HTTP
            ▼              ▼               ▼
     ┌────────────┐  ┌────────────┐  ┌───────────────┐
     │  servizio  │  │  servizio  │  │   servizio    │
     │   UTENTI   │  │    AULE    │  │ PRENOTAZIONI  │
     └─────┬──────┘  └─────┬──────┘  └───────┬───────┘
           │               │                 │
           ▼               ▼                 ▼
     ┌────────────┐  ┌────────────┐  ┌───────────────┐
     │  db utenti │  │  db aule   │  │ db prenotaz.  │
     └────────────┘  └────────────┘  └───────────────┘

     Il servizio PRENOTAZIONI, per validare una richiesta,
     chiama AULE su HTTP:  GET /aule/LAB3
     e non apre mai una connessione al database delle aule.

     ┌───────────────┐        messaggio asincrono
     │ PRENOTAZIONI  │ ─────────────────────────────▶ ┌───────────────┐
     └───────────────┘                                │   NOTIFICHE   │
                                                      └───────────────┘
```

## Perché i confini cadono in questi punti

L'anagrafica delle aule cambia una volta all'anno, quando si aggiorna la dotazione di un laboratorio.
Le prenotazioni cambiano ogni ora di ogni giorno. Due ritmi di cambiamento così diversi sono il segnale
più affidabile che ci si trova davanti a due responsabilità distinte, e quindi a due candidati servizi.

L'identità dei docenti è usata da tutti gli altri servizi ma non appartiene a nessuno di essi: è un
servizio a sé, ed è anche quello con i requisiti di sicurezza più stringenti, perché custodisce le
password. Isolarlo significa poter applicare regole più severe solo dove servono.

Le notifiche sono l'unico componente che può fallire senza che l'operazione principale fallisca: se il
messaggio di conferma non parte, la prenotazione resta comunque valida. Questa asimmetria giustifica una
comunicazione asincrona, cioè un messaggio depositato in coda anziché una chiamata attesa.

I report, che nello schema non compaiono come servizio, sono un caso intermedio da discutere: leggono
dati di più servizi e non ne possiedono di propri. Chi li disegna come servizio autonomo deve poi
spiegare da dove prende i dati senza violare la regola sul possesso delle tabelle.

## Che cosa si è appena pagato

Ogni freccia dello schema è una richiesta di rete, e ogni richiesta di rete è un punto in cui il sistema
può guastarsi. La domanda da porre per ciascuna freccia è sempre la stessa: che cosa deve fare il
chiamante se la risposta non arriva, se arriva dopo cinque secondi, se arriva con un errore, se la stessa
richiesta viene inviata due volte perché la prima risposta si è persa.

La transazione unica del monolite non esiste più. Se la prenotazione viene salvata ma la chiamata al
servizio Aule per aggiornare il contatore di utilizzo fallisce, il sistema resta in uno stato incoerente
e qualcuno deve rimediare. Non è un difetto dello schema: è il prezzo dichiarato dell'indipendenza.

La diagnosi cambia natura. Un docente segnala che la prenotazione non è andata a buon fine: il registro
degli errori da leggere non è più uno, sono quattro, e per capire l'ordine dei fatti occorre che le
richieste portino con sé un identificativo che le leghi fra loro.

## Domande da discutere in classe

Se il servizio Aule non risponde, il servizio Prenotazioni deve rifiutare la richiesta o accettarla e
verificare più tardi? La risposta dipende dal requisito, non dalla tecnologia: che cosa è peggio, una
prenotazione rifiutata a torto o una prenotazione accettata su un'aula che non esiste più?

Quanti servizi servono davvero per un istituto con novanta docenti e quaranta aule? Lo schema che state
guardando è quello di un sistema più grande di quello che vi serve. Quali di questi confini terreste, e
quali unireste, sapendo che ogni servizio in più è un elemento in più da configurare, avviare e
sorvegliare?

## Il ponte verso il progetto dell'anno

Il progetto che costruirete adotta una versione deliberatamente ridotta di questo schema: una pagina
statica, un solo servizio applicativo che espone le prenotazioni e un solo database. È la scelta giusta
per la dimensione del problema, ed è anche la scelta che permette di vedere ogni pezzo con chiarezza.
Lo schema completo resta il riferimento per capire dove si andrebbe se il sistema crescesse.
