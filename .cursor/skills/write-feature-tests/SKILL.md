---
name: write-feature-tests
description: Adds automated test cases for new or changed product behavior. Use when implementing a feature, user story, bug fix with behavior change, API endpoint, Android screen, domain rule, Gateway route, or tracker issue.
icon: beaker
color: green
---

# Write tests with every feature

A feature implementation is not done until automated tests cover the new behavior and have been run.

Read this skill **before** product code. Prefer test that fails → implement → green.

## When to use

Use when the change:

- Implements a tracker issue (US / Task / Feature) in `TraceSuite/TraceSuite`
- Adds or changes an endpoint, DTO, domain rule, Android screen/ViewModel, or Gateway route
- Changes status codes, list filters, or gates (trial, freeze, plaza cubierta)

Skip new tests only for README/comments, Dockerfile path renames with no runtime change, or version bumps. If the user or another service can observe a difference, write tests.

## Workflow

Copy and track:

```
- [ ] Map each acceptance criterion on the issue to at least one test
- [ ] Add a rejection/edge test for each "no puedo / si falta X" rule
- [ ] Name tests after product behavior, not internals
- [ ] Run the repo test command; fix failures
- [ ] List new/changed tests in the PR
```

Names:

- Good: `Musician_cannot_apply_when_plaza_is_covered`
- Bad: `Test1`, `ApplyAsync_works`

## Which test to write

Match the layer that changed. Follow existing files in the repo.

| Layer | Test | Pattern in this org |
| --- | --- | --- |
| Domain invariant | xUnit, no HTTP | `EventosBoardDomainTests` |
| HTTP JSON / status | `WebApplicationFactory` + Postgres if the repo already uses it | `EventLoopHttpTests`, camelCase JSON, JWT is the actor (no client `musicianId`) |
| Android UI / VM / DTO | JUnit in `app/src/test` | board, ficha, Gateway 403/409 |
| Gateway routes | Ocelot JSON fixture and `/health` if present | event/apply routes; no pharmacy/patient |
| Worker / EmailWorker | job, template, or `/health` | do not reintroduce Core/AMEFA |

If the repo has no test project, create one with the same stack (xUnit + `Microsoft.AspNetCore.Mvc.Testing` on .NET APIs; JUnit on Android). Do not ship the feature without a harness.

## Product contracts to assert

- Entity name is **evento** (not a separate Show type).
- **Cachet** is MXN on the plaza, display-only. Not Stripe, not `payment_intent`, no "pagar al músico" in MVP. Assert the amount on the ficha; do not simulate in-app payout.
- Musician board: in progress + upcoming (`now < EndsAt`); not draft or ended.
- Freeze is clock at `StartsAt`, not only `EventStatus.Frozen`.
- Covered plaza: Accepted or Enrolled + `IsActive` → `isFilled` / `isCovered`; apply → 409.
- Trial: 403 plan copy; the client does not send the trial count.

## Run

- .NET: `dotnet test`. If `TraceSuite.EventBus` cannot restore from GitHub Packages, use a local nupkg at the csproj version.
- Android: `./gradlew test`. `assembleDebug` is not a substitute. If SDK/`google-services.json` is missing, run JVM unit tests that do not need them and say what did not run.
- Do not mark the feature done if tests were not run or are failing.

## Additional resources

- Stacks and commands: [reference.md](reference.md)
