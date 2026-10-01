# Esempio guidato 4 — Un sistema reale da analizzare: il registro elettronico d'istituto

Documento da analizzare in classe nella seconda lezione, in gruppi da due. Descrive un sistema che gli
studenti usano tutti i giorni, ricostruito a partire dal suo comportamento osservabile: gli indirizzi
che il browser chiama, i tempi di risposta, i momenti in cui una parte non funziona e le altre sì.

## Che cosa si osserva da fuori

Il registro elettronico si apre all'indirizzo `https://registro.esempio.invalid`. Dopo l'accesso con
nome utente e password, il browser scarica un'applicazione a pagina singola e da quel momento in poi
chiama una serie di indirizzi diversi.

```text
POST  /auth/login              → restituisce un token, risponde in ~200 ms
GET   /api/classi/5AI/alunni   → elenco degli alunni, ~80 ms
GET   /api/lezioni?data=...    → lezioni del giorno, ~90 ms
POST  /api/assenze             → registrazione di un'assenza, ~120 ms
GET   /api/voti?periodo=1      → voti del quadrimestre, ~150 ms
GET   /pagelle/5AI/2025.pdf    → documento PDF, ~4 s
POST  /comunicazioni/invia     → invio di una circolare alle famiglie, ~300 ms
GET   /statistiche/assenze     → cruscotto per la dirigenza, ~9 s
```

Nel corso dell'anno la segreteria ha segnalato tre fatti utili all'analisi. Il primo: in un pomeriggio
di gennaio il cruscotto delle statistiche era irraggiungibile per due ore, mentre gli insegnanti hanno
continuato a registrare assenze e voti senza accorgersi di nulla. Il secondo: quando vengono generate le
pagelle di tutte le classi, il registro resta reattivo ma la generazione di ogni singolo documento
richiede minuti. Il terzo: l'invio delle circolari alle famiglie, in un'occasione, è avvenuto due volte
per lo stesso destinatario.

## La consegna per i gruppi

Ogni gruppo produce una tabella con una riga per ciascuno degli otto indirizzi elencati sopra, e
tre colonne: il nome che dareste al componente, la classificazione come servizio distinto oppure come
parte interna di un altro componente, e la motivazione in una frase. La motivazione deve appoggiarsi a
un fatto osservabile — un tempo di risposta, un guasto isolato, un ritmo di cambiamento diverso — e non
a un'impressione.

Il gruppo scrive poi un breve paragrafo su due domande. La prima: quale dei tre fatti segnalati dalla
segreteria è la prova più forte che almeno un componente è davvero separato dagli altri, e perché. La
seconda: il doppio invio della stessa circolare è un difetto tipico della comunicazione fra servizi;
quale proprietà mancava all'operazione di invio perché una ripetizione della richiesta fosse innocua?

## Indizi per la discussione finale

Il guasto isolato di gennaio dice qualcosa che nessuno schema disegnato a tavolino potrebbe dire: due
parti che si fermano indipendentemente l'una dall'altra non girano nello stesso processo. È
l'osservazione più affidabile di tutte, perché riguarda ciò che il sistema fa, non ciò che la
documentazione dichiara.

Il tempo di risposta è un indizio più debole ma parla dei confini giusti. Nove secondi per il cruscotto
e ottanta millisecondi per l'elenco degli alunni indicano due profili di carico incompatibili: tenerli
nello stesso processo significa che le interrogazioni pesanti rallentano quelle leggere. È lo stesso
problema del modulo Report visto in `01_Schema_Monolite.md`.

La generazione delle pagelle è un caso diverso ancora: è un lavoro lungo che nessuno attende davanti allo
schermo. Un'operazione così non si tratta con una richiesta e una risposta, ma con un compito messo in
coda; chi lo ha chiesto riceve subito una conferma di presa in carico e recupera il documento più tardi.

Il doppio invio va ricondotto alla ripetizione di una richiesta di cui la risposta si è persa. Se
l'operazione fosse stata costruita in modo che ripeterla non produca un secondo effetto — per esempio
facendo accompagnare ogni richiesta di invio da un identificativo generato dal chiamante, ripetuto
identico nei soli tentativi di rinvio e scartato dal servizio se già visto — la ripetizione sarebbe stata
innocua, mentre un secondo invio volutamente disposto dalla segreteria, avendo un identificativo nuovo,
sarebbe passato. È un tema che il modulo M4 riprenderà parlando di idempotenza dei metodi HTTP.
