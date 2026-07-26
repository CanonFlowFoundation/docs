# CANON_DYNAMIC_CRUCIBLE.md — CDC: Dynamic Logic as Executable Testing Law

> *After every permitted sequence of transformations, the CanonFlow laws
> still hold — and when they do not, the Crucible returns the smallest
> replayable trace showing exactly where truth was lost.*

- **Status:** `DESIGNED` — engine specification; the laws it encodes are
  buildable today as plain properties (§17's discipline)
- **Snapshot:** source review 2026-07-17 · cherry-pick pass 2026-07-18
- **Authority:** subordinate to `CANONFLOW_BASE.md`; executes under
  `CFF_PIPELINE.md`; extends `DYNAMIC_CFF_TESTING.md` (that annex is the
  **vocabulary**; this document is the **engine**)

Sections marked **▲** are cherry-pick corrections or doc-set bindings.

---

## 0 · POSITION AND NAMING CORRECTION

▲ The source draft named this "CFF Dynamic Crucible / CFDL" with a
companion "CFRL" refinement layer and a "CFFAssurance" equation. **CFF is
CanonFlow Format. Only. Ever** (Pipeline §0.1; manifesto vocabulary). The
component is renamed before it exists:

```text
CDC  = Canon Dynamic Crucible        (this engine)
CDL  = Canon Dynamic Law             (one governed law record, §13)
CRL  = Canon Refinement Law          (the local-contract layer, §16)
Law IDs: CANON.* for engine laws, GST.* for statutory laws
         (source draft's CFF.PACK.001 → CANON.PACK.001)
```

The assurance stack:

```text
CanonAssurance = Types ⊕ Refinements ⊕ DynamicLaws ⊕ Crucible ⊕ Review
```

Relation to the existing annex: `DYNAMIC_CFF_TESTING.md` supplied the
taxonomy (box/diamond obligations, the collapse law, the modality schema
field, the layered decidability map) and three cheap artifacts. CDC is the
layer that eventually **discharges** those obligations mechanically. The
annex's `discharge: model-check` vocabulary resolves to CDC results.

---

## 1 · EXACT ROLE

| Mechanism | Question answered |
|---|---|
| F# types | Can this state be represented? |
| CRL refinements | Does this function satisfy its local contract? |
| **Dynamic logic (CDC)** | **What remains true after actions and action sequences?** |
| FsCheck | Can generated examples falsify the law? |
| Z3 | Can symbolic constraints find a counterexample? |
| Human review | Does the formal law correctly represent GST authority? |

CDC does not replace normal F# testing. It is the algebra that describes
**which traces the Crucible must explore**. It fills the gap between a
refinement contract proving one function locally, Crucible tests checking
complete sequences, and statutory review deciding whether the encoded law
is correct.

Low value: `calculateTax : Rate -> Money -> Money` — a refinement contract
suffices. High value: protocols (`Ingest → Normalize → Validate → Evaluate
→ Explain`), lifecycles (`Generate → Compile → Repair* → Test → Review →
Sign`), rule-pack promotion, effective-date selection, evidence
accumulation, normalization idempotence, host parity, repair loops,
prohibition of unauthorized signing, source preservation, deterministic
replay.

---

## 2 · SEMANTICS

`⟦a⟧ ⊆ State × State`. Then `[a]p`: every terminating state reachable by
`a` satisfies `p` — a **universal safety obligation**. `⟨a⟩p`: at least
one execution reaches a state satisfying `p` — a **witness/reachability
obligation**. (Standard relational PDL interpretation.)

---

## 3 · NATIVE F# ACTION ALGEBRA

CDC owns its action model rather than depending on an experimental
third-party state-machine API:

```fsharp
type Transition<'state> =
    | Disabled
    | Completed of Set<'state>
    | Faulted of CanonError
    | Diverged

type AtomicAction<'state> =
    { Name    : ActionName
      Enabled : 'state -> bool
      Execute : 'state -> Transition<'state> }

type Program<'state> =
    | Block
    | Skip
    | Action   of AtomicAction<'state>
    | Test     of name: string * predicate: ('state -> bool)
    | Sequence of Program<'state> * Program<'state>
    | Choice   of Program<'state> * Program<'state>
    | Repeat   of RepeatPolicy * Program<'state>

type RepeatPolicy =
    | Bounded   of maxSteps: int
    | Inductive of invariant: PredicateId

type Formula<'state> =
    | Atom    of name: string * predicate: ('state -> bool)
    | Not     of Formula<'state>
    | And     of Formula<'state> list
    | Or      of Formula<'state> list
    | Implies of Formula<'state> * Formula<'state>
    | Box     of Program<'state> * Formula<'state>
    | Diamond of Program<'state> * Formula<'state>
```

A deliberately small PDL-inspired subset — never an attempt at a general
dynamic-logic theorem prover. ▲ Ownership rationale is Base §32 verbatim:
a dependency must remove more complexity than it adds, and experimental
APIs stay behind adapters (§11).

---

## 4 · THE RESULT ALGEBRA — NEVER A BOOLEAN

```fsharp
type DynamicCheckResult<'state> =
    | VerifiedFinite       of exploredStates: int
    | VerifiedSymbolically of ProofCertificate
    | PassedSamples        of count: int * seed: Seed
    | WitnessFound         of Trace<'state>
    | Refuted              of CounterexampleTrace<'state>
    | NoWitnessWithinBound of exploredStates: int
    | Inconclusive         of InconclusiveReason
```

| Result | Meaning |
|---|---|
| `VerifiedFinite` | all states of a completely enumerated finite model passed |
| `VerifiedSymbolically` | proved relative to the symbolic model |
| `PassedSamples` | generated testing found no counterexample |
| `WitnessFound` | a diamond formula has a concrete witness |
| `Refuted` | a concrete failing trace exists |
| `NoWitnessWithinBound` | no witness within the search limit — **not** nonexistence |
| `Inconclusive` | timeout, unsupported construct, incomplete model |

**`PassedSamples` must never be relabelled `Proved`.** ▲ Reconciliation
with the LIQUID annex's `ProofOutcome`: `VerifiedSymbolically ↔
Proven(certificate)`, `Refuted ↔ Refuted(witness)`, `Inconclusive ↔
Unproven(reason)` — one honesty algebra, two backends; and the shared law
is §31 truthful status applied to test results. The DYNAMIC annex's
modality field maps directly: a `box` obligation is discharged by
`VerifiedFinite | VerifiedSymbolically` (or honestly reported at
`PassedSamples` strength); a `diamond` obligation by `WitnessFound`.

---

## 5 · STATUTORY DYNAMIC LAWS

**Missing evidence must never produce Allowed** — `MissingEvidence →
[Normalize;Evaluate] Verdict = Unknown`:

```fsharp
let missingEvidenceNeverAllows =
    dynamicLaw {
        id "GST.EVIDENCE.001"
        source [ SourceRef.internalLaw "CANON-UNKNOWN-001" ]
        given (fun s -> s.Input.RequiredEvidenceMissing)
        after (action Normalize >>> action Evaluate)
        mustHold (fun s -> s.Result.Verdict = Unknown)
    }
```

The Crucible generates missing-evidence inputs, runs the **real**
normalizer and evaluator, and records a counterexample trace if anything
becomes Allowed. ▲ This is Base §14.2's "missing required fact never
becomes Pass" — the same proposition LIQUID's `VERIFY-UNKNOWN` proves over
Class D graphs; CDC checks it over the *whole executable protocol*,
interpreter included. Graph proof and protocol exploration are complements
(LIQUID §3's scope boundary), not duplicates.

**Effective-date selection** — `[SelectPack(asOf)] EffectiveFrom ≤ asOf <
EffectiveUntil` (`CANON.PACK.001`): directly tests amendment boundaries.

**Normalization idempotence** — `[Normalize;Normalize] C₂ = C₁`
(`CANON.NORMALIZE.001`), with a state that retains both observations
(`First`/`Second` options) — the DYNAMIC annex's star-collapse obligation,
now executable.

**Decisive verdict stability** — `Decisive(v) ∧ Irrelevant(e) →
[AddEvidence(e);Evaluate] SameVerdict(v)`. Fits the information lattice
`Unknown ⊑ Allowed`, `Unknown ⊑ Blocked`: relevant information may refine
Unknown; irrelevant information must never flip a decisive verdict. ▲ This
generalizes `VERIFY-META` and the metamorphic T5 layer into a lattice law.

---

## 6 · GOVERNANCE LAWS — THE CAGE, EXECUTABLE

The DYNAMIC annex stated the cage as `[Σ*]¬Forbidden` and proposed a tiny
model checker. CDC is that checker grown up. With
`U = Generate ∪ Compile ∪ Repair ∪ Test`:

```text
No automatic signing        ¬Approved → [U*] ¬Signed
                            only [Approve;Sign] Signed produces the state
Failed gate stays failed    GateFailed → [(Repair ∪ Submit)*] ¬Promoted
                            until a fresh successful evaluation occurs
Contracts cannot be weakened ContractLocked → [ModelRepair*] Contract' = Contract
                            implementation may change; approved refinements,
                            Gold tests, source bindings may not
```

Any number and ordering of generation, repair, and testing actions can
never produce a signed artifact — as a *checked formula over the declared
transition system*, not a review comment. ▲ Scope honesty carries over
from the annex: this proves the **design**; that CI/branch protection
faithfully implements the design remains a T-layer obligation.

---

## 7 · AXIOMS AS TEST-EXPANSION RULES

The PDL axioms compile laws into Crucible work:

```text
[a;b]p ↔ [a][b]p      Sequence: legal initial state → run a → retain
                       intermediate evidence → run b → check p → keep the
                       full trace
[a∪b]p ↔ [a]p ∧ [b]p   Choice: a universal law over a choice REQUIRES both
                       branches — SelectNativeHost ∪ SelectFableHost never
                       means "randomly pick one" under a Box
[a*]p                  Iteration, two modes:
                       bounded    → Repeat(Bounded n, a) as executable evidence
                       unbounded  → induction: p ∧ [a](p) ⇒ [a*]p, requiring
                                    (1) invariant before, (2) one step
                                    preserves, (3) termination separately
                                    established when required
```

▲ The choice rule is the conformance-kit law (Base §34.4) as an axiom, and
the induction rule is the DYNAMIC annex's induction-pair test design with
its proof-strength upgrade path.

---

## 8 · THE VACUOUS-TRUTH GUARD

`[BLOCK]p` is true because BLOCK has no terminating successor — logically
correct, engineering poison: a rule could "pass" because the action never
ran. Therefore **every required action carries a separate executability
obligation**:

```text
Enabled(s, a) → ⟨a⟩ true
```

and total deterministic Canon actions return
`Completed of 'state | Rejected of TypedRejection` — no silent divergence,
no empty successor set. Reports explicitly detect: disabled action · zero
explored transitions · divergence · timeout · **vacuous Box success**.

▲ This is Base §14.3's `if unsupported then true` vacuous-test law given a
formal name and a mechanical detector — and §17's reward rule depends on
it: making an action unreachable to win a vacuous Box is a rejected-reward
condition, not a pass.

---

## 9 · BOX VERSUS DIAMOND IN TESTING

**Box → adversarial safety tests.** `[EveryPermittedPackSelection]
EffectivePackSelected`: the Crucible *tries to violate* the proposition;
one counterexample refutes.

**Diamond → witness tests.** `⟨Generate;Repair≤3;Submit⟩
VerifiedCandidate`: the Crucible *searches for* one successful path;
failure yields `NoWitnessWithinBound`, which never proves nonexistence.
Diamond also audits reachability of supposedly reachable states:
`⟨AddMissingEvidence;Evaluate⟩ Unknown` — is the Unknown outcome actually
producible, or is the safety law passing vacuously?

---

## 10 · FSCHECK ADAPTER

FsCheck provides state generators, action-input generators, trace
generators, counterexample shrinking, replayable seeds. Its model-based
API is documented as **experimental** — therefore:

> CDC owns `Program`, `Formula`, `Trace`, and `DynamicLaw`; FsCheck is
> consumed through an adapter for generation and shrinking only.

```fsharp
type DynamicTestAdapter<'state> =
    { InitialStates   : Gen<'state>
      ActionArguments : ActionName -> Gen<obj>
      ShrinkState     : 'state -> seq<'state>
      ShrinkTrace     : Trace<'state> -> seq<Trace<'state>> }
```

Constitutional semantics never depend on a changing testing API.

---

## 11 · THE COUNTEREXAMPLE TRACE

Every failure produces a replayable artifact:

```text
Law: GST.EVIDENCE.001
Formula: MissingEvidence → [Normalize; Evaluate] Verdict = Unknown
Initial state: InvoiceAmount ₹10,000 · SupplierGSTIN present
               · RequiredUsageEvidence absent
Trace: 1. Normalize → canonical invoice created
       2. Evaluate  → rule GST.ITC.FOOD.001 → Verdict: Allowed
Violation: missing required evidence produced a decisive Allowed verdict
Minimal counterexample: Evidence=[] · Category=FoodAndBeverage
                        · Usage=FurtherSupply
Seed: 528193…        Pack digest: sha256:…
```

It enters the Crucible evidence bundle. ▲ **Forge feedback (dormant):**
`candidate F# + dynamic law + minimal trace + violated postcondition →
corrected implementation` is a stronger training example than a compiler
error — it teaches *semantic* repair. These accumulate for the dormant
Forge mission (Pipeline §0.1); when it wakes, the model may never edit the
law, action semantics, exploration bounds, hidden traces, approved
sources, or the required postcondition, and reward is **rejected** when a
law is weakened or an action made unreachable for vacuous Box success.

---

## 12 · THE GOVERNED LAW RECORD

```fsharp
type DynamicLaw<'state> =
    { LawId         : LawId
      Description   : string
      Sources       : SourceRef list
      Preconditions : Formula<'state>
      Program       : Program<'state>
      Postcondition : Formula<'state>
      Exploration   : ExplorationPolicy
      Severity      : Severity
      Owner         : CustodianRole }

type ExplorationPolicy =
    | ExhaustiveFinite
    | PropertyBased    of tests: int
    | BoundedModelCheck of depth: int
    | Symbolic
    | Inductive        of invariant: PredicateId
```

**The exploration policy prevents claims stronger than the executed
method** — the record's own §31 clause. ▲ `Sources` binds every law to
authority (a governance law cites the Base section; a statutory law cites
`gstref://` IDs from the CFF profile's source registry), so a dynamic law
is never free-floating formalism.

---

## 13 · PRODUCT-STATE AGREEMENT TESTING

`[Native ∪ Fable] Correct` checks each host independently and **compares
nothing**. Agreement needs a product state:

```fsharp
type HostAgreementState =
    { Input        : CanonicalInput
      NativeResult : Verdict option
      FableResult  : Verdict option }

// [EvaluateNative; EvaluateFable] Equivalent(NativeResult, FableResult)
```

▲ The same construction covers, with one pattern: previous vs new
rule-pack versions (impact reports) · reference vs optimized
implementation (§34.5 optimizer laws) · **Canon IR interpreter vs
generated F#** — the LIQUID annex's round-trip extensional-agreement gate
is precisely a product-state box law · BF16 vs quantized model candidate
evaluation (Seal §9's per-representation re-eval, when the Forge wakes).
One law shape, five agreement surfaces the doc set already demanded.

---

## 14 · CONCURRENCY BOUNDARY

Dynamic logic is elegant for sequential composition and wrong as the sole
tool for asynchronous scheduling, interference, and fairness. The verdict
core stays pure and sequential; concurrent infrastructure (CI services,
queues, pack publishing) uses a separate systematic-concurrency layer —
Microsoft Coyote can explore controlled .NET task schedules and reproduce
failing traces, and its own documentation says plainly that this is
systematic testing, **not theorem proving**.

```text
CDC              → deterministic protocols
Coyote / temporal → concurrent infrastructure
```

Never contaminate the pure verdict core with concurrency machinery. ▲ This
instantiates the DYNAMIC annex §9 rider: the "written concurrency plan"
the Pipeline demands before parallel work = temporal obligations + Coyote
exploration evidence.

---

## 15 · CRL ⊕ CDL COMPOSITION

```text
CRL (local):   Pre(s) → [a] Post(s')
               money non-negative · decisive verdict carries evidence
               · recursion terminates
CDL (global):  Pre(s) → [a;b;c*] Post(s')
               normalize-then-evaluate preserves provenance · no number of
               AI repairs signs a pack · selection-then-evaluation uses
               the effective rule

Harness = FCS ⊕ ASTInquisitor ⊕ CRL ⊕ CDL ⊕ Z3 ⊕ FsCheck ⊕ Sandbox ⊕ Crucible
```

---

## 16 · IMPLEMENTATION PATH (gate-aligned)

```text
Phase 1  Executable algebra: Action, Sequence, Choice, bounded Repeat,
         Box, Diamond, trace recording
Phase 2  FsCheck adapter: generated states/choices, shrinking, seed
         replay, boundary-focused generators
Phase 3  First laws: missing-evidence · normalization idempotence ·
         effective pack selection · deterministic replay · source
         preservation · no automatic signing · contract immutability
         during repair · Native/Fable agreement
Phase 4  Symbolic backend: supported subset → Canon Proof IR → Z3
Phase 5  Inductive repetition: invariant proofs for unbounded a*, with
         bounded exploration retained as executable evidence
```

▲ **Laws before engine — the anti-Pugazh clause.** CDC is `DESIGNED` and
stays outside the CFF v1 freeze (it is a new assurance surface). But six
of the eight Phase-3 laws are expressible **today** as ordinary FsCheck
properties with the DYNAMIC annex's modality tags — normalization
idempotence, deterministic replay, and source preservation are literally
CFF v1 train obligations (items 6–8, 12). Write them as plain properties
now; CDC's wake condition is CFF v1 `PROVEN`, and its first deliverable is
refactoring those existing properties into `DynamicLaw` records **without
changing what they check** — a product-state agreement test between the
old suite and the new engine, naturally.

---

## 17 · IMPORT FILTER AND DELTAS

§46.7: DomainNeutral ✓ (protocol laws exist identically for EDIFlow) ·
Enforceable ✓ (result algebra + exploration policy are CI-checkable) ·
Falsifiable ✓ (vacuous-Box detector; planted-violation fixtures) ·
NonDuplicative ✓ (extends the DYNAMIC annex; discharges its obligations
rather than re-stating them) · ReducesFailureOrAmbiguity ✓ (per-result
strength labels; the vacuity detector) · RespectsOwnership ✓
(Crucible-side; nothing in the verdict path; Core promotion deferred to
second-flow use).

Deltas: DYNAMIC annex §7 discharge vocabulary → resolves to
`DynamicCheckResult` constructors · DYNAMIC annex §6 model-checker →
subsumed by CDC Phase 1 over the same transition table · LIQUID annex §4
`ProofOutcome` → mapping table recorded (§4 here) · Pipeline §0.1
terminology table → CDC/CDL/CRL entries added; "CFDL/CFRL/CFFAssurance"
retired on arrival · Base §13 Crucible commands → future
`crucible run-dynamic` (dormant) · Base §14 layers → CDL laws recorded as
the T4/T5 upgrade path · Forge dossier → counterexample-trace corpus
listed among its wake-time assets.

---

## 18 · CONSTITUTIONAL INSERTION

```text
[a]p    = every permitted terminating execution of a must establish p
⟨a⟩p    = at least one permitted execution of a must witness p

Accept(system) = LocalContracts ∧ DynamicSafety ∧ RequiredReachability
               ∧ DeterministicReplay ∧ CrucibleEvidence
```

Dynamic logic gives CanonFlow a precise language for saying not only
"this output is valid," but: after every permitted sequence of
transformations, the laws still hold — and when they do not, the Crucible
returns the smallest replayable trace showing exactly where truth was
lost.
