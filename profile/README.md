# Gamble platform with microservices

Il progetto proposto mira a realizzare un sistema per la gestione di una piattaforma di gioco on-line, con particolare focus sulle scommesse sportive, con gestione della sessione degli utenti. Inoltre, sarà in grado di gestire le quote per gli eventi, calcolare le vincite degli utenti e gestire il processo di pagamento delle vincite.

## Microservizi:

### Application service

Available at port `8080`

#### API:

Public:

- [POST]: `/app/public/register`: creazione di un utente
- [POST]: `/app/public/login`: login di un utente

- [GET]: `/app/public/match`: ritorna la lista di partite giocabili (TODO: al momento non sono filtrate)
- [GET]: `/app/public/match/{id}`: ritorna dettaglio della partita
- [GET]: `/app/public/team/{id}`: ritorna storico partite dato l'id della squadra 


Private (requires JWT / user role ):

- [POST]: `/app/gamble/add`: aggiunge una partita alla schedina (usa api gestite da sessione)
- [POST]: `/app/gamble/remove`: rimuove una partita alla schedina (usa api gestite da sessione)
- [POST]: `/app/gamble/place-bet`: effettua una richiesta per giocare la schedina salvata in sessione (usa api gestite da sessione)
- [POST]: `/app/user/deposit`: permette di ricaricare il conto di un utente

- With <b>Session-Service</b>

- [GET]: `/app/placedbet/{id}`: ritorna dettaglio schedina giocata
- [GET]: `/app/placedbet/user/{id}`: ritorna elenco schedine giocate da un utente


#### Unit tests
- Testare che un utente non possa creare più di un numero massimo di schedine salvate in sessione

### Authentication service

Available at port `8082`

Private (possono essere chiamate solo da Application Service):

- [POST]: `/auth/jwt/register`: crea un utente e ritorna la relativa jwt
- [POST]: `/auth/jwt/login`: ritorna un jwt se le credenziali sono corrette

Private (requires administrator role):

- [GET]: `/auth/user/`: ritorna la lista di un utente
- [GET]: `/auth/user/{id}`: ritorna il dettaglio di uno specifico utente con relativo balance e storico transazioni
- [POST]: `/auth/transaction/deposit`: permette di ricaricare il conto di un utente
- [POST]: `/auth/transaction/withdraw`: permette di prelevare i soldi dal conto di un utente (giocare schedina)

#### Unit tests

- Creazione di un utente
- Abilitazione/Disabilitazione di un utente
- Test case corretta lista di transazioni associate
- Creazione di utente con relativo login
- Test ricarica di un account

#### Authentication database `authdb:23306`

### Game Service

Available at port `8081`

Private (possono essere chiamate solo da Application Service):

- [GET]: `/match/`: ritorna la lista di partite giocabili
- [GET]: `/match/{id}`: ritorna dettaglio della partita
- [GET]: `/game/team/{id}`: ritorna storico partite dato l'ID della squadra

Private (requires JWT / administrator role):

- [POST]: `/game/team/`: aggiunge una team
- [POST]: `/game/team/disable`: disabilita un team


#### Unit tests

- Creazione di un team
- Lista partite giocabili
- Sanity check per generazione partite

#### Game database `gamedb:13306`

### Session service

Available at port `8084`

Private (possono essere chiamate solo da Application Service):

- [POST]: `/session/`: aggiorna la sessione di un utente (aggiornamento e creazione)
- [GET]: `/session/{sessionId}`: ritorna la sessione di un utente dato l'Id della sessione

#### Session database `sessiondb:43306`

## Argomenti trattati

- Remote authenticator (Role based)
- Redis per gestione della sessione
- Remote facade
- Data Transfer Object
- Circuit breaker (application-service)
- CI (GitHub)
- Docker
