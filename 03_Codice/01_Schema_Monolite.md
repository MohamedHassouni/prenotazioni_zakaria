# Esempio guidato 1 — Il sistema di prenotazione come monolite

Documento da proiettare e annotare durante la prima lezione. Descrive il sistema di prenotazione delle
aule e dei laboratori nella forma che avrebbe se fosse scritto come un'unica applicazione.

## Lo schema

```text
                           BROWSER DEL DOCENTE
                                   │
                                   │  HTTP
                                   ▼
        ┌──────────────────────────────────────────────────────┐
        │      prenotazioni.jar  —  UN SOLO PROCESSO           │
        │                                                      │
        │   ┌────────────┐  ┌────────────┐  ┌───────────────┐  │
        │   │  Utenti    │  │   Aule     │  │  Prenotazioni │  │
        │   └────────────┘  └────────────┘  └───────────────┘  │
        │   ┌────────────┐  ┌────────────┐                     │
        │   │  Report    │  │ Notifiche  │                     │
        │   └────────────┘  └────────────┘                     │
        │                                                      │
        │   chiamate di metodo dirette fra i cinque moduli      │
        └──────────────────────────┬───────────────────────────┘
                                   │  JDBC
                                   ▼
        ┌──────────────────────────────────────────────────────┐
        │  DATABASE UNICO                                       │
        │  utenti · aule · prenotazioni · log_notifiche         │
        └──────────────────────────────────────────────────────┘
```

## Che cosa fa ciascun modulo

Il modulo **Utenti** conserva l'anagrafica dei docenti e verifica le credenziali di accesso. Il modulo
**Aule** conserva codice, capienza e dotazioni di aule e laboratori. Il modulo **Prenotazioni** contiene
la regola centrale del sistema: una prenotazione è valida solo se l'aula esiste, se il docente è
abilitato e se nell'intervallo richiesto non ce n'è già un'altra. Il modulo **Report** produce il
riepilogo mensile di occupazione per la dirigenza. Il modulo **Notifiche** invia il messaggio di
conferma al docente e registra l'invio.

Tutti e cinque i moduli sono classi dello stesso progetto, compilate insieme, avviate insieme e
distribuite come un unico archivio. Quando il modulo Prenotazioni ha bisogno della capienza di un'aula
chiama un metodo di `AuleService`, e la chiamata non lascia mai il processo.

## Domande da discutere in classe

La prima domanda riguarda il rilascio. Il preside chiede di aggiungere una colonna al report mensile: è
una modifica di dieci righe nel modulo Report. Quante parti del sistema devono essere fermate,
ricompilate e riavviate perché quella modifica arrivi in produzione? E che cosa succede alle
prenotazioni in corso durante il riavvio?

La seconda riguarda la scala. Il primo giorno di scuola il report mensile viene calcolato da trenta
persone contemporaneamente e la CPU della macchina si satura, tanto che le prenotazioni diventano
lente. Con questa architettura, l'unico rimedio è replicare l'intero programma: che cosa viene
replicato inutilmente?

La terza riguarda i confini. Il modulo Notifiche, per costruire il testo del messaggio, legge
direttamente la tabella `prenotazioni` invece di chiedere i dati al modulo Prenotazioni. Il codice
funziona ed è più corto. Che cosa succede il giorno in cui la colonna `motivo` viene rinominata?

La quarta riguarda i pregi, e va posta con la stessa serietà. Una prenotazione che viene inserita
mentre si aggiorna il conteggio delle occupazioni dell'aula è una sola transazione del database: o
riesce tutto o non resta traccia di nulla. Che cosa serve per ottenere lo stesso risultato quando i due
moduli sono due servizi separati su due macchine diverse?

## Dimostrazione facoltativa del docente

Se il tempo lo consente, il docente può mostrare dalla cattedra l'avvio di un container di `nginx:alpine`
e la pagina che risponde su `http://localhost:8080`, per rendere visibile la rapidità dell'avvio rispetto
all'accensione di una macchina virtuale. È una dimostrazione proiettata, non un'esercitazione: gli
studenti osservano e non eseguono nulla. I comandi di avvio e di pulizia, l'immagine necessaria e le due
alternative al recupero da Docker Hub sono nel `README.md` di questa cartella. Docker entra in modo
operativo dal modulo M1.
