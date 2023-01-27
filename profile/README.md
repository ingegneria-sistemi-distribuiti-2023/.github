# Gamble platform with microservices

Student project that aims to create a set of microservices that simulate an online gaming system particularly focused on football bets. The system was implemented using Spring Boot v 3.0.0. The aim of the project was to implement best practices for the development and deployment of a product, following the practices introduced in the Distributed Systems Engineering course (LM-18, MSc. Data Science, University of Catania).

Some logic functions have not been implemented, since considered not necessary to reach the project's goal like: payment management, game validity check at the time of the place-bet, payment mechanis for winning bets, ticket status check once the matches are over and possible recharge on the account.

Each database and server will have a dedicated repository within this organization. The report, in Italian, is available [here](https://github.com/ingegneria-sistemi-distribuiti-2023/.github/blob/main/report.pdf).

## Microservices:

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


#### Application database `applicationdb:53306`

### Authentication service

Available at port `8082`

Private (require only `Secret-Key`)

- [POST]: `/auth/jwt/register`: create a user and return his `Bearer Token JWT`
- [POST]: `/auth/jwt/login`: return `Bearer Token JWT` if username and password match

Private (requires JWT):

- [GET]: `/auth/user/`: list of users
- [GET]: `/auth/user/{id}`: return user's detail (balance and transactions) given his `id`
- [POST]: `/auth/jwt/validate`:  validate a JWT token given a `username` and the `token` itself
- [POST]: `/auth/transaction/deposit`: allows to recharge a user's balance
- [POST]: `/auth/transaction/withdraw`: allows to withdraw from user's balance
- [POST]: `/auth/user/enable/{id}`: allows to enable a user account
- [POST]: `/auth/user/disable/{id}`: allows to disable a user account


#### Authentication database `authdb:23306`

### Game Service

Available at port `8081`

Private (can be requested only by Application Service):

- [GET]: `/game/match/`: returns the list of all playable matches
- [GET]: `/game/match/{id}`: returns the detail of a match given `id`
- [GET]: `/game/team/{id}`: returns the history of a team's matches given `id`
- [GET]: `/game/team/`: returns the list of all teams


Private (requires JWT):

- [POST]: `/game/team/`: create a team given a name


#### Game database `gamedb:13306`

### Session service

Available at port `8084`

Private (can be requested only by Application Service):

- [POST]: `/session/`: save a user's session (update and create)
- [GET]: `/session/{sessionId}`: returns a user's session given the session's `id`

#### Session database `sessiondb:43306`

## Used technologies and patterns

- Remote authenticator
- Redis (for session management)
- Remote facade
- Data Transfer Object
- Circuit breaker
- Retry Pattern
- Continuos Integration (GitHub)
- Docker & Docker Compose
