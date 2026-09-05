# Audit — how many different things have we called “the same loop”?

This document is deliberately comparative. It asks, for each neighboring repo:

1. **What is expensive?**
2. **What is remembered?**
3. **What makes reuse legal?**
4. **How can the system discover that it is wrong?**
5. **What is the audit / confirmation mechanism?**
6. **What gets updated after the audit?**
7. **What is the strongest failure mode?**

The goal is not to force everything into one metaphor. The goal is to locate the exact common interfaces and the exact non-equivalences.

---

## 1. TheClutch / TheClutch2 — cached action policy

Links: [TheClutch](https://github.com/anttiluode/TheClutch) · [TheClutch2](https://github.com/anttiluode/TheClutch2)

### Object being cached

A **plan / habitual policy**. The cheap path is `cheap_step(state)`. The expensive path is `expensive_plan(state)`.

### Validity signal

A scalar prediction / task error drives a gate. The simple winning gate in the released benchmark is a leaky integrator of error rather than the more elaborate second-difference “accelerator” gate.

### Audit / refresh

There is no random audit of a believed-valid plan in the basic Clutch. Surprise itself wakes the expensive planner. Once the planner returns a calm usable plan, that plan is latched back into the cheap habitual path.

### Receipt

The published navigation benchmark reports roughly **3.4 expensive plans** versus **250.8** for replanning every step, with the Clutch using about **3.6%** of the replan-all compute in that workload.

### Failure boundary

This is **reactive invalidation**. A plan can remain wrong until the error signal exposes it. If the wrong plan produces no identifying error, the Clutch has no independent way to discover the blind spot.

### AEC role

`□ cached plan -> ↻ habitual reuse -> × surprise -> ● replan -> □ relatch`

---

## 2. Concrete — cached causal relevance map

Link: [Concrete](https://github.com/anttiluode/Concrete)

### Object being cached

A persistent **file -> test execution map**, learned by instrumenting real pytest runs.

This is already different from TheClutch. The cached object is not an answer. It is a **relation saying which expensive checks are likely to remain relevant after a particular change**.

### Validity signal

Changed files plus the learned dependency/relevance map produce a candidate test frontier.

### Audit

Concrete keeps a fixed **exploration reserve** outside the currently believed frontier. That is the load-bearing mechanism: the old map can be wrong because dynamic imports, code paths, generated dependencies or new behavior were never observed when it was learned.

### Update

Every executed test is traced again, so exploration can create new file->test edges and ordinary exploitation can update counts/reliability.

### Failure boundary

A completely trusted learned graph can become **self-sealing**: tests outside the map are never run, so evidence that the map is incomplete never arrives.

### AEC role

`□ relevance graph -> ↻ skip most tests -> ● exploration test -> ✓/× relation -> □ graph update`

This is closer to **auditing a causal frontier** than caching a value.

---

## 3. ConcreteVideo — cached internal computation

Link: [ConcreteVideo](https://github.com/anttiluode/Concrete/tree/main/ConcreteVideo)

### Object being cached

The output activation of an expensive PyTorch module.

### Cheap certificate

A bounded sketch estimates input drift `dx`. Executed calls teach an empirical output/input gain `dy/dx`. A high quantile times a safety margin predicts whether reusing the old output should stay below a tolerance.

### Audit

A fraction of predicted-safe reuses is deliberately recomputed. The expensive result is not needed for the current decision — it is purchased **for epistemic maintenance**.

### Update

An unsafe audit becomes a new gain observation and makes later reuse more conservative.

### Receipt

The repository's toy CPU benchmark reports **19 of 40 wrapped executions removed**, about **1.70x** wall speedup and approximately `1e-5` mean output error. The repo explicitly says this is a mechanism check, not proof of real video/diffusion acceleration.

### Failure boundary

The learned scalar gain is only an empirical local envelope. Discontinuous blocks, changed control arguments, GPU synchronization overhead or excessive cache memory can destroy the benefit.

### AEC role

This is one of the cleanest literal implementations of:

`● execute -> □ output + validity model -> ↻ reuse -> ● audit -> ✓ safe / × unsafe -> □ update`

---

## 4. Oppository — same contract around real RAFT compute

Link: [Oppository](https://github.com/anttiluode/Oppository)

### Object being cached

A dense optical-flow result from pretrained **RAFT-Large**.

### Cheap certificate

Two 48x27 grayscale frame sketches produce input drift. Executed RAFT transitions teach a conservative empirical gain from input drift to flow error.

### Audit

Predicted-safe reuse is occasionally replaced by a real RAFT call. The code explicitly tracks:

```text
calls
executions
reuses
audits
unsafe_audits
gpu_ms
estimated_gpu_ms_saved
predicted_error
observed_error
```

### Important distinction from ConcreteVideo

The cache is now around a real pretrained model with a visible output and a measurable GPU bill, rather than a toy wrapped block. But the README still treats the real same-video benchmark as the judge. The architecture is stronger than the published performance claim.

### Failure boundary

A fixed frame-difference threshold is a mandatory attacker. If the learned validity boundary does not beat that simpler rule after overhead, the adaptive layer has not earned itself.

### AEC role

Oppository is the most literal **economic implementation** of the AEC notation: reuse only matters if an actual expensive forward call disappeared.

---

## 5. AIvideoFX — cached generated appearance with transport

Link: [AIvideoFX](https://github.com/anttiluode/AIvideoFX)

### Object being cached

An expensive diffusion-generated **visual keyframe**.

### What makes it unusual

Reuse is not merely “hold the old value.” PhaseRail can **transport** the cached image with current motion. The cache payload therefore has a cheap dynamical continuation operator.

### Cheap certificate

The later `Concrete Dream` path uses a 48x27 luminance certificate to predict visible change. The older `Causal Refresh` path uses style-tolerant edge/ownership/transport residuals rather than raw RGB.

### Audit and age

The system has both:

- random/controlled `AUDIT` calls that buy a supposedly unnecessary diffusion answer;
- a hard **maximum key age**, which forces eventual repurchase even when the cheap certificate stays quiet.

Unsafe audits increase future skepticism.

### Failure boundary

The repository is explicit that thresholds have not yet been calibrated against human judgement, identity metrics or real diffusion-call savings. A visually compelling controller is not yet a measured acceleration result.

### AEC role

`□ cached expensive representation + cheap transport` is an important extension. Reuse can include **prediction/transport of the cached object**, not merely returning it unchanged.

---

## 6. DidItChange / Clutch drift — cached prediction model as change detector

Link: [DidItChange](https://github.com/anttiluode/DidItChange)

### Object being cached

A short-term expectation of the next scalar / local trend.

### Validity signal

Reality is compared with the current predictive envelope. A sufficiently large break triggers a change event; slower slope changes are handled separately.

### Audit / confirmation

The app refines flagged events with a local step fit to estimate when the change occurred and the before/after levels. Its synthetic suite also explicitly measures false change verdicts and misses near the noise boundary.

### Difference from compute caches

The cached model is not primarily used to skip a forward pass. It is used to decide whether **the world's generating process has changed**.

### AEC role

`□ expectation -> × residual -> ? change event -> ✓ local refinement`

---

## 7. GeometricNeuronV24 — paid sensing and durable write

Link: [GeometricNeuronV24](https://github.com/anttiluode/GeometricNeuronV24)

### Object being purchased

An addressed scalar measurement:

```text
address = (scale, row, column)
measurement = h_a^T x
```

The evidence is **address + pulse history**, not the scalar alone.

### Gate 6: memory as avoided sensing

The clearest cache-like result is Gate 6. A persistent WRITE keeps a correction in the same sensed world. Across the released 20-seed assay:

```text
ACTIVE + WRITE       42 paid probes
ACTIVE + NO WRITE   280 paid probes
```

Repeated paid looks while an event remained present fell from **56 to 0**.

### What this adds

Unlike a normal software cache, the remembered answer changes the **future state being sensed**. Memory can make later sensing unnecessary by modifying the world/model rather than by storing an answer beside it.

### Audit / selection

Gate 6B also distinguishes “uncertain” from “worth measuring”: expected information gain reduced onset search from 4.375 to **3.000 probes**, while raw predictive variance did not improve on fine-only search.

### Failure boundary

The original multiscale variance story failed. Information gain, not amplitude of uncertainty, earned the coarse-to-fine schedule.

### AEC role

AEC must allow `□` to be a **write into the represented substrate**, not only an external dictionary entry.

---

## 8. ReadWrite — intervention as purchased evidence

Link: [ReadWrite](https://github.com/anttiluode/ReadWrite)

### Core distinction

Passive observation and active intervention can have different information.

RW5 builds two hidden states that are passively indistinguishable. With a constant write operator, the response to a write is also identical. With a **state-dependent writer**, the same known intervention creates different observed consequences.

### Why this matters to AEC

When an entry is stale or two hypotheses compete, the next purchase need not be “measure again.” It can be:

> **apply a reversible perturbation chosen to make the hypotheses predict different futures.**

### Failure boundary

The current RW5 writer is synthetic. The repo explicitly does not claim the real-model path has been executed.

### AEC role

This supplies the `PROBE` action: an audit can be **interventional** rather than observational.

---

## 9. LentoOrava / PulseTriage — suspect then confirm

Link: [LentoOrava](https://github.com/anttiluode/LentoOrava)

### Object being purchased

One expensive scalar KPI after a reversible rollback intervention.

### Process

PulseTriage does not treat a sparse-screen ranking as truth:

```text
balanced rollback masks
      -> scalar outcomes
      -> sparse OMP shortlist
      -> direct individual rollback confirmations
      -> suspects
```

The implementation records every `ProbeRecord`, a `screen_effect`, and a `confirmed_effect` for each surviving suspect.

### Receipt

On the released digits workload: 256 candidate changes, 4 sparse faults, **57 scalar evaluations** recover **92.77%** of lost KPI and recall **87.11%** of actual faults, versus 257 calls for exhaustive individual rollback.

### Audit within the audit

The coded screen is permutation-calibrated. If shuffled outcomes fit nearly as well, or direct confirmations fail, the output becomes **inconclusive** instead of returning a decorative ranking.

### AEC role

PulseTriage supplies the clearest `? -> ● -> ✓` diagnosis loop. It distinguishes **evidence that nominates a cause** from **evidence that directly confirms it**.

---

## 10. RajoitustenHierarkia — expected consequences become a literal cache

Link: [RajoitustenHierarkia](https://github.com/anttiluode/RajoitustenHierarkia)

This is where the metaphor turned into explicit cache bookkeeping.

### Cached object

Expected healthy scalar consequences for a small diagnostic measurement panel.

### Two independent invalidation mechanisms

- **Demand drift:** an entry can remain correct but stop being useful.
- **World/substrate drift:** an entry can remain requested but become wrong.

### Gate 3H

The variable-drift assay records actual **stale uses**. Plain LRU reaches 164 stale uses; a distributed residual check cuts that to **3** and nearly matches an unfair hidden-phase oracle.

But the stronger architectural claim fails: **TTL-4** remains slightly better by calls per correct diagnosis.

### Why this negative result matters

AEC must not imply that surprise-driven auditing is universally superior. Sometimes an age counter is the right invalidation algorithm.

### AEC role

This repo contributes the explicit per-entry fields:

```text
age
reuse
stale-use count
refresh cost
residual audit
```

and the rule:

> **Before building intelligent invalidation, attack it with TTL, periodic refresh, LRU and random eviction.**

---

## 11. Child — delayed relevance and the self-sealing memory problem

Link: [Child](https://github.com/anttiluode/Child)

### Cached object

Child explores several forms of persistent state: receiver traces, slow selected memory, episodic temporal indices and active-sensing policy state.

### Gate 2

A bounded fast trace holds detailed evidence until a later event reveals what was relevant. Only then is the useful fragment consolidated into slow state.

That differs from a normal cache because the **key is not known at write time**.

### Gate 5

The learned retention policy receives only feedback from what it selected. After the relevance law changes, a greedy policy can fail because it receives no evidence from the ignored region. A fixed exploration reserve or surviving fast trace restores discoverability.

### Central failure mode

> **A policy can make its own ignorance invisible.**

That is exactly the epistemic version of cache invalidation's hardest problem: if a receiver is never checked, its stale relation cannot announce itself.

### AEC role

Child contributes **delayed key discovery**, **exploration against blind spots**, and the distinction between transient trace and durable memory.

---

## 12. AlternativeNeuron — hypotheses themselves change future measurement cost

Link: [AlternativeNeuron](https://github.com/anttiluode/AlternativeNeuron)

### Three clocks

```text
FAST     resolve current ambiguity by poking
MEDIUM   keep useful objects so they need not be rediscovered
SLOW     reshape routing so recurring questions become cheaper
```

### Important Gate 5 result

Memory entries are not independent cache lines. Adding a stored hypothesis can make other hypotheses more expensive to certify because the observer now has more alternatives to distinguish.

The best memory policy can therefore leave capacity unused.

### Open-world novelty tax

Closed-world recognition is cheap partly because it assumes that the new thing must already be in memory. Allow arbitrary novelty and exact certification can require much more evidence.

### Gates 6 / 7

The repo also separates statistical source recovery from semantic ownership: PCA/ICA can separate generators; efference/timing is needed to anchor which recovered source is tied to the machine's own action.

### AEC role

AlternativeNeuron says the cache can change the **measurement geometry itself**. More cached hypotheses do not monotonically reduce evidence cost.

---

## 13. BlackBoxLab — the cache can live outside the observer

Link: [BlackBoxLab](https://github.com/anttiluode/BlackBoxLab)

### Cached object

In the strongest relevant line, repeated local activity writes a persistent material / environmental structure. Builders can be removed, and later naïve agents encounter a world whose dynamics were altered by history.

### Difference from all ordinary caches

The memory is not owned by an agent process. It is **stigmergic / environmental**.

### Important boundary

The repo itself distinguishes infrastructure from heredity. Persistent causal material is not automatically a genome, lineage memory, or intelligent external memory.

### AEC role

AEC should allow a cache entry's storage location to be:

```text
private state
shared software state
shared physical/environmental state
```

The relevant property is not ownership but whether an earlier expensive interaction leaves a persistent consequence that later work can reuse.

---

## 14. OutoSynapsi — auditing which operator makes consequences

Link: [OutoSynapsi](https://github.com/anttiluode/OutoSynapsi)

### What is inferred

The hidden object is not merely a state or fault. It is the **effective transport law**.

Gate 4 hides the exponent in a family `c_e = g_e^p`. With only 16 noisy scalar arrival-time measurements, the observer identifies the supplied operator family closely enough to derive a structural allocation whose objective is near the hidden-world oracle.

### Why this belongs here

An expectation is only reusable if the **operator that made it** remains valid. OutoSynapsi pushes audit one level down:

```text
not only "is this cached consequence stale?"
but
"has the law mapping action to consequence changed?"
```

### Failure boundary

The candidate functional family is supplied. This is system identification inside a constrained model class, not open-ended discovery of mathematics.

### AEC role

This is the natural model for **operator provenance** on an entry. A cache receipt may need to say not only where a value came from, but under which identified dynamics it was valid.

---

## 15. Operaattori / OperaattoriJako — after the cache, compile the body

Links: [Operaattori](https://github.com/anttiluode/Operaattori) · [OperaattoriJako](https://github.com/anttiluode/OperaattoriJako)

These are **not cache systems**, and they should not be relabelled as such.

They matter because they provide the slower endpoint of the proposed synthesis:

```text
persistent structure
      -> transport operator
      -> local nonlinear state
      -> consequence
```

`Operaattori` explicitly separates rigid pose from intrinsic metric: changing intrinsic metric recompiles the passive operator, while a pure rigid bend is a compiler no-op in the tested cable model. It also shows that the gradient of a structural change can depend strongly on operating state because nonlinear feedback alters the consequence.

### AEC role

Repeated confirmed evidence could eventually be **compiled into structure**, but the structural update rule must respect the actual operator and local nonlinear state. “Frequently used -> strengthen” is not enough.

This is downstream of AEC, not part of its minimal cache contract.

---

## 16. Geometric-Neuron — the meta-cache: claims themselves need invalidation

Link: [Geometric-Neuron](https://github.com/anttiluode/Geometric-Neuron)

The historical repo now contains a useful meta-example. A once-prominent phase-memory retrieval result was later attacked with no-probe and wrong-probe controls. The claimed retrieval mechanism collapsed because the experiment already knew the memory addresses; the expensive-looking probe was not causally necessary.

The repo retained the dead claim and the killing null rather than rewriting history.

### AEC role

Not a runtime cache. It is a reminder that **research claims are cached expectations too**:

```text
claim
  acquired_by: experiment
  provenance: script / dataset / settings
  reused_by: later reasoning
  audit: null / attacker / replication
  verdict: survives / narrowed / killed
```

This is why “Receipts always” belongs in the new repo.

---

# Cross-cutting differences

## A. What kind of object is cached?

| kind | examples |
|---|---|
| action / plan | TheClutch |
| causal relevance relation | Concrete |
| internal model output | ConcreteVideo, Oppository |
| generated representation | AIvideoFX |
| local predictor / trend | DidItChange |
| spatial state correction | GeometricNeuronV24 |
| expected scalar consequence | RajoitustenHierarkia |
| episodic fact / latent hypothesis | Child, AlternativeNeuron |
| external material trace | BlackBoxLab |
| operator model | OutoSynapsi |
| suspected cause | PulseTriage, until direct confirmation |

These payloads should **not** share the same validity estimator by default.

## B. What makes an entry stale?

```text
WORLD DRIFT
    the same action now has a different consequence

DEMAND DRIFT
    the entry is still true but no longer useful

MODEL / OPERATOR DRIFT
    the law that made the expectation changed

CONTEXT DRIFT
    same key, different surrounding state

COORDINATE DRIFT
    physical implementation / address changed

OPEN-WORLD NOVELTY
    the new case is not one of the cached hypotheses

SELF-INDUCED DRIFT
    the system changed its own operator, invalidating old predictions

POLICY BLINDNESS
    the entry is wrong but the current policy never buys evidence that could reveal it
```

A universal AEC must represent **why** an entry might become invalid, not merely attach a single TTL.

## C. How is invalidity discovered?

| mechanism | repos |
|---|---|
| prediction residual / surprise | TheClutch, DidItChange, RajoitustenHierarkia |
| random audit of predicted-safe reuse | ConcreteVideo, Oppository, AIvideoFX |
| age / TTL / periodic refresh | AIvideoFX, RajoitustenHierarkia |
| exploration outside current frontier | Concrete, Child |
| information-gain measurement | GeometricNeuronV24 |
| reversible intervention | ReadWrite, PulseTriage |
| direct confirmation | PulseTriage |
| permutation / shuffled null | PulseTriage, many research gates |
| held-out prediction under a changed world | OutoSynapsi, RajoitustenHierarkia |

No single audit policy dominates this list.

## D. What is the cost unit?

The unification should not erase cost semantics.

```text
planner expansions / calls
pytest seconds
GPU milliseconds
model forward passes
diffusion generations
scalar validation calls
paid spatial probes
memory slots
transport / conductance budget
calibration pulses
false-action / stale-reuse loss
```

The ledger therefore needs `cost_value` **and** `cost_unit`, not one abstract “cost” scalar unless an adapter deliberately normalizes it.

## E. What does “confirmed” mean?

Three levels should remain separate:

```text
SUPPORTED
    the cached prediction matched one observed consequence

AUDITED
    a supposedly unnecessary independent check also matched

CAUSALLY CONFIRMED
    a targeted intervention / direct rollback discriminated the suspect
```

A fourth research-level state is useful:

```text
REPLICATED / NULL-SURVIVED
    survives independent seeds, attacker, permutation or designed null
```

A system should not promote `?` directly to `✓` merely because the same evidence that nominated the hypothesis also scores it highly.

---

# The most important common lesson

The repos repeatedly converge on a more precise statement than “memory saves work”:

> **A remembered object earns its keep only when it lets a later decision avoid purchasing evidence while keeping the cost of stale reuse acceptably bounded.**

That immediately creates three accounting questions:

```text
1. How much did this belief cost to acquire?
2. How much work has reusing it avoided?
3. What independent route exists for the world to prove it wrong?
```

If question 3 has no answer, the cache can become an ignorance machine.

---

# What should be unified, and what should not

## Unify

- the **event schema** (`BUY`, `REUSE`, `AUDIT`, `STALE`, `SUSPECT`, `CONFIRM`, `EVICT`, `STRUCTURAL_UPDATE`);
- per-entry provenance, age, tolerance, cost, reuse count and audit history;
- cost / savings accounting;
- explicit distinction between predicted validity and observed audit outcome;
- visual instrumentation;
- adapters that translate existing repos into the common event stream.

## Do not unify prematurely

- cache replacement policies;
- validity models;
- one global notion of “surprise”;
- one notion of address;
- one memory timescale;
- one operator update rule;
- one normalized cost function across unrelated applications.

The common layer should be **instrumentation + contract**, not a universal brain algorithm.

---

# Candidate integration order

The cleanest sequence is practical rather than synthetic:

1. **Oppository adapter** — easiest literal `EXECUTE / REUSE / AUDIT / unsafe` event source.
2. **Concrete adapter** — proves the same ledger can represent relational knowledge rather than cached tensor values.
3. **PulseTriage adapter** — adds `SUSPECT / CONFIRM` and provenance of causal tests.
4. **TheClutch adapter** — adds plan invalidation and latching.
5. **AIvideoFX adapter** — adds transported cached state and hard age ceilings.
6. **RajoitustenHierarkia replay adapter** — stress-tests stale-use and demand-drift fields against an existing executed receipt.

Only after those work should the repo attempt a shared policy. If common instrumentation is not already useful across those six, a shared controller is probably the wrong abstraction.
