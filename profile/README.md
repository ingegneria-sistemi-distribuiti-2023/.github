# Gamble platform with microservices

Student project that aims to create a set of microservices that simulate an online gaming system particularly focused on football bets. The system was implemented using Spring Boot v 3.0.0. The aim of the project was to implement best practices for the development and deployment of a product, following the practices introduced in the Distributed Systems Engineering course (LM-18, MSc. Data Science, University of Catania).

Some logic functions have not been implemented, since considered not necessary to reach the project's goal like: payment management, game validity check at the time of the place-bet, payment acquired, ticket status check once the matches are over and possible top-up on the account.

Each database and server will have a dedicated repository within this organization. The report, in Italian, is available [here](https://duckduckgo.com).

## Microservizi:

### Application service

Available at port `8080`

#### API:

Public:

- [POST]: `/app/public/register`: create a user
- [POST]: `/app/public/login`: login, return a jwt if authenticated
- [GET]: `/app/public/match`: list of available matches
- [GET]: `/app/public/match/{id}`: detail of a match, given `id`
- [GET]: `/app/public/team/{id}`: history of a team's matches, given team's `id`


Private (requires JWT ):

- [POST]: `/app/gamble/add`: add a match to an existing bet or create an empty one (call session-service using `RestTemplate`)
- [POST]: `/app/gamble/remove`: remove a match given a `betId` (call session-service using `RestTemplate`)
- [POST]: `/app/gamble/place-bet`: make a request to an existing placing bet, if user has not sufficient balance returns an error (call session-service, authentication service api using `RestTemplate`)
- [POST]: `/app/user/deposit`: recharge a user's balance
- [GET]: `/app/placedbet/{id}`: return bet's detail given `id`
- [GET]: `/app/placedbet/user/{id}`: list of all bet played by user given `id`


#### Unit tests
TODO (?)

### Authentication service

Available at port `8082`

Private (require only `Secret-Key`)

- [POST]: `/auth/jwt/register`: create a user and return his `Bearer Token JWT`
- [POST]: `/auth/jwt/login`: return `Bearer Token JWT` if username and password match

Private (requires administrator role):

- [GET]: `/auth/user/`: list of users
- [GET]: `/auth/user/{id}`: return user's detail (balance and transactions) given his `id`
- [POST]: `/auth/transaction/deposit`: allows to recharge a user's balance 
- [POST]: `/auth/transaction/withdraw`: allows to withdraw from user's balance

#### Unit tests

TODO (?)

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
