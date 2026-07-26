# LIQUID_CANONFLOW.md — The Refinement and Proof Annex

> *A proof mechanizes a stated proposition. It never states the law.*
>
> Proposed annex to CANONFLOW_BASE.md, submitted through the §46.7 import
> filter and the §29 ADR process. Supersedes the pre-Base
> `liquid-canonflow-refinement-discipline.md`, which was written against the
> retired "restricted F# lane" architecture. The Base changed the ground:
> **Class D is the refinement dividend**, and this annex re-derives every
> Liquid Haskell lesson against it.

---

## 0 · POSITION LAW

```text
This annex adds one capability: bounded, decidable proof over Class D graphs,
Class P tables, and canonical refinements — at authoring and assurance time.

This annex adds zero authority:

Proven(proposition, graph) ⇏ LegalTruth(graph)          (§3 unchanged)
Proof ∉ VerdictPath                                      (§4 unchanged)
Proof ≠ signature ≠ test ≠ legal correctness             (§3 extended)
```

A machine proof joins the §3 list of things that are not legal correctness.
What it *is*: the strongest available mechanization of the oracle rank
"approved invariant or metamorphic relation" — it discharges the invariant
over the **entire input domain** instead of sampled points. The invariant's
statutory adequacy remains a human interpretation record.

What the old Liquid doc got right survives; what it bound to "restricted F#
generation" is re-bound to the Base's real substrate. Lanes are dead:
arbitrary-code packs are §11-REJECTED, so there is exactly one distributable
decision representation — the Class D finite acyclic graph — and it is
*better* for verification than the lane it replaced.

---

## 1 · THE THREE BINDING SITES (revised layer map)

| Refinement mechanism | Binding site | Base status |
|---|---|---|
| Phantom tags / erased units (`Refined<decimal, TaxableValue>`) | compile time | **SEALED** — already law in §7 and §46.1 |
| Private constructors, typed promotion path | construction time | **SEALED** — §46.1 boundary promotion is the smart-constructor law |
| Units of measure for dimensional money errors | compile time | **DELTA** — §2 below extends §5 |
| SMT proof over Class D / Class P / drift pairs | authoring + assurance time | **DELTA** — §§3–6 below, the core of this annex |

The honest summary from the pre-Base doc still holds, relocated: what Liquid
Haskell checks at compile time, CanonFlow checks partly in the F# type system,
partly at the §46.1 promotion boundary, partly in T2–T7 — **and now fully,
statically, over the Class D graph**, which was bounded (§11.2: finite,
acyclic, no recursion, allowlisted operators, decimal-only money) for security
and interpreter-cost reasons and turns out to have purchased decidability with
the same coin.

---

## 2 · UNITS-OF-MEASURE EXTENSION TO THE EXACT-MONEY LAW

§7 already permits "phantom tags or erased units." This annex makes one class
of them normative on the statutory monetary path:

```fsharp
[<Measure>] type inr
[<Measure>] type percent
[<Measure>] type fraction

// 0.18m<fraction> ≡ 18.0m<percent>; the type system forbids confusing them.
// taxable * rate      : decimal<inr> * decimal<fraction> → decimal<inr>   ✓
// taxable * 18m<percent>                                 → FS0001         ✗
```

The rate-applied-as-18-instead-of-0.18 defect is a real statutory bug class
that plain `System.Decimal` accepts silently. Under this extension it is a
compile error. Amendment to §5: every monetary policy's `scale · midpoint ·
aggregation` declaration also states the **dimension** (`inr`, `fraction`,
`percent`, per-unit price, quantity), and bare-dimension decimal arithmetic on
the verdict path is a T0/analyzer failure. Erased at runtime; zero AOT, Fable,
or serialization cost — wire formats continue to carry canonical decimal
strings.

---

## 3 · CLASS D PROOF LAW — what the graph's boundedness buys

A Class D graph is a finite acyclic decision structure over allowlisted facts
and operators, with decimal money and explicit uncertainty. That is a theory
of linear arithmetic over exact rationals, enumerated sorts, and epoch-day
integers — **decidable**. Every obligation below is therefore checkable by a
bounded solver run, and each one mechanizes a law the Base already states:

| Obligation | Proves, over the whole domain | Mechanizes |
|---|---|---|
| `VERIFY-TOTAL` | every point of the declared fact domain reaches an outcome; monetary conditions cover their ranges, not merely their branch list | §12 exhaustiveness analysis — upgraded from structural check to proof; the uncovered ₹0–₹2,49,999 band no branch mentions becomes a *witness*, not a hope |
| `VERIFY-DET` | no fact assignment satisfies two branches with different outcomes, or branch order is proven irrelevant | §12 contradiction detection — the blocking diagnostic now carries a concrete counterexample model |
| `VERIFY-UNKNOWN` | under §6 Kleene semantics, ∀ assignments where any required fact = Unknown ⇒ outcome ≠ Pass | §14.2 "missing required fact never becomes Pass" — currently a sampled property, now a universal per-pack certificate |
| `VERIFY-SEV` | the §6 severity/aggregation function cannot hide a Fail under any outcome combination | §14.2 "Fail cannot be hidden by weaker outcomes" |
| `VERIFY-BOUND` | threshold operators match pack parameters *including strictness* (`<` vs `≤` at ₹2,50,000) | §5 boundary law + §14.3's `> ↔ >=` mutation — proven, not sampled |
| `VERIFY-INTERVAL` | effective intervals across versions of one rule ID are disjoint; no date has two contradictory in-force versions | §10 temporal law, supersession chains |
| `VERIFY-EQUIV` | `evaluate(normalize(x)) = evaluate(x)` and `simplify` soundness *per pack*, as a certificate | §12 normalization laws and §34.5 optimizer laws — ADR-015 gains per-transformation equivalence certificates |
| `VERIFY-DRIFT` | for constraint pairs in the decidable fragment, the §34.2 implication directly: source admits ∧ target rejects ⇒ `StrictTarget`, etc. | ADR-016 — `ComplexOrUnknown` shrinks to the genuinely complex; the drift engine gains a proof backend with its honest fallback intact |

Two scope boundaries, stated so nobody over-claims:

```text
SMT proves the GRAPH.  T-layer tests and mutations prove that the
INTERPRETER and ENGINE faithfully execute the graph.  Different targets;
both remain mandatory.  A proven graph under a buggy interpreter is a
proven lie.

Class P first.  Rate-table range coverage and effective-interval
disjointness over parameter packs (VERIFY-BOUND/INTERVAL restricted to
tables) are trivially decidable and deliver value at Gate 6 — before the
Class D interpreter ever wakes.
```

---

## 4 · SOLVER PLACEMENT LAW

The solver is a Studio/Crucible instrument. It is constitutionally caged:

```text
Solver ∈ {Studio authoring diagnostics, Crucible assurance gates}
Solver ∉ VerdictPath                    (§4: pure managed F#/.NET only)
Solver ∉ CFF reader, GSTFlow runtime, or any shipped application (§32)
Solver = native peripheral ⇒ helper process, §23 crash containment
Solver version + checksum ∈ toolchain manifest (§43.1)
Solver run = bounded (time, memory) + pinned deterministic configuration
```

Proof outcomes form an algebra, never a Boolean:

```fsharp
type ProofOutcome =
    | Proven         of certificate: ProofArtifact       // unsat core / proof log
    | Refuted        of witness: CounterexampleModel     // concrete fact assignment
    | Unproven       of reason: UnprovenReason           // Timeout | OutsideFragment
                                                          // | EncodingGap of string
```

Laws:

```text
Timeout ⇒ Unproven, recorded — never a hang, never a silent pass
Unproven ⇒ the existing sampled gate (T2/T4/T6) remains the gate;
           status vocabulary stays honest per §31
Refuted  ⇒ blocking diagnostic; the witness is shown to the author
Proven   ⇒ certificate digest enters the §13 proof manifest
```

Counterexample witnesses are **Derived-grade** facts (§3): reproducibly
calculated, derivation and solver version recorded. A `VERIFY-TOTAL` witness —
the exact uncovered band — flows into the §15.3 CandidateCase pipeline as a
proposed boundary case. The *facts* are Derived; the **expected outcome** for
that case is still authored and reviewed by humans. The solver finds the hole;
it never decides what belongs in it.

---

## 5 · ENCODER SOUNDNESS LAW — the annex's own landmine

The proof is exactly as good as the `Graph → SMT` encoding. The encoder is
therefore a projection under §34.1 and gets the full treatment:

```text
encoder version pinned in toolchain manifest
encoding fidelity classified per operator (Lossless | Unrepresentable)
allowlisted operator without an exact encoding ⇒ that graph is
    Unproven(OutsideFragment) — never approximately encoded
```

And a mandatory **differential agreement suite**: sample fact assignments
(including every solver-produced witness), evaluate through the real Class D
interpreter and through the solver's model of the graph, and require exact
agreement. Encoder mutation tests (drop a conjunct, flip an inequality,
mis-scale a decimal) must all be caught by this suite. An encoder defect is a
soundness incident, ranked with §46.5 nondeterminism incidents — because a
wrong `Proven` is worse than no proof at all.

New landmine-register entries:

| Landmine | Countermeasure |
|---|---|
| Encoder unsoundness makes false `Proven` | classified encoding fidelity + differential agreement + encoder mutations |
| Solver nondeterminism or version drift | pinned version/checksum/config; outcome recorded with toolchain digest |
| `Proven` label read as legal truth | §0 position law; §3 wording; UI/docs say "invariant proven," never "correct law" |
| Solver becomes a runtime dependency | §4/§32 placement law; CI asserts shipped artifacts have no solver linkage |

---

## 6 · INVARIANT-BINDING LAW (spec-first, Base edition)

The pre-Base "CF-DOC-001" rule, restated in Base vocabulary: every invariant
stated in a profile's interpretation record, a canonical type's documentation,
or a pack's review notes **names its discharging checker**:

```text
Invariant ↦ discharge ∈ { T2 boundary family, T4 property (seeded),
                          T5 metamorphic, T6 mutation kill, VERIFY-* proof }
```

An invariant with no named discharge is itself a blocking finding at profile
approval (§34.3) and a `Contract` gap under §37. Documentation thereby becomes
the table of contents of the evidence bundle rather than testimony beside it.
This is the Liquid Haskell haddock→refinement pipeline with the Base's own
oracle discipline attached.

**Assumption ledger**, recast per §3: any invariant deliberately left at
sampled confidence when a proof was plausible, or any trusted-but-unproven
lemma, becomes a **Declared-grade** entry with an accountable declarant:

```fsharp
type Assumption =
    { Id        : AssumptionId
      Claim     : string                 // the proposition trusted
      Declarant : Custodian              // §3: Declared names its declarant
      StandIn   : DischargeRef           // the T4/T2 checker holding the fort
      ReviewBy  : LocalDate }
```

`StandIn` is non-optional — an assumption with no falsifier fails the evidence
lint. Ledger ships inside the §13 proof manifest. This encodes the honest LH
move ("the disease isn't worth the medicine") as auditable, expiring debt.

---

## 7 · AUTHOR-AGNOSTIC GATE LAW

The proof obligations bind the **artifact**, not its author. A Class D graph
drawn by a CA in Studio, proposed by the AI candidate pipeline (§15.3), or
produced by the local forge model faces identical `VERIFY-*` obligations.
Consequence for §15.3: "schema/static validation" of an AI proposal now
includes the proof battery, which means an AI-proposed graph arrives at human
review carrying either certificates or witnesses — reviewers spend attention
on statutory meaning, not on hunting coverage holes a solver finds in
milliseconds. This *narrows* AI authority rather than expanding it: one more
mechanical gate between proposal and approval.

---

## 8 · IMPORT-FILTER SELF-TEST (§46.7)

```text
DomainNeutral        ✓  decision graphs, parameter tables, drift pairs exist
                        in EDIFlow (850 profiles) exactly as in GST
Enforceable          ✓  CI gates with typed outcomes; no judgment calls
Falsifiable          ✓  encoder differential suite; known-good/known-bad
                        graph benchmark (below)
NonDuplicative       ✓  mechanizes §5/§6/§10/§12/§14/§34 laws already stated;
                        adds no new semantic law
ReducesFailure       ✓  shrinks ComplexOrUnknown; converts sampled universals
                        (§14.2) into certificates; witnesses feed the corpus
RespectsOwnership    ✓  lives in Studio/Crucible; Core promotion deferred
                        until a second Flow uses it unchanged (§2)
```

Rejected inheritances from the pre-Base doc, for the record: the three-lane
model (§11 already decided it — Class D or nothing), Writer-monad telemetry
(§46.7 REJECT stands; evidence contract over abstraction), and any phrasing in
which a passing proof "guarantees soundness" (§3, §31).

---

## 9 · STATUS AND WAKE CONDITIONS

This entire annex enters as `DESIGNED`. Wake schedule, gate-aligned:

```text
Gate 6  · VERIFY-BOUND/INTERVAL over Class P tables       (first live value:
          rate-map coverage + effective-interval disjointness, no interpreter
          dependency)
Gate 7  · full Class D battery, wired into Studio diagnostics
          (missing-branch and contradiction reports become witness-backed)
          and `crucible prove-graph`
Gate 7+ · VERIFY-DRIFT backend for ADR-016; VERIFY-EQUIV certificates
          for ADR-015
```

Promotion `DESIGNED → EXPERIMENTAL` requires: pinned solver + encoder in the
toolchain manifest; the differential agreement suite green; and the
classification benchmark — **a corpus of known-proven and known-refuted graphs
(mutation-manufactured) classified with zero false `Proven`**. False
`Unproven` is tunable friction; false `Proven` is disqualifying. Promotion to
`PROVEN` follows §31 unchanged: one real profile family carried end-to-end
with certificates in its shipped proof manifest, plus independent contact.

---

## 10 · PROPOSED AMENDMENTS (each an ADR candidate)

1. **§5** — dimension declaration joins scale/midpoint/aggregation; UoM
   normative on the verdict monetary path.
2. **§12** — Studio exhaustiveness/contradiction diagnostics: witness-backed
   where the proof backend is awake; structural analysis remains the fallback.
3. **§13** — Crucible gains `crucible prove-graph`; proof manifest gains
   solver/encoder digests, per-obligation outcomes, certificate digests, and
   the assumption ledger.
4. **§14.2** — VERIFY-UNKNOWN/SEV certificates recorded per pack where
   available; sampled properties remain mandatory (interpreter target).
5. **§15.3** — AI candidate validation includes the proof battery;
   solver witnesses enter CandidateCase as Derived facts with human-authored
   expected outcomes.
6. **§34.2** — drift engine accepts a proof backend; `ComplexOrUnknown`
   reserved for genuinely undecidable or timed-out pairs.
7. **§34.3 / §37** — invariant-binding law: undischarged documented invariant
   blocks profile approval.
8. **§38** — four new landmines from §5 of this annex.
9. **§43.1** — solver + encoder versions/checksums join the toolchain
   manifest; proof outcomes join the release evidence set.

---

## 11 · FINAL FORM

```text
Type system   proves shape        at compile time
Promotion     proves construction at the boundary          (§46.1)
Tests         prove behavior      at sampled points        (T0–T12)
Mutations     prove the tests     against seeded defects   (T6)
Solver        proves invariants   over the whole domain    (this annex)
Custodian     approves meaning    — and nothing above replaces this line
```

> The graph was bounded to keep packs from becoming malware.
> The same bound makes them provable.
> Take the dividend — and never let a certificate speak in the law's voice.
