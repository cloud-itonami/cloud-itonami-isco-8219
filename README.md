# cloud-itonami-isco-8219

Open Occupation Blueprint for **ISCO-08 8219**: Assemblers Not
Elsewhere Classified.

This repository designs a forkable OSS business for a generic
assembly-line scheduling and logistics coordination practice: a line
scheduling and supply-coordination robot manages crew/task records
under a governor-gated actor, so an assembly-line crew keeps its own
operating records instead of renting a closed workforce-management
SaaS.

8219 is a residual "Not Elsewhere Classified" ISCO-08 unit group
covering diverse assembly-line work not captured by the more specific
unit groups (8211 Mechanical Machinery Assemblers, 8212 Electrical and
Electronic Equipment Assemblers). Standard assembly-line hazards apply
generically (pinch-point/crush hazard, hand-tool injury) without a
single dominant hazard type.

**Maturity: `:implemented`.** `src/assemblycoord/` implements the
`AssemblyCoordActor` as a `langgraph.graph/state-graph`
(`assemblycoord.actor`) wired to an `Assembly Line Scheduling
Coordination Advisor` (`assemblycoord.advisor`) and an independent
`AssemblyCoordGovernor` (`assemblycoord.governor`), following the
itonami actor pattern (ADR-2607121000): `:intake -> :advise -> :govern
-> :decide -+-> :commit (:ok? true) +-> :request-approval (:escalate?
true, human-in-the-loop interrupt) +-> :hold (:hard? true)`. HARD
invariants (always hold, never overridable): assembler provenance,
line provenance, no-actuation (`:effect` must be `:propose`), a closed
op-allowlist (`:log-work-record`, `:schedule-crew-operation`,
`:flag-safety-concern`, `:coordinate-supply-order` — nothing else may
ever be proposed), and a permanent, unconditional block on any
proposal that would directly finalize an assembly-execution decision
(e.g. deciding to proceed with a specific assembly run) or a
line-safety-clearance decision (e.g. declaring an assembly line safety
cleared), or that would override a plant safety officer's judgment.
Always-escalate paths (human sign-off regardless of confidence,
mapping this repo's Trust Controls in
[`docs/business-model.md`](docs/business-model.md)):
`:flag-safety-concern` (always) and `:coordinate-supply-order` above
the registered cost threshold.

## Robotics premise

All cloud-itonami verticals are designed on the premise that a **robot performs
the physical domain work**. Here a line scheduling/logistics
coordination robot performs crew scheduling, production-run/inventory/
progress-record logging and components/materials-stock supply-order
coordination for a generic assembly-line crew, under an actor that
proposes actions and an independent **Assembly Line Scheduling
Coordination Governor** that gates them. The governor never dispatches
hardware itself, never performs assembly work on the line, and never
finalizes an assembly-execution decision or a line-safety-clearance
decision, and never overrides a plant safety officer's judgment;
`:high`/`:safety-critical` actions (such as a flagged pinch-point/
crush-hazard or hand-tool-injury concern, or an above-threshold supply
order) require human sign-off. **This actor coordinates LINE
SCHEDULING/LOGISTICS ONLY — it never performs assembly work itself,
and it never makes a line-safety-clearance decision itself.**

Assemblers Not Elsewhere Classified perform diverse assembly-line work
across industries not captured by the more specific 8211/8212 unit
groups; the shared hazard dimension is generic assembly-line hazard
(pinch-point/crush hazard from moving parts and fixtures, hand-tool
injury), without a single dominant hazard type. This actor never
performs that work and never clears a line as safe — it only schedules
and logs around it, and always routes safety concerns to a human plant
safety officer.

## Core Contract

```text
crew roster + line registration + safety-reporting policy
        |
        v
Assembly Line Scheduling Coordination Advisor -> AssemblyCoordGovernor -> log/schedule/coordinate, or human sign-off
        |
        v
robot actions (gated) + operating records + audit ledger
```

No automated advice can dispatch a robot action the governor refuses,
finalize an assembly-execution decision, finalize a line-safety-
clearance decision, override a plant safety officer's judgment,
suppress an operating record, or disclose sensitive data without
governor approval and audit evidence.

## Capability layer

Resolves via [`kotoba-lang/occupation`](https://github.com/kotoba-lang/occupation)
(ISCO-08 `8219`). Required capabilities:

- :robotics
- :identity
- :audit-ledger

See [`docs/business-model.md`](docs/business-model.md) and
[`docs/operator-guide.md`](docs/operator-guide.md).

## License

AGPL-3.0-or-later.
