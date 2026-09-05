# Second-pass audit — things the first AEC map missed

The first pass found a recurring loop around paid evidence, reuse, audit, staleness, diagnosis and confirmation. A deeper scan of the older and side-branch repositories changes the abstraction in several important ways.

The strongest correction is not another cache policy:

> **`age` is not enough, `valid/stale` is not enough, and a cache entry can become invalid because the world changed, because our knowledge arrived late, because the representation changed, or because the entry was merely pushed into a colder tier rather than deleted.**

This note records the additional matches, and also marks weak rhymes that should not be promoted into the common contract.

---

## 1. WidePresent — a cache entry needs more than one clock

Link: [WidePresent](https://github.com/anttiluode/WidePresent)

This is the most important second-pass match.

`WidePresent` already arrived at a bookkeeping distinction that the first AEC schema flattened into one field called `age`:

```text
world / event / valid time     when the represented fact belongs in the world
knowledge / arrival time       when the system learned the fact
```

A Sunday event reported on Tuesday is simultaneously **old in world time** and **new in knowledge time**.

That means an AEC entry should not generally say only:

```text
age = 2 days
```

It may need:

```text
valid_time / event_time
observed_at / knowledge_time
world_age
knowledge_age
```

`WidePresent` also contains an even more important distinction:

```text
nothing happened
```

is not the same as

```text
nothing has arrived yet
```

With delayed sources, the second state is **incomplete evidence**, not a valid negative and not a stale cache entry. Its watermark logic deliberately abstains until the evidence horizon is closed.

### AEC correction

AEC therefore needs a validity state such as:

```text
UNKNOWN
INCOMPLETE
VALID
SUSPECT
STALE
SUPERSEDED
```

and optional evidence-completeness metadata:

```text
source_watermark
evidence_complete_through
expected_lateness
```

This is not cosmetic. Without it, a cache can confidently reuse an answer whose supporting evidence has not even had time to arrive.

---

## 2. PresentMoment — memory can be a decaying physical trace

Link: [PresentMoment](https://github.com/anttiluode/PresentMoment)

`PresentMoment` is not an AEC implementation. There is no explicit cache lookup or audit policy. But it exposes a storage form that the first pass omitted.

Its core object is a bank of body/brain ringdowns:

```text
tau_i db_i/dt = -b_i + g_i u(t-d_i)
```

so the current physical state contains a continuously decaying residue of earlier events. The event is gone, but a useful consequence of it is still present in the organism's state.

That gives another payload/residency class:

```text
symbolic cache value
cached model output
persistent structural write
environmental trace
dynamical / physical trace
```

A dynamical trace does not become stale at one discrete instant. Its influence can decay continuously.

### AEC correction

An entry may therefore have an optional:

```text
influence_weight
decay_model
half_life / time_constant
```

The common contract should not require all memory to be dictionary-like.

---

## 3. Island-Memory-Field — eviction can be soft

Link: [Island-Memory-Field](https://github.com/anttiluode/Island-Memory-Field)

This repository contains a continual-memory mechanism in which a memory slot can be moved deeper into a complex coordinate. Its contribution at the active boundary then attenuates roughly with depth.

The useful systems idea is independent of the Riemann inspiration:

```text
hot / working memory
        ↓
move deeper
        ↓
cold / archival memory
        ↓
weaker current interference, not total deletion
```

That is different from the first AEC vocabulary, which mostly had `STORE` and `EVICT`.

### AEC correction

Add a residency/tier concept:

```text
HOT
WARM
COLD
ARCHIVED
```

with optional `influence_weight` and `residency_cost`.

An entry can remain valid but be deliberately made less influential because demand fell or interference became expensive.

The same repository is also a good **meta-AEC** example: an attempted RH characterization was stress-tested, a blind zone was found, the claim was downgraded to a necessary condition, and only the surviving mathematical mechanism was carried into the engineering branch.

---

## 4. PredictiveHKT — validity depends on the representation generation

Link: [PredictiveHKT](https://github.com/anttiluode/PredictiveHKT)

`PredictiveHKT` fits a Koopman/DMD-like operator `A` in a learned latent space. Its own honest-limit section notices a subtle failure:

> while the encoder is still training, `A` is being fit in a **moving coordinate system**, so apparent mode drift can come from the learner changing rather than the world changing.

That is exactly an AEC invalidation mode the first schema did not name.

A consequence can be perfectly valid **under representation generation 17** and meaningless under generation 18.

The same issue appears more abstractly in [OutoSynapsi](https://github.com/anttiluode/OutoSynapsi): when the effective operator law changes, a whole family of expectations derived under the old operator can become suspect at once.

### AEC correction

Receipts should optionally carry:

```text
model_version
representation_id
operator_generation
basis_id
context_hash
```

and dependency links:

```text
entry depends_on representation:17
entry depends_on operator:42
```

Then one `GENERATION_CHANGE` / supersession event can invalidate a family of entries instead of pretending each one independently became stale.

This is **generational invalidation**.

---

## 5. Spectral-island / predictive-cortex notes — admission is a separate problem

Links:

- [ShadyStuff](https://github.com/anttiluode/ShadyStuff) — `spectral_islands_field_backprop.md`
- [ShadyStuff](https://github.com/anttiluode/ShadyStuff) — `predictive_cortex.py`
- [AlternativeNeuron](https://github.com/anttiluode/AlternativeNeuron)

The first AEC pass mostly asked what to do **after an entry exists**.

Several earlier branches ask the preceding question:

> **When does a new observation deserve an entry at all?**

The spectral-island notes use a novelty-gated Oja rule: novel directions are admitted into a maintained basis while familiar directions need not allocate another slot. `AlternativeNeuron` Gate 5 later made the same issue much cleaner: scarce internal objects should be selected by future sensing value, not simply frequency, and open-world novelty imposes a certification tax.

The old `predictive_cortex.py` also contains a precursor of the same economy: drive computation with **prediction error / novelty rather than raw input**.

### AEC correction

Separate:

```text
ADMISSION     should this become a durable entry?
VALIDITY      is the existing entry still true enough?
UTILITY       is the existing entry still worth capacity?
```

These are not one decision.

A valid entry may be evicted because it is useless. A novel observation may be ignored because storing it is not worth the cost. A familiar observation may trigger no new storage at all.

---

## 6. AIvideoFX / Clutch — staleness evidence can itself have memory

Links:

- [AIvideoFX](https://github.com/anttiluode/AIvideoFX)
- [TheClutch](https://github.com/anttiluode/TheClutch)

AEC originally made the cheap certificate look instantaneous:

```text
cheap evidence -> valid/stale decision
```

But several actual implementations accumulate evidence over time.

`TheClutch`'s winning gate is a leaky integrator of error. `AIvideoFX`'s causal-refresh path likewise accumulates persistent mismatch rather than firing on every noisy frame.

So there can be **memory about whether memory is becoming invalid**.

### AEC correction

Keep optional audit-state fields such as:

```text
risk_state / evidence_accumulator
accumulator_decay
last_residual
residual_history_summary
```

This is different from the cached payload itself.

---

## 7. P-KAS / Unit / DendriticAttention — structural promotion is older than the cache vocabulary

Links:

- [PhaseKeyedAssociativeStorage](https://github.com/anttiluode/PhaseKeyedAssociativeStorage)
- [Unit](https://github.com/anttiluode/Unit)
- [DendriticAttentionSystem](https://github.com/anttiluode/DendriticAttentionSystem)

These are **partial historical matches**, not AEC implementations.

P-KAS has repeated satisfied relations strengthen structure, weak/irrelevant relations prune, and an intervention `Stamp` used when a constraint is violated.

`Unit` has continually adapting modular components and explicitly worries about stable long-term adaptation / catastrophic forgetting.

`DendriticAttentionSystem` already visualized an `expected pattern`, an adaptive focus field and dendritic growth toward interesting patterns.

The rhyme is:

```text
repeated useful consequence
        ↓
promote / strengthen structure

weak / unused consequence
        ↓
prune / release structure
```

But these repos lack explicit purchased-evidence receipts, validity audits and a measured reuse economy. They belong in AEC as ancestry for `STRUCTURAL_UPDATE`, not as evidence that AEC itself already existed there.

---

## 8. HolographicMemory / CognitiveBellSystem — persistence is not audited validity

Links:

- [HolographicMemory](https://github.com/anttiluode/HolographicMemory)
- [CognitiveBellSystem](https://github.com/anttiluode/CognitiveBellSystem)

These older projects contain persistent distributed traces, adaptive sensitivity and decaying transient activations. They are useful reminders of a distinction:

> **A thing surviving in memory is not the same as knowing that it remains valid for the current problem.**

AEC should not absorb every memory project merely because it has persistence.

To count as a strong AEC match, there should be some combination of:

```text
an avoidable repurchase
an explicit validity claim
a reuse decision
an independent route for discovering error
measured cost / consequence
```

Without those, it is memory, not an audited epistemic cache.

---

## 9. The hidden meta-version — the research program itself has been an AEC

Links:

- [BirthOfClockfield](https://github.com/anttiluode/BirthOfClockfield)
- [Island-Memory-Field](https://github.com/anttiluode/Island-Memory-Field)
- [ClockfieldDimensionality](https://github.com/anttiluode/ClockfieldDimensionality)
- [ShadyStuff](https://github.com/anttiluode/ShadyStuff)

This is not the same runtime algorithm, but the bookkeeping topology recurs at the level of **scientific claims**.

`BirthOfClockfield` is a clean example:

```text
□ working claim: spectrum is GUE / Riemann-like
        ↓
● adversarial test: symmetry audit + prime-trace instrument
        ↓
× claim fails
        ↓
✓ narrower replacement: generic GOE-like chaos, no prime trace
        ↓
new permanent gatekeeper for future claims
```

The repository explicitly records the GOE correction and a strict prime-trace null rather than silently overwriting the earlier story.

`Island-Memory-Field` likewise records a failed converse and downgrades the claim while salvaging the usable kernel. Several other repos contain `Honest Ledger` sections that separate established, computed and speculative claims.

### AEC correction

AEC can optionally support:

```text
payload_kind = scientific_claim
claim_status = speculative | supported | failed | superseded
falsifier / audit_method
supersedes
invalidates
scope / boundary
```

But keep this as **meta-instrumentation**, not evidence that scientific reasoning is literally a cache algorithm.

---

# The new common object

After the second pass, a useful AEC entry looks less like:

```text
key -> cached value -> age
```

and more like:

```text
IDENTITY
  key / inferred object / open-world hypothesis

PAYLOAD
  value / plan / relation / model / trace / claim

VALIDITY CLAIM
  what must remain true for reuse to be legal

TWO-CLOCK TIME
  world/valid time
  knowledge/arrival time
  evidence completeness / watermark

PROVENANCE
  which expensive consequence earned the claim

GENERATION
  model / representation / operator version on which it depends

ECONOMICS
  acquisition cost
  reuse savings
  memory/residency cost

VALIDITY STATE
  unknown / incomplete / valid / suspect / stale / superseded

AUDIT STATE
  last audit
  accumulated residual/risk
  misses

UTILITY / RESIDENCY
  hot / warm / cold / archived
  influence weight

DEPENDENCIES
  which other entries / operators / representations can invalidate this one
```

That is more complicated than the original six symbols, but the symbols can stay as the visual grammar. These are orthogonal dimensions behind them.

---

# What this suggests building

The first adapter gate should now be harder than merely proving that three repos can emit `BUY/REUSE/AUDIT`.

Use three deliberately different systems:

```text
Oppository     cached expensive answer
Concrete       cached relevance relation
PulseTriage    hypothesis -> direct confirmation
```

Then add two stress adapters:

```text
WidePresent    late evidence / two clocks / incomplete state
PredictiveHKT  representation-generation invalidation
```

If one ledger can represent all five **without lying about their differences**, the schema has earned itself.

A sixth optional demonstration could use `BirthOfClockfield` as a static claim-ledger replay, showing that the same visualizer can display claim supersession while keeping the runtime and scientific semantics explicitly separate.

The visualizer would then show not merely:

```text
WHAT WAS BOUGHT?
WHAT WAS REUSED?
WHAT WENT STALE?
```

but also:

```text
WAS THE EVIDENCE COMPLETE YET?
WHICH REPRESENTATION GENERATION DID THIS CLAIM BELONG TO?
WAS IT EVICTED, OR ONLY ARCHIVED?
DID THE VALUE BECOME WRONG, OR MERELY IRRELEVANT?
WHAT OTHER CLAIMS DEPENDED ON IT?
```

That is a substantially stronger test of whether AEC is a reusable instrument rather than a retrospective metaphor.
