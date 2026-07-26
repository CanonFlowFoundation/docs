# DYNAMIC_CFF_TESTING.md — The Modal Testing Annex

> *A test suite is a search for diamond witnesses. A constitution is a box formula.*
>
> Annex to `CANONFLOW_BASE.md` and `CFF_PIPELINE.md`, submitted through the
> §46.7 import filter. It gives CFF testing a formal vocabulary — dynamic
> logic's `[a]p` / `⟨a⟩p` — and three enforceable artifacts derived from it.
> CFF means CanonFlow Format (Pipeline §0.1). Base wins on conflict.

---

## 0 · POSITION LAW

Dynamic logic extends modal logic with an action language: `[a]p` — after
performing `a`, `p` necessarily holds; `⟨a⟩p` — after `a`, `p` possibly holds;
actions compose by sequence `a;b`, choice `a∪b`, iteration `a*`. Hoare
correctness `p{a}q` is the special case `p → [a]q`.

This annex adds **vocabulary and three mechanisms**, zero authority:

```text
a discharged box ⇏ legal truth                    (§3 Base, unchanged)
a modal spec     ⇏ implementation proof            (§31, unchanged)
model checking   ∈ authoring/CI time, ∉ VerdictPath (§4, unchanged)
```

What it buys: every test in the T0–T12 stack, every Crucible gate, and the
agent cage itself already *are* modal statements — naming them as such makes
obligations classifiable, dischargeable, and honest about what green means.

---

## 1 · THE ACTION LANGUAGE OF CFF v1

Atomic actions (each a total function returning a typed outcome, per §46.1):

```text
inspect · parseManifest · verifyDigests · assessTrust · extract
write · canonicalize · normalize · replay · import · export
```

Composites are the Base's own laws read as regular expressions:

```text
§19 verification order  =  inspect ; parseManifest ; verifyDigests
                           ; assessTrust ; … ; activate          (sequence, A4)
§46.1 type-states       =  the ONLY action word reaching VerdictProduced
                           passes through every promotion stage
Adapter families        =  jsonIntake ∪ csvIntake ∪ cffImport     (choice, A3)
Batch processing        =  (intakeOne)*                           (iteration, A5)
```

A3 (`[a∪b]p ↔ [a]p ∧ [b]p`) is the conformance-kit law (§34.4) in one line:
a property claimed over the adapter family is an obligation on **each**
adapter — passing the suite for one branch of the union proves nothing about
the other. A4 (`[a;b]p ↔ [a][b]p`) is why staged verification composes:
proving each stage's postcondition is the sequence's proof.

---

## 2 · BOX AND DIAMOND — THE TEST TAXONOMY

The T-layer stack is a hierarchy of modal discharge strengths:

| Obligation form | Meaning | Discharged by | Honest strength |
|---|---|---|---|
| `⟨a⟩p` | this outcome is *reachable* | example test, golden vector (T3, T7) | **full** — one witness proves a diamond |
| `p → [a]q` | after `a`, `q` *always* | property test (T4), boundary matrix (T2) | **sampled** — true at tested states, not valid |
| `p → [a]q` over decidable fragment | same, universally | `VERIFY-*` proof (LIQUID annex) | **full** — certificate over the whole domain |
| tests-of-tests | the suite can refute | mutation (T6) | measures the suite's refutation power |
| `⟨attack⟩Bad` | an unsafe state is reachable | fuzz / malicious corpus (T9) | a **failed search** — absence of witness ≠ `[attack]¬Bad` |

Two consequences, both binding:

```text
1. A golden vector proves a diamond and only a diamond. "The reader CAN
   reproduce this verdict" — never "the reader always behaves."  Presenting
   T3/T7 green as universal safety is a §31 truthful-status violation.

2. A fuzz suite that found nothing is evidence of effort, not a box.  The
   claim it licenses is "no witness found within this corpus and budget" —
   which is exactly how §14.2's "arbitrary garbage never escapes as an
   unhandled error" must be reported: bounded corpus, bounded claim.
```

---

## 3 · THE DETERMINISM LAW IS "BOX EQUALS DIAMOND"

Dynamic logic: for a **deterministic, terminating** action, `[a]p ↔ ⟨a⟩p` —
must and might collapse. That collapse *is* the Base's kernel law:

```text
VerdictPath admits only actions where [a]p ↔ ⟨a⟩p.

deterministic  (same pinned input ⇒ same canonical verdict, §10, §14.2)
∧ terminating  (bounded resource budget, §46.3)
⇔ the modal collapse holds.
```

Actions where box and diamond genuinely differ — OCR (`⟨ocr⟩CorrectField` but
not `[ocr]CorrectField`), AI extraction, network fetch, camera capture — are
precisely the actions the constitution quarantines outside the kernel (§4,
§22, §46.2). The quarantine is not a style preference; it is the refusal to
admit non-collapsing modalities into a path whose replay law (§10) *asserts*
the collapse.

**Enforceable artifact 1 — the collapse test.** The T8/replay suites are the
empirical check of `[a]p ↔ ⟨a⟩p`: run the same action from the same pinned
state N times and across hosts; any divergence is a witness that the action
does not belong in the kernel. `crucible compare-hosts` and the §46.5
same-digest-on-retry law are collapse tests by construction — name them so in
the proof manifest (`collapse: verified` per action family), and a
nondeterminism incident is formally "a diamond witness against the kernel's
box claim."

---

## 4 · ITERATION LAWS — REPLAY, IDEMPOTENCE, CONSERVATION, RETRY

**A5 / star collapse for idempotent actions.** The Base demands
`normalize(normalize(x)) = normalize(x)` (§12, §34.5). In modal form:
`[normalize*]p ↔ [normalize]p` — the star adds nothing. The test is mechanical
and already mandated (canonicalize twice, byte-compare); this annex only names
the obligation class: **star-collapse tests**, required for `normalize`,
`canonicalize`, `simplify`, and CFF re-export of an unchanged bundle.

**A6 / induction is the run-ledger proof pattern.** A6:
`p ∧ [a*](p → [a]p) → [a*]p` — establish `p` initially, show every step
preserves it, conclude it after any number of steps. The §46.4 conservation
law (`Discovered = Accepted ⊎ Rejected ⊎ Quarantined ⊎ Deferred ⊎ Unprocessed`)
is proven exactly this way: conservation at intake, preservation per stage,
therefore conservation for the whole run. FsCheck **model-based/state-machine
testing** is the sampled form of the induction premise — generate action
sequences, check `p → [a]p` at every step. **Enforceable artifact 2:** batch
and pipeline invariants in packets are declared as induction pairs (initial
establishment + per-step preservation property), not as end-state assertions;
an end-state-only check of an iterated action is a rejected test design,
because it cannot localize which step broke the invariant.

**The broken-TV rule is the retry law.** Dynamic logic derives
`b → [k]b ⊢ b → [k*]b`: if one kick provably never fixes the TV, kicking
forever never will. Substitute `b` = PermanentDependency failure, `k` = retry:
the §46.2 rule — retry only *classified transient* failures — is this theorem.
For a permanent failure class, `b → [retry]b` holds in every state, hence
`b → [retry*]b`; a retry loop on a permanent error is not persistence, it is a
proven no-op with a resource bill. The failure classifier, not the retry
budget, carries the correctness burden — which is why §46.2 makes
classification the reviewed artifact.

---

## 5 · ACCEPTANCE ITEMS ARE HOARE TRIPLES — AND WHY GREEN ≠ VALID

Every packet acceptance item has the shape `p → [command]q`: fixture
precondition, declared command, expected postcondition. RED (Gate E) witnesses
`¬(p → [command]q)` **at one concrete state** — that is the entire meaning of
a witnessed red run, and why setup noise doesn't count: the witness must be a
state satisfying `p` where `q` fails after `command`, not a state where the
command never ran.

Dynamic logic's implication/inference distinction then says precisely what
GREEN means. `p → [a]q` being *true in the tested states* is not the same as
being *valid* (true in all states) — the article's own example:
`(x=1) → [x:=x+1](x=1)` is true in every state where `x≠1` and still invalid.
This is the formal spine under three Base laws at once:

```text
Test count ≠ legal correctness          (§3)   — truth at samples, not validity
"Tests passed" is insufficient           (Pipeline kernel #9)
The implementation is never the oracle   (kernel #7) — running [a] to learn q
                                          confuses the model with the spec
```

Where validity is actually achievable — the decidable Class D fragment — the
LIQUID annex discharges it. Everywhere else, the packet's honest claim is
"true at these states, with this refutation power (mutation score)."

---

## 6 · THE AGENT CAGE IS A BOX FORMULA — AND IT IS MODEL-CHECKABLE

Let `Σ` be the agent's permitted action alphabet (Pipeline §8.1 ALLOW list).
The cage is:

```text
[Σ*] (¬Merged ∧ ¬Signed ∧ ¬Published ∧ ¬ChannelMoved ∧ ¬SourceMutated)
```

— no sequence of permitted actions, of any length, reaches a forbidden state.
The DENY list makes the forbidden actions *not exist in Σ*; the box formula is
the check that no composition of allowed ones simulates them.

**Enforceable artifact 3 — model-check the process layer.** The packet state
machine (`DRAFT → … → HUMAN_INTEGRATED`, plus PAUSED/RESUME half-states) is a
finite Kripke structure with regular actions — this is **propositional**
dynamic logic, decidable (EXPTIME in general; trivial at this size). CI can
therefore *prove*, not review, the process obligations:

```text
[resume] (RESUME_VALIDATING ; (EXECUTING ∪ RESUME_BLOCKED))   — no silent third exit
[Σ*] (CRUCIBLE_PROVEN precedes HUMAN_INTEGRATED)              — gate order
[Σ*] (state = APPROVED → contract_digest is frozen)            — approval freeze
¬⟨Σ*⟩ (HUMAN_INTEGRATED without approvals for risk_class)      — cage completeness
```

A tiny checker over the pipeline's declared transition table (the table in
Pipeline §6 *is* the model) turns "the CI enforces the state machine" from a
promise into a proof, and every pipeline amendment re-checks automatically.
This closes the gap flagged at the pipeline's review: reviewer promises about
process order become machine-checked box formulas.

Scope honesty: PDL model checking verifies the **declared transition
system** — that the process design cannot reach a bad state. That the
*implementation* (CI scripts, branch protection) faithfully implements the
transition system remains a T-layer/review obligation. Design proof and
implementation proof are different boxes; claim each separately.

---

## 7 · THE MODAL OBLIGATION SCHEMA (packet extension)

Acceptance items gain one field:

```yaml
acceptance:
  - id: A1
    modality: box          # box | diamond
    claim: No archive entry escapes the extraction root
    discharge: property    # example | vector | property | proof | model-check
    mutation_required: true
  - id: A2
    modality: diamond
    claim: The golden bundle replays to the recorded canonical digest
    discharge: vector
```

CI rules (extends the LIQUID invariant-binding law):

```text
modality: box     ⇒ discharge ∈ {property, proof, model-check}
                    ∧ mutation_required: true
modality: diamond ⇒ discharge ∈ {example, vector}
box claim with only an example discharge  ⇒ packet-invalid
diamond claim advertised as universal in docs ⇒ truthful-docs failure (§31)
```

One schema field, and the taxonomy of §2 becomes enforced rather than
understood.

---

## 8 · THE LAYERED DECIDABILITY MAP

```text
Process / cage layer   PDL (propositional, regular actions)   → model-check, full proof
Class D data layer     linear rational arithmetic              → SMT, full proof (LIQUID)
General data layer     first-order dynamic logic               → UNDECIDABLE; sample
                                                                 (T2/T4) + mutate (T6),
                                                                 never claim proof
```

First-order dynamic logic — programs over unbounded data — is where proof
claims go to die; the Base's instinct to bound Class D and quarantine the rest
put every CanonFlow verification target in a decidable box *except* the one
that honest sampling already governs. The map above is the whole verification
strategy of the project on three lines.

---

## 9 · THE CONCURRENCY NOTE

Dynamic logic is superb for sequential composition and, per its own
literature, awkward for concurrency — where Pnueli's temporal logic (an
*endogenous* modal logic: one global situation evolving, conjunction as
parallel composition) is the standard tool. Read against the Pipeline: the
sequential executor, one-writer-per-seam, and DORMANT parallel agents are not
merely caution — they keep the project inside the logic that fits it. The wake
condition for parallel packet execution (Pipeline §8.2) therefore acquires a
formal rider: **waking concurrency means adopting temporal-logic obligations
(interference freedom, deadlock, fairness) that no amount of PDL box-checking
covers.** The concurrency plan the Pipeline demands in writing is, concretely,
that obligation set.

---

## 10 · IMPORT FILTER, STATUS, DELTAS

§46.7 self-test: DomainNeutral ✓ (actions/obligations exist identically for
EDIFlow) · Enforceable ✓ (three artifacts: collapse tests named in proof
manifests, induction-pair test design, modality field + CI rules; plus the
process model-checker) · Falsifiable ✓ (a modality-tagged suite that passes a
planted mutant misclassification fails) · NonDuplicative ✓ (names and
classifies existing T-layers; adds no new semantics) · ReducesAmbiguity ✓
("what does green mean" gets a per-item answer) · RespectsOwnership ✓
(Crucible/CI tooling; nothing in the kernel).

Status: `DESIGNED`. Wake order: (1) modality field + CI rules in the packet
schema — costs one YAML key, wake with `CFF-0001`; (2) collapse/star-collapse
naming in proof manifests — wake with train item 11; (3) process
model-checker — wake as a small R1 tooling packet once the Pipeline §6
transition table is frozen; (4) induction-pair test-design rule — wake with
the first batch/iteration packet (train item 9).

Deltas proposed: Pipeline §5.3 acceptance schema (+`modality`, `discharge`,
`mutation_required`) · Pipeline §13 CI checks (+`modality-valid`,
`process-model-check`) · Base §13 proof manifest (+collapse verification per
action family) · Base §14 test-layer table (+modal classification column) ·
Pipeline §8.2 parallel wake condition (+temporal-obligation rider) · LIQUID
annex §6 (invariant-binding absorbs the modality field as its discharge
vocabulary).

---

## 11 · FINAL FORM

```text
Example tests     witness   ⟨a⟩p        — reachability, fully proven by one run
Property tests    sample    p → [a]q    — truth at states, never validity
Proofs            discharge p → [a]q    — validity, on the decidable fragments
Mutations         measure   the suite's power to refute
Replay            checks    [a]p ↔ ⟨a⟩p — the kernel's membership card
The cage          is        [Σ*]¬Forbidden — and it model-checks
The custodian     decides   what p ought to say — no modality replaces this line
```

> The constitution was already written in dynamic logic.
> This annex only supplies the notation — and three places
> where the notation becomes a gate.
