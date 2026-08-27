# Test stacks (RedTarima / TraceSuite)

## Appointments.API

- Project: `tests/TraceSuite.Appointments.API.Tests`
- Runner: `dotnet test`
- Domain: `EventosBoardDomainTests`, `EventBoardLoopTests`
- HTTP: `EventLoopHttpTests` + `AppointmentsApiFactory` + `[Collection("Postgres")]`
- JSON: camelCase; JWT from `TestJwt`

## Android

- JVM unit tests under `app/src/test`
- Runner: `./gradlew test`
- Examples: Eventos board, DTO parsing, Gateway error copy

## Gateway / Auth / Payment / Worker / EmailWorker

- Prefer the existing test project. If none exists, add xUnit (+ `WebApplicationFactory` for HTTP hosts).
- Health contract is `/health` (not `/alive`, except Gateway which serves both).
