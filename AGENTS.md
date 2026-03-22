# AGENTS.md — AxiSEM3D Workspace

## Plan Directory

All plan files live in `plans/`:
- Active plans: `plans/active-plans/`
- Completed plans: `plans/completed-plans/`
- Phase-complete files also go in `plans/active-plans/` during a plan, and are archived to `plans/completed-plans/` on plan completion.

## Commit Convention

Use this pattern for all commits:

```
<type>(<scope>): <subject>

- Bullet 1: specific change
- Bullet 2: specific change

Plan: <relative-path-to-plan-file>
Phase: <N> of <total>
Related: <relative-path-to-phase-complete-file>
```

Types: `feat`, `fix`, `refactor`, `test`, `docs`, `chore`
Include `Plan:` and `Phase:` fields for all feature/fix commits.

## Europa Remote Execution

`.europa.yml` is present in this workspace. Before planning any computationally expensive task — AxiSEM3D simulations, kernel computations, large data processing, compilation — **load the europa skill** and route the work accordingly.

Europa is a university office PC, accessible only via the **university VPN** (manual connection required). Always remind the user to check VPN before issuing any europa command.

Key constraint: one heavy job at a time. Check `europa status` before submitting new work.

## Project Context

- **Package:** `axikernels` — Python tools for AxiSEM3D seismic kernel computation
- **Solver:** AxiSEM3D (C++/MPI spectral element) lives in `axisem3d_root/`
- **Remote binary:** `another_axisem3d` installed on europa, used for kernel runs
- **Environments:** `.venv/` for local Python dev; `axikernels_env` conda env activates on europa
