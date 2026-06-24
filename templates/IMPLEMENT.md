# IMPLEMENT — <task name>

> The running ledger. Update **every session**. A resuming agent reads `PLAN.md` then this,
> verifies the last gate still passes, then continues. Keep progress anchored in **verifiable**
> units (commits, passing tests) — never trust a prose summary that may have drifted.

## Status
- **Current milestone:** <M#>
- **Last verified gate:** <e.g. "all tests green @ commit abc1234"> on <date>
- **Next action:** <the single next concrete step>

## Done (verifiable)
- [x] <thing> — `<commit / test / artifact>`
- [x] <thing> — `<commit / test / artifact>`

## In progress
- [ ] <thing> — <where it stands, what's blocking>

## Next up
- [ ] <thing>
- [ ] <thing>

## Decisions log
| Date | Decision | Rationale |
|------|----------|-----------|
| <date> | <what was decided> | <one line> |

## Open issues / risks
- <issue> — <mitigation or who/what to check>

## Resume checklist (read on wake / new session)
- [ ] Read `PLAN.md` — re-anchor to goal + acceptance criteria
- [ ] Read this ledger — current milestone, next action
- [ ] Verify last gate still passes before continuing
- [ ] Confirm no out-of-scope drift since last session
