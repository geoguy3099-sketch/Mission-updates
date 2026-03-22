# 5-Hour Seismic Phase Kernel Analysis & Comparison

> **Agent instruction:** This is a long-mission task file.
> Load the `long-missions` skill (`~/.copilot/skills/long-missions/SKILL.md`) before proceeding.
> Also load the `europa` skill (`~/.copilot/skills/europa/SKILL.md`) — all compute runs on europa.
> Follow the checkpoint format, guardrails, and status-file protocol defined in those skills exactly.

> **ATLAS WORKFLOW OVERRIDE — UNSUPERVISED MODE**: This is a 5-hour long-mission with no human supervision.
> Normally Atlas pauses after each phase to wait for user commit confirmation before proceeding.
> **For this mission, skip the pause.** After each phase checkpoint, automatically:
> - Write the phase-complete file
> - Generate and execute the git commit (auto-push to GitHub)
> - Update `long-mission-status.json`
> - Continue immediately to the next phase
> User has pre-approved this autonomous flow by launching this mission file while stepping away.

**Mission ID:** MISSION_20260322_KERNEL_PHASES
**Framework:** long-missions v1
**Start time:** 11:00 UK
**Hard stop:** 16:00 UK
**Total budget:** 5 hours (300 min)

**MISSION:** Test kernel computation parity between 1D and 3D models across 10 seismic phases.
**GOAL:** Identify if kernel differences are real geophysics or computational artifacts.
**FALLBACK:** If 1D/3D analysis completes early, repeat all phases with 30s base model.

---

## Critical Success Factors

1. **Phase isolation is EVERYTHING**: Before any full simulation, run quick envelope/FT analysis to confirm phase separation.
2. **Use europa aggressively**: 1D sims run fast on europa; pull results, inspect kernels, iterate before 3D.
3. **Report after each phase**: End each section with a CHECKPOINT block (see format below).
4. **Assume europa may timeout**: Cache results locally; if europa jobs hang > 15min, move to next phase.
5. **Time is zero-waste**: While europa jobs run, prepare next phase's inputs locally.

---

## Phase Schedule (TARGET ORDER)

1. **Phase 1: ScS Isolation & Comparison** (1h)
   - 1D analysis + kernel isolation check + 3D run
2. **Phase 2: PcP** (40 min)
3. **Phase 3: SKS** (40 min)
4. **Phase 4: PKP** (40 min)
5. **Phase 5: PP** (40 min)
6. **Phase 6: SS** (40 min)
7. **Phase 7-10: [Time permitting]** S, P, P-to-S, custom

**IF TIME PERMITS (after all phases with 50s base model):**
- Repeat Phase 1-6 with 30s base model
- OR extend to remaining phases

---

## Workflow Template for EACH PHASE

### Step 1: Quick Phase Analysis (LOCAL, ~5 min)
- Write a minimal analysis script (envelope, FFT, time windows)
- Goal: Visually confirm phase is well-separated from neighboring arrivals
- Output: PNG showing isolation + time windows

### Step 2: 1D Kernel Computation (EUROPA, ~8-15 min)
- Set up 1D forward + adjoint runs on europa
- Compute kernel with confirmed time windows
- Pull kernel image + data
- **DECISION POINT:** Is the kernel clean and isolated?
  - YES → proceed to 3D
  - NO → adjust windows, re-run on europa (iteration)

### Step 3: 3D Kernel Computation (EUROPA, ~10-20 min)
- Run 3D forward + adjoint with identical window setup
- Compute kernel
- Pull kernel image + data

### Step 4: Compare & Report (LOCAL, ~3 min)
- Side-by-side plots: 1D kernel, 3D kernel, difference
- Quantify: max amplitude difference, pattern difference (%)
- Record: any major shape differences?

---

## Resource Budget

| Resource        | Limit                       | Abort if            |
|-----------------|-----------------------------|---------------------|
| Europa disk     | ≥ 1 GB free required        | < 200 MB free       |
| Time per phase  | See schedule above          | > 2× estimated time |
| Max retries     | 2 per phase                 | 3rd failure = skip  |
| Max total time  | 300 min (16:00 UK)          | Exceeded → stop now |
| Network (VPN)   | Must be active              | 3+ min disconnected |

---

## Abort Conditions (HARD STOPS)

Stop immediately if ANY trigger:

- [ ] Europa disk < 200 MB free
- [ ] Same error repeated 3× on same phase
- [ ] Internet unreachable > 3 consecutive minutes
- [ ] Elapsed time > 290 min (16:00 UK − 10 min buffer)
- [ ] Any unexpected modification of security-sensitive files

On abort: update `long-mission-status.json`, write ABORT REPORT with full details.

---

## Fallback Plan

If 1D kernel computation is broken or europa is unavailable:
→ Skip to 3D analysis with manual configuration
→ Document the discrepancy and continue to next phase
→ If both 1D and 3D are blocked: run the phase analysis scripts locally and save isolation plots only (scientific value without the simulation cost)

---

## Success Criteria

- ✅ Complete checkpoints for Phases 1-6 (or as many as fit in 5h)
- ✅ Side-by-side kernel comparisons (1D vs 3D) for each phase
- ✅ Clear answer: "1D/3D kernels are [similar|different]" with quantification
- ✅ Evidence of phase isolation (images saved to disk)
- ✅ `long-mission-status.json` updated with all checkpoints

---

## Checkpoint & Status

After every phase, output a CHECKPOINT block (see long-missions skill) and update `long-mission-status.json`.

**After each checkpoint commit and push status:**
```bash
git add long-mission-status.json
git commit -m "mission: checkpoint [phase name]"
git push
```
(GitHub Actions will handle the push automatically if the workflow is configured.)

---

## Queue Messages

Set phone alarms and paste these at the scheduled times:

### Message 1 — 11:45 UK (45 min elapsed)
```
KERNEL MISSION — 25% time elapsed

Expected by now: Phase 1 (ScS) complete or nearly complete.
Request current checkpoint status.

IF on schedule: continue Phase 2 (PcP).
IF behind: cut Phase 1 iteration, push to Phase 2 now.
IF blocked: describe blocker, pivot to fallback plan.

RESOURCE CHECK: europa df + confirm VPN active.
```

### Message 2 — 12:45 UK (1h 45min elapsed)
```
KERNEL MISSION — 40% time elapsed

Expected by now: Phases 1-2 complete (ScS, PcP). Phase 3 (SKS) in progress.
Request current checkpoint + any patterns emerging in 1D vs 3D kernels.

IF on schedule: continue Phase 3 and 4.
IF behind: drop to 1 retry max per phase. Skip refinements.
IF blocked: pivot to fallback. Continue non-blocked phases.
```

### Message 3 — 13:45 UK (2h 45min elapsed)
```
KERNEL MISSION — 60% time elapsed. Final push beginning.

Expected by now: Phases 1-3 done. Phase 4 (PKP) in progress.
Checkpoint: how many phases complete? Any surprising 1D/3D differences?

IF on schedule: continue Phase 5 (PP).
IF ahead: start bonus phases or begin 30s model repeat.
IF tight: prioritise completing the phase in progress, skip SS if needed.
```

### Message 4 — 14:40 UK (3h 40min elapsed)
```
KERNEL MISSION — 80% time elapsed.

Expected by now: Phases 1-5 done. Phase 6 (SS) in progress or complete.
Final 80 min: wrap up Phase 6, start 30s base model Phase 1 if > 30 min remains.

WIND-DOWN RULE: Do not start any new europa job if estimated runtime > remaining time.
```

### Message 5 — 15:20 UK (4h 20min elapsed)
```
KERNEL MISSION — Final 40 minutes.

STOP all new submissions. Pull any remaining results.
Update long-mission-status.json with final state.
Write completion summary: phases done, key findings, 1D/3D comparison table.

I will review your work at 16:00.
```

---

## Launch Prompt for Atlas

```
Load the long-missions skill and the europa skill.
Then read KERNEL_ANALYSIS_5H_MISSION.md — that is your complete mission specification.

Follow the long-missions checkpoint format and status-file protocol throughout.
Follow the europa guardrails (VPN check, disk check, one job at a time) for all remote work.

Begin with Phase 1 (ScS). Work through phases systematically.
Hard stop: 16:00 UK. Start now.
```
