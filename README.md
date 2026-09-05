# AuditedEpistemicCache

**How many times have we been here before?**

This repository audits a recurring mechanism that appeared independently across many of Antti Luode's projects:

> **Pay for expensive evidence once. Reuse the result while a cheap certificate says it is still valid. Spend some budget checking that belief. When it fails, diagnose why, refresh the evidence, and let repeated confirmations change what is cheap next time.**

This is **not one new cache algorithm** and it is not a claim that planning, perception, CI, memory, active sensing and neural dynamics are secretly the same thing. They are not. The point of the repo is to identify the shared contract, preserve the differences, and see whether a useful common instrument exists.

## The recurring loop

```text
                    VALIDITY LOOP

                       ● BUY
                 expensive consequence
                         |
                         v
                   □ EXPECTATION
                  /             \
          cheap certificate    uncertain / due
                says safe          |
                  |                ● AUDIT
                  v                 |
               ↻ REUSE        fresh consequence
                  |                 |
                  |            +----+----+
                  |            |         |
                  |            ✓         ×
                  |          valid      stale
                  |            |         |
                  +------------+-----> □ refresh


                    DIAGNOSIS LOOP

                         ×
                something invalidated
                         |
                         v
                    ? SUSPECTS
                         |
                  choose experiment
                         |
                         ●
                  buy consequence
                         |
                   reject / ✓ confirm
                         |
                     repair/update
```

A third, slower process sits above both loops:

```text
many useful ✓ confirmations
          |
          v
compile recurring causal relevance into structure
          |
          v
future evidence becomes cheaper
          |
          v
fewer ● purchases
```

That last arrow is where the cache line meets the older operator / structural-plasticity work.

## The six symbols

| symbol | question | generic meaning |
|---|---|---|
| **● BUY** | What did we actually pay to learn? | expensive model call, validation run, probe, rollback, planner call, full test, calibration pulse |
| **□ EXPECTATION** | What consequence are we carrying forward? | cached output, plan, baseline, dependency edge, episodic fact, response model, prototype |
| **↻ REUSE** | What work did the expectation let us avoid? | skipped compute, skipped measurement, avoided rediscovery, reused plan |
| **× STALE** | What evidence says the old expectation no longer deserves trust? | prediction residual, unsafe audit, age, changed dependency, drift, failed confirmation |
| **? SUSPECT** | Which explanation / receiver / cause should be tested next? | faulty change, hidden law, location, stale entry, world-vs-self cause |
| **✓ CONFIRM** | What expensive or independent consequence survived a direct test? | direct rollback, audit recomputation, held-out consequence, intervention response |

The important addition is that every `□` should carry its own receipt:

```text
□ expectation
  acquired_by:
  acquisition_cost:
  context / address / receiver:
  tolerance / validity claim:
  age:
  reuse_count:
  last_audit:
  audit_count:
  miss_count:
  provenance:
```

A cache entry is therefore not merely a value. It is a **claim that a previously purchased consequence remains usable under some conditions**.

## Repository audit

The detailed comparison is in **[AUDIT.md](AUDIT.md)**. The mathematical / software contract is in **[UNIFIED_MODEL.md](UNIFIED_MODEL.md)**. The deeper second scan — including `WidePresent`, representation drift, soft archival and the research-claim ledger — is in **[SECOND_PASS.md](SECOND_PASS.md)**. The machine-readable common event record is in **[`schema/aec-event.schema.json`](schema/aec-event.schema.json)**.

### Second-pass correction: one `age` was not enough

The deeper scan found four dimensions that the first schema had flattened:

```text
TIME
  world / valid time       when the represented thing belongs in the world
  knowledge / arrival time when the system learned it
  evidence completeness    whether late evidence could still arrive

GENERATION
  model / representation / operator version under which the claim was earned

RESIDENCY
  hot / warm / cold / archived — not every removal is hard eviction

ADMISSION
  whether a new observation deserves a durable entry at all
```

This comes most sharply from [WidePresent](https://github.com/anttiluode/WidePresent): `nothing happened` and `nothing has arrived yet` are different epistemic states. An entry can therefore be **INCOMPLETE** without being stale. [PredictiveHKT](https://github.com/anttiluode/PredictiveHKT) adds another failure mode: an operator estimated in a learned latent space can become obsolete because the *representation itself* moved. [Island-Memory-Field](https://github.com/anttiluode/Island-Memory-Field) supplies soft archival instead of binary keep/evict.

The schema now carries these as optional metadata rather than adding more visual glyphs. The six-symbol legend stays simple; the receipt behind each symbol gets stricter.

The recurring mechanism appears in several distinct families.

### 1. Cached control and computation

- [TheClutch](https://github.com/anttiluode/TheClutch) / [TheClutch2](https://github.com/anttiluode/TheClutch2) — cached habitual plan versus expensive replanning; surprise wakes cognition.
- [Concrete](https://github.com/anttiluode/Concrete) — learned file→test relevance is reused; a fixed exploration reserve audits blind spots.
- [ConcreteVideo](https://github.com/anttiluode/Concrete/tree/main/ConcreteVideo) — cached PyTorch block outputs; random audits test predicted-safe reuse.
- [Oppository](https://github.com/anttiluode/Oppository) — the same contract around real RAFT optical-flow GPU calls.
- [AIvideoFX](https://github.com/anttiluode/AIvideoFX) — generated keyframes are carried forward until a cheap visual certificate, age rule, or audit buys a fresh diffusion result.

### 2. Paid observation, intervention and diagnosis

- [GeometricNeuronV24](https://github.com/anttiluode/GeometricNeuronV24) — addressed scalar observations are paid evidence; persistent WRITE prevents repeated sensing.
- [ReadWrite](https://github.com/anttiluode/ReadWrite) — sometimes observation cannot resolve the hidden state; a state-dependent intervention becomes an additional sense.
- [LentoOrava / PulseTriage](https://github.com/anttiluode/LentoOrava) — coded rollback measurements produce suspects; direct individual rollback confirms them.
- [DidItChange](https://github.com/anttiluode/DidItChange) — a prediction is reused until residuals justify a change verdict and local refinement.
- [RajoitustenHierarkia](https://github.com/anttiluode/RajoitustenHierarkia) — the cache is literally a set of expected healthy scalar consequences, with age, stale uses, refresh cost and residual audits measured directly.

### 3. Memory and learned relevance

- [Child](https://github.com/anttiluode/Child) — fast traces preserve evidence until delayed relevance is known; later gates expose self-sealing memory policies and reserve exploration.
- [AlternativeNeuron](https://github.com/anttiluode/AlternativeNeuron) — fast poking, medium memory and slow routing cost are separated; remembered hypotheses alter the geometry/cost of future measurements.
- [BlackBoxLab](https://github.com/anttiluode/BlackBoxLab) — traces can live in a shared environment; later agents reuse history written outside themselves.
- [PresentMoment](https://github.com/anttiluode/PresentMoment) — a partial match showing that reusable history can live as a decaying dynamical trace rather than a symbolic cache entry.
- [Island-Memory-Field](https://github.com/anttiluode/Island-Memory-Field) — a partial match supplying working/cold archival and soft influence suppression instead of hard eviction.

### 4. Slow structure / operator identification

- [OutoSynapsi](https://github.com/anttiluode/OutoSynapsi) — scalar consequences identify which effective transport law is governing a world; repeated traffic then deforms the operator under a fixed budget.
- [Operaattori](https://github.com/anttiluode/Operaattori) / [OperaattoriJako](https://github.com/anttiluode/OperaattoriJako) — persistent structure compiles a transport operator; local nonlinear state can change the consequence and the gradient of structural edits.
- [PhaseKeyedAssociativeStorage](https://github.com/anttiluode/PhaseKeyedAssociativeStorage) — historical partial match: repeated satisfied relations strengthen structure, weak relations prune, and interventions respond to constraint violations.
- [Geometric-Neuron](https://github.com/anttiluode/Geometric-Neuron) — historically important for a different reason: later null tests were allowed to invalidate earlier “memory” claims. It is a claim-audit ledger, not an implementation of this cache contract.
- [PerceptionLab](https://github.com/anttiluode/PerceptionLab) — the older experimental origin of changing what is sampled, at what scale, and when.

### 5. Time, representation and epistemic completeness

- [WidePresent](https://github.com/anttiluode/WidePresent) — world time, knowledge time and watermarks expose the difference between stale evidence and evidence that has not arrived yet.
- [PredictiveHKT](https://github.com/anttiluode/PredictiveHKT) — an estimated operator lives inside a representation generation; if the learned encoder moves, old modal claims can become incomparable even when the external world did not change.
- [DidItChange](https://github.com/anttiluode/DidItChange) — prediction residuals identify when the generating process itself may have changed rather than merely when a cached computation should be rerun.

### 6. Meta-level claim auditing

- [BirthOfClockfield](https://github.com/anttiluode/BirthOfClockfield) — an earlier GUE/Riemann interpretation was later audited to GOE-like generic chaos, and a prime-trace null became a permanent gatekeeper.
- [Island-Memory-Field](https://github.com/anttiluode/Island-Memory-Field) — an attempted equivalence was downgraded after a blind-zone counterexample; the surviving mechanism was repurposed rather than the failed claim hidden.
- [ClockfieldDimensionality](https://github.com/anttiluode/ClockfieldDimensionality) and several [ShadyStuff](https://github.com/anttiluode/ShadyStuff) notes contain explicit `Honest Ledger` claim/status sections.

This is the same **bookkeeping topology** at a meta level, not the same runtime algorithm. The schema therefore allows `scientific_claim` as an optional payload kind while keeping claim-ledger adapters explicitly separate from compute/cache adapters.

## What is actually shared

A useful common abstraction is:

```text
context c_t
   |
   +--> cheap certificate z_t
   |
   +--> cached claim E_k: "the old expensive consequence is still good enough"

policy chooses one of:

REUSE     use the cached consequence
AUDIT     pay for truth even though reuse looked safe
REFRESH   pay because invalidity is already likely
PROBE     buy evidence that separates competing hypotheses
CONFIRM   directly test a suspect before acting on it
EVICT     stop spending memory on an entry
```

The common objective is **not maximum cache hit rate**. It is something closer to:

```text
minimize
    evidence / compute cost
  + cost of stale reuse
  + audit cost
  + memory cost
  + cost of unresolved uncertainty

subject to an explicit quality / safety / task constraint.
```

## What is *not* shared

The audit matters because several mechanisms only look alike from far away.

- A cached **plan** is not the same object as a cached **model output**.
- A cached **baseline measurement** is not the same thing as an episodic **memory**.
- `AUDIT` can mean random recomputation, a TTL refresh, an exploration reserve, a reversible intervention, a permutation null, or direct confirmation.
- Staleness can come from world drift, demand drift, model drift, context change, open-world novelty, late/incomplete evidence, or the system changing its own operator/representation.
- Some systems know the cache key exactly; others must first infer what object / state / cause the key should refer to.
- Some caches are internal data structures; BlackBoxLab's persistent material is an **environmental cache** and PresentMoment's candidate trace is a **dynamical physical residue**.
- Persistence alone is not enough. [HolographicMemory](https://github.com/anttiluode/HolographicMemory), [CognitiveBellSystem](https://github.com/anttiluode/CognitiveBellSystem), [Unit](https://github.com/anttiluode/Unit) and older memory demos rhyme with retention/adaptation but do not yet supply the paid-evidence + validity-audit contract.
- Some slow structure is not a cache at all. `Operaattori` is included because it provides the downstream mechanism by which repeated confirmed traffic can change the operator through which future evidence flows.

Those differences are the point of [AUDIT.md](AUDIT.md) and [SECOND_PASS.md](SECOND_PASS.md), not noise to average away.

## What they can become together

The first useful target is **not another synthetic neuron**. It is a common evidence ledger that existing tools can emit.

Every adapter would write the same small record, but the second pass makes the receipt richer:

```json
{
  "entry_key": "receiver-or-hypothesis-id",
  "claim": "cached consequence remains within tolerance",
  "payload_kind": "plan|output|baseline|relation|memory|model",
  "validity_state": "VALID",
  "temporal": {
    "valid_time": 1700,
    "knowledge_time": 1703,
    "evidence_complete_through": 1698
  },
  "generation": {
    "representation_id": "encoder-17",
    "operator_generation": 42
  },
  "cost": {"value": 38.2, "unit": "gpu_ms"},
  "savings": {"value": 143, "unit": "avoided_calls"},
  "residency": {"tier": "HOT", "influence_weight": 1.0},
  "provenance": "expensive execution / reversible probe / direct confirmation"
}
```

Then one visualizer can show, across completely different systems:

```text
what was bought
what was cached
what was reused
what became stale
what was audited
what was suspected
what was confirmed
whether evidence was complete yet
which representation/operator generation a claim belonged to
what was archived rather than deleted
what changed structurally because of repeated confirmation
```

That would let `Concrete`, `Oppository`, `AIvideoFX`, `PulseTriage`, `TheClutch` and later research gates share **instrumentation**, not pretend to share one algorithm.

The strongest practical candidate is therefore:

> **an auditable evidence/cache runtime + event schema + visualizer, with adapters for existing repos.**

The stricter adapter gate after the second pass is:

```text
Oppository     cached expensive answer
Concrete       cached relevance relation
PulseTriage    hypothesis -> direct confirmation
WidePresent    late evidence / two clocks / incomplete state
PredictiveHKT  representation-generation invalidation
```

If one ledger can represent all five without lying about their differences, the common schema has earned itself. If not, this repository should remain an audit/index rather than inventing another abstraction for its own sake.

## Current stopping line

Nothing here establishes a new theorem of memory, cognition, cache invalidation, active inference or metareasoning. The neighboring established fields include caching/memoization under change, bitemporal/event-time bookkeeping, stream-processing watermarks, change detection, active testing, experimental design, selective computation, value-of-information/value-of-computation ideas, continual learning and system identification.

The potentially useful contribution is more prosaic: **the same explicit evidence bookkeeping has repeatedly been missing, then re-invented, across otherwise unrelated projects.** This repo exists to make that bookkeeping reusable and falsifiable.

**Attackers first, claims second. Receipts always.**
