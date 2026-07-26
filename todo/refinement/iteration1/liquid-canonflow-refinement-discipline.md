# Liquid CanonFlow: Refinement Discipline Without Liquid Haskell

**Fourth companion — blends the Liquid Haskell / Diff case study (Tweag) into the CFF stack.**
*F# has no Liquid Haskell. This document maps every LH capability onto the mechanism CFF already owns — and identifies the one place where genuine SMT verification is affordable.*

---

## 0. The transferable thesis

The case study's central line is CFF's founding thesis said in Haskell:

> "It is by a careful threading of logic that a program is built into existence; the critical aspects lie within a theory in its writer's mind, which tends to be lost across iterations, updates, refactors and people moving on."

That is the context-loss problem — the same one provctl, the provenance manifests, and CanonFlow itself exist to solve. LH's answer is a pipeline: **document the invariant → encode it as a refinement → check it mechanically**. CFF adopts the pipeline; only the checking substrate differs. And LH's removability principle ("the program still compiles with LH disabled") holds by construction in CFF: every check is external to the shipped artifact — the rule pack stays data.

---

## 1. The layer map — where each LH capability lands in CFF

| LH capability | CFF mechanism | Binding time |
|---|---|---|
| Refinement on values (`{v:Int \| v ≥ 0}`) | Smart constructor + private ctor returning `Result` | Construction time |
| Refinement type aliases (`WaveFront D`) | Phantom-typed wrappers over validated data | Compile + construction |
| Dimensional/unit confusion | **F# units of measure** (§3) | Compile time, zero cost |
| Prelude partiality bans (`head`) | CF-* policy analyzer (Seal §6) — partial functions on the deny list | Analysis time |
| Termination metrics | **Lane 1: termination by construction** (§5) | Grammar |
| Pre/post-condition specs | Documented invariant ↔ FsCheck property, paired (§4) | Test time |
| SMT-verified predicates | **Z3 over Canon IR** (§2) — the real prize | Promotion time |
| `assume` / `ignore` / `lazy` escape hatches | Assumption ledger (§6) — harness code only | Governance |
| Lemmas in dead bindings | Lemma→property demotion rule (§6) | Test time |

The honest summary: what LH checks at compile time, F# checks partly at compile time (types, UoM), partly at construction time (smart constructors), partly at test time (FsCheck) — **and fully, statically, at the IR level**, which is where CFF cashes the SMT check LH made look desirable.

---

## 2. The prize: Z3 over Canon IR — the Lane 1 dividend

LH works because its predicates are simple enough for an SMT solver. Arbitrary F# is not — but **the Restricted subset (Seal §2, Lane 1) was bounded for exactly this shape of payoff**: comparisons over `Money` and `LocalDate`, bounded DUs, approved combinators, no recursion. That grammar is SMT-decidable. So the Crucible gains a formal gate family, **CF-VERIFY**, discharged by Z3 against the extracted IR:

| Check | Statutory meaning |
|---|---|
| CF-VERIFY-TOTAL | Every point of the input domain reaches a verdict — not just DU-case completeness, but *range* completeness: monetary conditions cover (0, ∞), date conditions cover the effective interval. FS0025 catches missing constructors; Z3 catches the uncovered ₹0–₹2,49,999 band nobody wrote a branch for. |
| CF-VERIFY-DET | No input satisfies two branches yielding different verdicts (or branch order is proven irrelevant) — the rule is a function, not a race. |
| CF-VERIFY-BOUND | Threshold literals align with pack-supplied authority *including strictness*: `≤ 250000` vs `< 250000` is a one-character statutory bug that no test at ±1 rupee granularity is guaranteed to plant. Z3 checks the inequality itself. |
| CF-VERIFY-INTERVAL | Effective intervals across versions of the same RuleId are disjoint — no date where two contradictory rule versions are simultaneously in force. |
| CF-VERIFY-META | Metamorphic obligations (irrelevant evidence ⇒ unchanged verdict) proven over the whole domain, not sampled. |

Encoding notes: `decimal` → exact rationals (decimals *are* rationals — no float approximation in the solver); `LocalDate` → epoch-day integers; DUs → enumerated sorts.

**Chain-of-trust caveat, stated plainly:** Z3 verifies the **IR**. Its verdicts speak about the F# only through the round-trip gate — FsCheck extensional agreement `∀ input. original ≡ reinterpreted` (harness §7). The promotion conjunction is therefore: *(F# ≅ IR, tested)* ∧ *(IR ⊢ properties, proven)*. Neither claim alone is the guarantee; and per the Seal, even both together prove admissibility, not law.

Where FsCheck samples, Z3 exhausts. Keep both: properties run on the F# (catching extraction bugs), Z3 runs on the IR (catching domain gaps sampling misses).

---

## 3. Units of measure — the static check F# gives away free

LH refines `Int` to `Nat`; F# refines `decimal` dimensionally at zero runtime cost:

```fsharp
[<Measure>] type inr
[<Measure>] type percent
[<Measure>] type fraction    // 0.18<fraction> = 18.0<percent>; never confuse them again

let gstRate : decimal<fraction> = 0.18m<fraction>
let taxable : decimal<inr>      = 1_00_000m<inr>
let tax     : decimal<inr>      = taxable * gstRate     // fraction × inr → inr ✓
// taxable * 18m<percent>  →  compile error: decimal<inr percent>
```

The percent-vs-fraction confusion is a *real statutory bug class* — a rate applied as 18.0 instead of 0.18 compiles happily as bare `decimal`. UoM makes it FS0001. **Delta: CanonFlow primitives adopt UoM (`Money = decimal<inr>` internally, rates as `decimal<fraction>`); CF-NUM-001 extends to reject bare-decimal arithmetic in monetary positions.** This is the closest thing F# has to a refinement type that costs nothing and cannot be bypassed.

---

## 4. Spec-first triples — haddocks the compiler takes to heart

The case study's `LineRange` invariant maps one-to-one onto `EffectiveInterval`:

```fsharp
/// The following invariants hold:
///   interval.Until = None  ∨  interval.Until ≥ Some interval.From
///   (open intervals model in-force law)
/// Enforced: constructor private; EffectiveInterval.create returns Result.
/// Checked:  prop_intervalOrdering (FsCheck) · CF-VERIFY-INTERVAL (Z3, cross-version)
type EffectiveInterval = private { From : LocalDate; Until : LocalDate option }
```

The discipline, sealed: **every documented invariant names its checker.** A doc comment stating a property that no FsCheck property or CF-VERIFY obligation covers is itself a policy violation (new rule: **CF-DOC-001** — undischarged documented invariant). This turns documentation from testimony into a table of contents for the evidence bundle.

Forge-side consequence: the teacher emits **triples** — spec comment + implementation + properties — as one artifact, and the harness checks the correspondence (property names ↔ documented invariants). The Type A corpus is re-tagged accordingly; the LoRA learns that a rule *is* its spec, code, and proof obligations together, not code alone.

---

## 5. Termination — solved by grammar, not by metrics

LH proves termination with lexicographic metrics (`/ [len hunk, 0]`) because Haskell lets you write anything. CFF's lanes dissolve the problem for generated code:

- **Lane 1 (Restricted)**: no general recursion in the grammar; approved combinators are folds over finite structures. Termination is **by construction** — nothing to prove, nothing for the model to get wrong. This is now an explicit *justification* of the lane choice, alongside IR extractability and SMT decidability: three dividends from one restriction.
- **Harness internals** (human-written): where mutual recursion appears, adopt the case study's pattern as convention — state the decreasing measure in the doc comment (`/// decreases: [len attempts, phase]`), and back it with a fuel parameter or a property test on the recursion's step function. The repair loop already complies: it is a fold over an attempt stream with a stopping predicate (harness §1), i.e. structurally terminating.

The case study's polymorphism lament — "the more we specify, the more we narrow the type" — lands as a design confirmation: **Canon IR is monomorphic by design**, so the tension never arises where verification actually happens.

---

## 6. Fight or flight — governed, not forbidden

LH's escape hatches (`ignore`, `assume`, `lazy`) are pragmatic triage. CFF splits the policy by trust zone:

**Generated rules: no escape hatches. None.** `#nowarn` is already sandbox-rejected; CF-* suppressions are not grammar. A rule that can't pass without an exemption gets rewritten or escalated — the restricted lane exists so that "fight" is always affordable.

**Harness and CanonFlow library code: hatches allowed, but ledgered.** Every `assume`-equivalent (a suppressed analyzer rule, an unproven invariant, a trusted lemma) becomes an entry in an **assumption ledger** shipped inside the evidence bundle:

```fsharp
type Assumption =
    { Id         : AssumptionId
      Claim      : string              // "reverse preserves noStuttering"
      Rationale  : string              // "validity obvious; proof not worth the medicine"
      StandIn    : PropertyRef option  // the FsCheck property holding the fort
      Owner      : Custodian
      ReviewBy   : LocalDate }
```

This encodes the case study's honest move — assuming `lemmaReverseNoStuttering` because "the disease isn't worth the medicine" — as auditable debt with an expiry, not silent trust. **Lemma→property demotion rule:** any lemma too expensive to prove must be demoted to a high-iteration FsCheck property referenced by its ledger entry; a `StandIn = None` assumption fails the evidence-bundle lint.

---

## 7. Verification pressure as design feedback

The case study's most quietly important observation: *"changes that simplified the verification of an invariant tended to benefit the code quality independently of it."* CFF operationalizes this as a metric rather than a vibe — track, per rule candidate, the **verification friction**: repair turns + unextractable-rewrite requests + assumption-ledger growth. Rising friction across candidates for the same statutory area is a signal that the *primitives* need a new combinator, not that the model needs a bigger rank. Friction reports feed CanonFlow API evolution — the same feedback loop the author describes, made measurable.

---

## 8. Deltas to the doc set

1. **Seal §2 (lanes)** — Lane 1 justification extended: IR extractability + SMT decidability + termination-by-construction.
2. **Crucible gates** — add CF-VERIFY family (Z3 over IR): TOTAL, DET, BOUND, INTERVAL, META; runs at promotion time on survivors, after the round-trip gate.
3. **Harness §7** — round-trip property explicitly named as the bridge premise for all Z3 claims; promotion evidence records both the FsCheck agreement seeds and the Z3 proof artifacts (unsat cores on failure — a CF-VERIFY-TOTAL counterexample *is* the uncovered band, feed it back to the model verbatim).
4. **CanonFlow primitives** — UoM adoption (`inr`, `fraction`, `percent`); CF-NUM-001 extended to bare-decimal monetary arithmetic.
5. **New policy rules** — CF-DOC-001 (undischarged documented invariant).
6. **Type A corpus** — becomes spec-first triples (doc invariant + code + properties); correspondence checked at admission; LoRA faculty 2.1 extended with spec-comment discipline.
7. **Evidence bundle** — gains the assumption ledger; lint fails on `StandIn = None`.
8. **Metrics** — add verification-friction per statutory area; report feeds primitive/combinator evolution.
9. **Toolchain digest** — Z3 version + IR-to-SMT encoder version join the pinned set (the cache key already absorbs them via Toolchain ⊕ Policy).
10. **Reward ladder (Seal §8)** — CF-VERIFY sits at tier 6+ (promotion-time only); never in the GRPO hot path — solver time is for survivors, not rollouts.
