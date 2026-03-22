# [Mission Title]

> **Agent instruction:** This is a long-mission task file.
> Load the `long-missions` skill (`~/.copilot/skills/long-missions/SKILL.md`) before proceeding.
> If europa is involved, also load the `europa` skill (`~/.copilot/skills/europa/SKILL.md`).
> Follow the checkpoint format, guardrails, and status-file protocol defined in those skills exactly.

**Mission ID:** MISSION_YYYYMMDD_NAME
**Framework:** long-missions v1
**Start time:** [HH:MM UK]
**Hard stop:** [HH:MM UK]
**Total budget:** [N hours]

---

## Objective

[1-3 sentences: what does success look like when you return?]

---

## Resource Budget

| Resource        | Limit                       | Abort if            |
|-----------------|-----------------------------|---------------------|
| Europa disk     | ≥ 1 GB free required        | < 200 MB free       |
| Time per phase  | See phases below            | > 2× estimated time |
| Max retries     | 2 per phase                 | 3rd failure = skip  |
| Max total time  | [N min]                     | Exceeded → stop now |
| Network (VPN)   | Must be active              | 3+ min disconnected |

---

## Phases

### Phase 1: [Name] (estimated [N] min)
- **Objective:** [What success looks like]
- **Inputs:** [Files/data needed]
- **Outputs:** [Files produced]
- **If BLOCKED:** [What to do if this fails]
- **If SLOW (>2× time):** [Skip or simplify to: ...]

### Phase 2: [Name] (estimated [N] min)
- **Objective:**
- **Inputs:**
- **Outputs:**
- **If BLOCKED:**
- **If SLOW:**

### Phase 3: [Name] (estimated [N] min)
- **Objective:**
- **Inputs:**
- **Outputs:**
- **If BLOCKED:**
- **If SLOW:**

[Add more phases as needed]

---

## Fallback Plan

[Describe: if the main path is blocked early, what's the next most valuable thing to do?]

---

## Abort Conditions (HARD STOPS)

Stop immediately if ANY trigger:

- [ ] Europa disk < 200 MB free
- [ ] Same error repeated 3× on same phase
- [ ] Internet unreachable > 3 consecutive minutes
- [ ] Elapsed time > total budget − 10 min
- [ ] Any unexpected modification of security-sensitive files

On abort: update `long-mission-status.json`, write ABORT REPORT with full details.

---

## Success Criteria

Mission is complete when:

- [ ] [Criterion 1]
- [ ] [Criterion 2]
- [ ] [Criterion 3]

---

## Queue Messages

Send these manually at the times indicated (set phone alarms now):

### Message 1 — at [25% time] ([HH:MM UK])
```
[Mission Title] — 25% time elapsed

Expected by now: Phase 1 complete, Phase 2 in progress.
Request current checkpoint status.

IF on schedule: continue to Phase 2, keep pace.
IF behind: prioritise [top phase], skip refinements.
IF blocked: describe blocker, execute fallback plan.

RESOURCE CHECK: europa df + vpn status.
```

### Message 2 — at [40% time] ([HH:MM UK])
```
[Mission Title] — 40% time elapsed

Expected by now: Phases 1-2 done.
Request current checkpoint status.

IF on schedule: continue as planned.
IF behind: drop to 1 retry max per phase, skip slow steps.
IF blocked: pivot to fallback, report clearly.
```

### Message 3 — at [60% time] ([HH:MM UK])
```
[Mission Title] — 60% time elapsed

Expected by now: Phases 1-3 done, Phase 4 in progress.
Request current checkpoint status + key findings so far.

IF on schedule: great — continue.
IF starting to fall behind: prioritise highest-value phases.
```

### Message 4 — at [80% time] ([HH:MM UK])
```
[Mission Title] — 80% time elapsed

Expected by now: Main phases complete. Starting bonus work if time allows.
Final push: complete Phase [N] and start bonus phases if remaining time > [M min].
```

### Message 5 — at [92% time] ([HH:MM UK])
```
[Mission Title] — Final 8% of time budget remaining

WIND DOWN:
- No new long-running submissions to europa
- Pull remaining results
- Update long-mission-status.json with final state
- Write mission completion summary

I will review your work on return.
```
