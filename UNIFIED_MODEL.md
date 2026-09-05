# Unified model — an audited cache of claims about consequences

This note asks the constructive question left by [AUDIT.md](AUDIT.md):

> **What is the smallest common object that TheClutch, Concrete, Oppository, AIvideoFX, PulseTriage, V24 and the memory/operator repos can all emit without pretending they run the same algorithm?**

The proposed answer is a **claim-bearing cache entry plus an event ledger**.

The common layer is intentionally weaker than a universal controller.

---

## 1. Cache the claim, not only the value

A normal cache stores roughly:

```text
key -> value
```

The recurring projects need something closer to:

```text
key
  -> payload
  -> claim about when that payload remains usable
  -> receipt showing how the claim was acquired
  -> history showing how often it has been reused and audited
```

Write one entry as

```text
E_k = (
    key,
    payload,
    context,
    claim,
    tolerance,
    provenance,
    acquisition_cost,
    acquired_at,
    age,
    reuse_count,
    audit_count,
    miss_count,
    last_audit,
    validity_model
)
```

The semantic center is `claim`.

For a cached model output it could be:

```text
"for inputs sufficiently close to anchor x0,
 reusing y0 keeps output error <= 1.5 px"
```

For Concrete:

```text
"this test has been causally/relevantly connected to these files
 in observed executions"
```

For RajoitustenHierarkia:

```text
"this healthy rollback consequence is still the expected baseline"
```

For TheClutch:

```text
"this cached plan still explains / solves the current local situation"
```

For PulseTriage:

```text
"candidate X is a plausible cause"
```

followed by a stronger entry after direct confirmation:

```text
"rolling back X improved the KPI by Δ under the current incident"
```

The payload can differ completely. The contract is the same because **reusing the payload spends trust in the claim**.

---

## 2. Separate five quantities that the old repos often mixed

### 2.1 Value

The cached thing itself:

```text
plan
activation
flow field
keyframe
baseline scalar
memory prototype
relation edge
operator parameter
```

### 2.2 Validity

Whether the old value remains good enough **now**.

### 2.3 Utility

Whether the entry is worth keeping even if it remains valid.

This is the RajoitustenHierarkia distinction:

```text
world drift  -> entry can become wrong

demand drift -> entry can remain right but become irrelevant
```

### 2.4 Identity / key

What current event the old entry should even be compared with.

This is easy in a dictionary cache and hard in `AlternativeNeuron`, open-world memory, moving substrates and causal-address experiments.

### 2.5 Provenance

What purchased consequence originally justified the entry.

Without provenance, `✓ confirmed` quickly degenerates into “the model believed itself twice.”

---

## 3. The universal actions are verbs, not policies

Every adapter should be able to emit some subset of these events:

```text
BUY
    an expensive consequence was actually purchased

STORE
    a purchased consequence became persistent state

REUSE
    a persistent consequence replaced a purchase

AUDIT
    a supposedly avoidable purchase was deliberately made to test validity

STALE
    an audit / residual / age rule established or strongly indicated invalidity

SUSPECT
    a hypothesis was nominated but not directly established

PROBE
    a measurement or intervention was selected to discriminate hypotheses

CONFIRM
    independent/direct evidence supported a suspect

REJECT
    independent/direct evidence contradicted a suspect

REFRESH
    an entry was replaced by newly purchased evidence

EVICT
    an entry stopped consuming capacity

STRUCTURAL_UPDATE
    repeated confirmed traffic changed the slower operator / routing / body
```

The event vocabulary is the thing to share.

Whether `AUDIT` is random, TTL-based, residual-triggered, information-gain-selected or direct rollback remains adapter-specific.

---

## 4. A compact event record

A useful common event could look like:

```json
{
  "time": 183.2,
  "system": "Oppository",
  "event": "AUDIT",
  "entry_key": "raft-flow:last",
  "payload_kind": "model_output",
  "context": {
    "receiver": "raft-large",
    "anchor": "frame_pair_8041"
  },
  "claim": {
    "metric": "flow_rms_error_px",
    "tolerance": 1.5,
    "predicted": 0.73
  },
  "cost": {
    "value": 21.4,
    "unit": "gpu_ms"
  },
  "outcome": {
    "observed_error": 0.82,
    "verdict": "valid"
  },
  "entry_before": {
    "age": 12,
    "reuse_count": 41,
    "audit_count": 5,
    "miss_count": 0
  }
}
```

A PulseTriage event would use the same outer shape but different semantics:

```json
{
  "system": "PulseTriage",
  "event": "CONFIRM",
  "entry_key": "candidate:preprocess_117",
  "payload_kind": "causal_hypothesis",
  "claim": {
    "statement": "rollback improves validation KPI"
  },
  "cost": {
    "value": 1,
    "unit": "validation_call"
  },
  "outcome": {
    "confirmed_effect": 0.184,
    "verdict": "confirmed"
  }
}
```

The ledger can therefore be shared without forcing the meaning of `payload` or `cost` to be shared.

---

## 5. The central decision problem

At time `t`, the system has a cache entry `E` and cheap current evidence `z_t`.

It chooses among actions such as:

```text
REUSE
AUDIT
REFRESH
PROBE
EVICT
```

A generic expected-cost expression is:

```text
ExpectedCost(a | E, z_t)
  = purchase_cost(a)
  + memory_cost(a)
  + P(stale | E, z_t, a) * stale_loss
  + uncertainty_loss(a)
  + exploration_cost(a)
```

The system chooses an action subject to application-specific constraints:

```text
predicted visual error <= tolerance
or
fault recall >= target
or
navigation success >= target
or
test-miss probability <= target
```

This equation is **not** proposed as a new theorem or a fully specified solver. It is simply the accounting surface on which the existing policies can be compared.

The important point is that **reuse rate alone is not the objective**.

A cache can achieve 99% reuse by being confidently wrong.

---

## 6. Two nested loops

### 6.1 Validity loop

```text
              cached claim E
                   |
             cheap evidence z
                   |
          predicted invalidity?
             /             \
           low              high
            |                |
          REUSE            REFRESH
            |
       sometimes AUDIT
            |
      observed fresh truth
         /          \
      matches      violates
        |             |
      VALID         STALE
        |             |
        +------ update E ----+
```

This loop dominates:

- TheClutch
- Concrete / ConcreteVideo
- Oppository
- AIvideoFX
- DidItChange
- RajoitustenHierarkia

### 6.2 Diagnosis loop

```text
           invalid / unexplained consequence
                       |
                   hypotheses H
                       |
             choose discriminating test
                       |
                    PROBE / BUY
                       |
               scalar / local consequence
                       |
               update hypothesis set
                       |
                CONFIRM / REJECT
```

This loop dominates:

- GeometricNeuronV24
- ReadWrite
- PulseTriage
- OutoSynapsi
- AlternativeNeuron

The two loops meet when **cache invalidation does not say why the entry failed**.

That is the most important composition:

```text
VALIDITY says:
    "the old thing is no longer safe"

DIAGNOSIS says:
    "which part of the world / model / operator caused that?"
```

---

## 7. The third loop: compile repeated confirmation into structure

This is where `AlternativeNeuron`, `OutoSynapsi`, `BlackBoxLab` and `Operaattori` contribute something the normal caching systems do not.

Suppose an evidence ledger shows repeated confirmed traffic:

```text
context A
  -> probe B
  -> consequence C
  -> confirmed useful
```

again and again.

There are three increasingly strong ways to exploit that history:

```text
LEVEL 1: CACHE
    store C so B need not be purchased again

LEVEL 2: ROUTE
    make B / C cheaper to access next time

LEVEL 3: COMPILE
    change the persistent operator / body so the useful consequence
    becomes a native low-cost trajectory of the system
```

`AlternativeNeuron` explicitly separates medium memory from slow routing cost.

`OutoSynapsi` derives traffic-dependent structural allocations under a fixed budget.

`BlackBoxLab` demonstrates history deposited in external slow material that changes later agents' dynamics.

`Operaattori` supplies the warning: the consequence of a structural edit depends on the operator and the current nonlinear operating state. A compile step needs an actual dynamics model or an empirical audit; simple frequency-count strengthening is not enough.

AEC should therefore treat `STRUCTURAL_UPDATE` as an optional downstream event, not part of the minimal cache core.

---

## 8. Why “audit” is load-bearing

A cache without an independent path to invalidation can become self-sealing.

This happens in different disguises:

```text
Concrete without exploration:
    unobserved dependency can never enter the map

Child without reserve / trace:
    ignored relevance region can improve invisibly

AlternativeNeuron closed-world recognition:
    every novel object gets forced into an old class

TheClutch with a blind error signal:
    wrong plan can persist if the monitor never notices

RajoitustenHierarkia frozen baseline:
    repeated reuse drifts away from the current substrate
```

So an AEC entry is incomplete unless it has some declared **invalidator**:

```text
random audit
age / TTL
periodic full check
exploration reserve
independent sensor
prediction residual
intervention
held-out consequence
human judgement
oracle / exhaustive ruler in benchmark only
```

If none exists, the entry should be labelled explicitly:

```text
UNAUDITABLE UNDER CURRENT OBSERVATION MODEL
```

That is more informative than a false confidence score.

---

## 9. “Confirmed” needs provenance levels

Use a small evidence ladder rather than one boolean:

```text
0  UNTESTED
   candidate / inherited claim

1  SUPPORTED
   matches the same stream that generated the hypothesis

2  AUDITED
   survives a purchase that was not required for exploitation

3  CAUSALLY_CONFIRMED
   survives a targeted independent/reversible intervention

4  ATTACKER_SURVIVED
   survives boring alternative / permutation / null / held-out world

5  REPLICATED
   survives new seeds / instances / substrate
```

Not every application needs all levels.

The point is to stop this transition:

```text
high score -> “confirmed”
```

when the score came from the same evidence that nominated the hypothesis.

PulseTriage already implements a small version of this separation with coded screening followed by direct rollback confirmation.

The Geometric-Neuron null-test history shows why the distinction matters at research scale.

---

## 10. Cache identity is sometimes the hard problem

Ordinary caches receive the key:

```text
URL
function arguments
module path
file path
```

Several research repos do not.

They have to infer:

```text
"is this the same event?"
"is this the same dynamical object?"
"is this one of my remembered prototypes or something novel?"
"did the world change or did my own operator change?"
```

That means AEC should distinguish:

```text
KEY_KNOWN
    ordinary cache lookup

KEY_INFERRED
    current evidence must be matched to an existing entry

KEY_OPEN_WORLD
    current evidence may deserve a new entry
```

`AlternativeNeuron` shows why this matters: the number of stored hypotheses changes the future discrimination problem, and open-world novelty introduces a real evidence tax.

Do not hide identity uncertainty inside a generic confidence number.

---

## 11. The first reusable software should be instrumentation

The repo should resist the temptation to invent `SuperAuditPolicy` immediately.

The existing projects already showed:

- a simple leaky error integrator can beat a fancier jerk detector (`TheClutch`);
- a fixed exploration reserve is load-bearing (`Concrete` / `Child`);
- a simple fixed panel can beat adaptive measurement (`RajoitustenHierarkia G3E`);
- LRU can be nearly oracle under demand shift (`G3G`);
- TTL can beat residual-driven refresh on efficiency (`G3H`);
- direct confirmation is worth spending on after a coded screen (`PulseTriage`).

Therefore the first common software should do only:

```text
record events
record receipts
record cost
record validity claims
record audits/misses
record savings
replay and visualize
```

Then adapters can compare policies without replacing them.

---

## 12. Proposed minimal API

A future package could expose something this small:

```python
ledger.buy(
    key=...,
    payload_kind=...,
    claim=...,
    cost=(value, unit),
    provenance=...,
)

ledger.reuse(key, predicted_risk=...)

ledger.audit(
    key,
    cost=(value, unit),
    observed_error=...,
    tolerance=...,
)

ledger.suspect(key, hypothesis=..., evidence=...)
ledger.confirm(key, cost=(value, unit), outcome=...)
ledger.reject(key, cost=(value, unit), outcome=...)
ledger.evict(key, reason=...)
ledger.structural_update(target=..., receipt=...)
```

The adapters remain responsible for deciding **when** to call those methods.

That is the correct abstraction boundary until evidence says otherwise.

---

## 13. Cross-tool metrics

The common visualizer can compute metrics that mean something across domains without pretending the raw cost units are comparable.

### Per entry

```text
purchases
reuses
reuse / purchase ratio
age at reuse
age at miss
audits
unsafe audits
cost paid
estimated native-unit cost avoided
cost spent auditing
stale-use count
confirmation depth
```

### Per system

```text
fraction of actions served by old evidence
fraction of old evidence independently audited
misses per 100 reuses
native-unit savings
native-unit audit overhead
mean lifetime of useful entries
entries evicted while still valid
entries retained after demand disappeared
fraction of suspects directly confirmed
```

### Research-only

```text
attacker gap
null survival
seed replication
claim status: survives / narrowed / killed
```

No global “intelligence score.”

---

## 14. The visual object

The Astra visualizer idea becomes more useful if it renders a **flow of evidence**, not a neuron.

A single entry can be shown as:

```text
             acquisition
                 ●
                 |
                 v
                □────↻────↻────↻
                |             |
               age           audit
                |             ●
                |             |
                |        ✓ valid / × stale
                |                  |
                +------------------+
```

A diagnosis can branch:

```text
                     ×
                  /  |  \
                 ?   ?   ?
                     |
                     ●
                     |
                reject / ✓
```

And structural compilation can be shown as repeated confirmed paths thickening a slower edge.

This is one visual grammar that can replay events from GPU caching, CI selection and black-box diagnosis without lying that the underlying algorithms are identical.

---

## 15. Falsification / stopping rule

This repo earns code only if a common ledger provides concrete value in existing systems.

A reasonable first criterion:

```text
three unrelated adapters
    Oppository
    Concrete
    PulseTriage

must emit the same event schema without losing the domain-specific facts
needed to explain their decisions.
```

Then the common viewer must answer, for each run:

```text
What was bought?
What was reused?
What did reuse save?
What was audited?
What went stale?
How was staleness discovered?
What was only suspected?
What was directly confirmed?
```

If the adapters require so many one-off fields that the common layer becomes decorative, stop. The honest result would be:

> **the recurring metaphor was real, but the useful software abstractions remain domain-specific.**

If the schema works, only then ask whether some audit policy can transfer across domains.
