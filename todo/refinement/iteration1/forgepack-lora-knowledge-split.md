# ForgePack: Skill in the Weights, Law in the Pack

**Companion to `canonflow-statutory-forge-plan.md` (§0–9) and the harness design.**
*Cherry-picked from the LoRA responsibility-split critique; corrected, integrated, contradictions resolved.*

---

## 0. The decomposition

```
ForgePack_t = Harness ⊕ (BaseModel + LoRA) ⊕ Knowledge_t
```

| Component | Owns | Change cadence |
|---|---|---|
| Foundation model (`Qwen/Qwen3.5-9B`, pinned revision) | General reasoning, language, coding | Rare (model bump) |
| LoRA adapter | F# + CanonFlow **working habits** | Slow (retrain on behavior drift) |
| Knowledge pack (`Knowledge_t`) | Current GST law, rates, circulars, effective dates | Fast (every notification) |
| Harness | Tool control, validation, security, evidence | Versioned with `Promotable` |
| Crucible | Independent semantic testing | Versioned with eval set |
| Human custodian | Statutory approval | Constant |

The one-line law:

> **LoRA teaches *how* to engineer a GST rule. The knowledge pack states *what* the law currently says. The harness decides whether the result is acceptable. None of the three trusts the others.**

The critique hedged on "8B/9B" — resolved: `Qwen3.5-9B` is verified real (Feb/Mar 2026, dense 9.65B, hybrid Gated DeltaNet, 262K ctx, Apache 2.0). Pin the revision hash; no fallback needed.

---

## 1. Why law stays out of the weights

Weights are the wrong substrate for anything that changes by notification:

**Never in LoRA:** current rates · notification numbers · turnover thresholds · filing deadlines · HSN mappings · state amendments · circular interpretations · effective dates · judicial decisions.

Reasons, in order of severity:

1. **Audit** — a rate remembered by a weight matrix has no `SourceRef`. A verdict that cannot cite is not evidence; it is testimony.
2. **Latency** — law changes in days; retraining cycles in weeks. The pack updates by re-signing data.
3. **Rollback** — a wrong fact in `Knowledge_t` is a one-line revert. A wrong fact in an adapter is a retrain.

The model must never "know" 18%. It must be *handed*:

```fsharp
{ Rate           = 0.18m
  EffectiveFrom  = LocalDate(2025, 4, 1)
  EffectiveUntil = None
  Source         = NotificationRef …
  PackDigest     = Sha256 … }
```

…and convert grounded authority into a proposed rule. **The knowledge pack is itself a signed artifact** — same canonical-CBOR + content-address + detached-signature machinery as the rule packs (harness design §8). `Knowledge_t` is versioned, retrieval-injected into the scaffold prompt, and its `PackDigest` lands in every candidate's provenance trail. This is the CanonFlow market thesis applied to statute: implicit constraints made machine-readable, provenance-carrying, law-verified.

**Corollary — a new eval gate:** an *ungrounded constant* in generated code (any monetary rate, threshold, or date literal not traceable to a supplied pack record) is a `Rejection` case:

```fsharp
| UngroundedFact of literal: string * Range
```

The effect firewall walks the typed AST anyway (harness §4); constant-provenance checking is one more fold over the same tree.

---

## 2. What the LoRA teaches — five faculties

### 2.1 Strict F# generation
Lightweight syntax and offside discipline · exhaustive matches · DUs · `Option`/`Result` · immutable records · `decimal` never `float` · `LocalDate` + effective intervals · **typed** `Unknown` over unsafe defaults · bounded recursion · CanonFlow naming and module conventions.

Target behavior:

```fsharp
match invoice.Category with
| FoodAndBeverage      -> …
| CapitalGoods         -> …
| UnknownCategory v    -> Unknown (UnsupportedCategory v)
```

Trained against, hard: `| _ -> Allowed`. ⚠️ (Fail-open on unknown law — the most dangerous line in the original blueprint; now also a falsifier in the Crucible.)

### 2.2 CanonFlow API fluency
The permitted vocabulary, drilled until invention stops: `Money` · `TaxPeriod` · `StateCode` · `EffectiveInterval` · `Evidence` · `SourceRef` · `Verdict` · `RuleResult`. Untuned models hallucinate `GSTCalculator.CalculateTax(…)`; the harness firewall rejects it, but every rejection is a wasted loop iteration. LoRA's job is to make the firewall bored. **Metric: fabricated-API rate** (plan §6.3) — this faculty is measured directly.

### 2.3 Compiler-error repair — the highest-value faculty
The learned transformation:

```
Code_failed ⊕ Diagnostics → Code_corrected
```

Coverage: FS0001 type mismatches · FS0025 incomplete matches · wrong DU cases · missing modules · indentation/offside · float↔decimal · DateTime↔LocalDate · option misuse · record-field ambiguity · misapplied functions.

**Only real pinned-compiler diagnostics** — the mechanical mutation harness (plan §5.3) is the sole source. Teacher-invented error text remains banned. Note the closed loop: surviving mutants from Crucible mutation-scoring flow back here as fresh repair triplets. The crucible feeds the forge.

### 2.4 Harness obedience
Behavioral, not legal: emit only the requested module · never touch `.fsproj`/packages · never request network/filesystem · allowlisted APIs only · repair **only the diagnostics supplied** (no drive-by rewrites of working code) · stop when information is insufficient · preserve source references · **never weaken a test to make code pass**. This faculty is what makes span-scoped feedback (harness §3) cheap — an obedient model doesn't need the whole file.

### 2.5 Statutory abstention
A high-quality domain model is not one that always answers; it is one that knows when the evidence does not support a deterministic verdict.

```fsharp
Unknown
    { Reason         = ConflictingAuthorities
      RequiredReview = StatutoryCustodian
      Sources        = conflictingSources }
```

**Resolved tension with the GRPO reward** (plan §2 penalized verdict collapse; this doc rewards abstention — both are right, conditionally):

> The reward is **label-conditional**. When the golden decision table shows a determinate verdict, `Unknown` scores as failure (anti-collapse penalty stands). When the eval label is itself `Unknown`/conflict — contradictory sources, missing classification, overlapping authorities deliberately planted in the eval set — a *typed, sourced* `Unknown` scores as **success** and a confident guess scores as the worst failure in the battery.

The eval set therefore needs a dedicated abstention slice with planted conflicts. Metric: **appropriate-Unknown rate**, measured in both directions (false confidence and false abstention).

---

## 3. Adapter structure — decision

The critique proposes two adapters:

- **Adapter A — F# Forger** (stable): syntax, compiler repair, FP discipline, CanonFlow primitives, security restrictions, output discipline.
- **Adapter B — GST Engineering** (versioned): GST terminology, statutory structure patterns, temporal modelling, evidence requirements, law→type translation, Allowed/Blocked/Unknown reasoning. Teaches GST *structures*, never memorizes current law.

**Call: start with one combined adapter, tag-separated datasets.** Rationale: stacked-adapter serving on the GB10 against Qwen3.5's hybrid DeltaNet architecture is unproven (Unsloth ARM64 already needs a smoke test; don't compound unknowns), and the critique itself concedes this fallback. The logical split lives in the **data tags**, so a future A/B split is a re-slice of the corpus, not a re-collection. Revisit only if eval shows the GST slice degrading F# faculties or vice versa.

---

## 4. Training mixture (initial, tagged)

| Dataset slice | Share | Primary faculty |
|---|---|---|
| Valid CanonFlow F# generation | 35% | 2.1 + 2.2 |
| Real compiler bug & repair | 30% | 2.3 |
| Law-to-typed-rule structure | 15% | 2.2 + law→type |
| Boundary & temporal modelling | 10% | intervals, deadlines |
| Abstention & conflict handling | 5% | 2.5 |
| Security-policy refusal | 5% | 2.4 |

Starting values, not laws — mixture is an ablation axis. **Every example carries a faculty tag** (`Syntax` · `TypeModelling` · `CompilerRepair` · `ApiUse` · `TemporalReasoning` · `EvidenceBinding` · `Abstention` · `SecurityCompliance`) so per-faculty regressions are visible in eval, and mixture reweighting is a query, not a rebuild. Interleave by explicit probability (never `concatenate_datasets` ⚠️); loss masked to completion tokens; all admission rules from plan §5 stand — verified samples only, mock laws quarantined under `FICTIONAL_TRAINING_ONLY`.

---

## 5. LoRA mechanics — stated precisely

`get_peft_model(model, r=…)` freezes the 9B base and trains low-rank matrices on selected layers. Even with `load_in_4bit=False, dtype=bfloat16` this is **LoRA, not full-parameter BF16 fine-tuning** (and adamw_8bit means an 8-bit optimizer besides ⚠️). What that buys: small trainable state · fast experiments · **removable adapters** · independent rollback · separate adapter signatures · low risk of damaging base F# ability.

Rank discipline (extends plan §6.1): **choose the smallest rank that reaches the acceptance target.** r=64 may lift compile rate while degrading abstention quality or general reasoning — evaluate the whole pipeline, never compile success alone.

| Experiment | Purpose |
|---|---|
| No LoRA (base + harness) | Tool-grounded baseline — the bar to beat |
| r=16 | Efficient specialization |
| r=32 | Likely middle |
| r=64 | Only if held-out results demand the capacity |

---

## 6. The earn-its-place criterion

```
LoRAValue = Quality(LoRA ⊕ Harness) − Quality(Base ⊕ Harness)
```

If the untuned base with compiler tools reaches nearly the same result, the adapter is **unnecessary operational complexity** — this is Phase 0's gate (plan §4) restated as a continuous metric, now measured **per faculty**:

first-attempt compile · compile-after-repair · CanonFlow API invention rate · forbidden-API rate · exhaustive-match rate · test-pass rate · hidden-Gold pass rate · source-binding completeness · appropriate-Unknown rate (both directions) · repair-loop token & wall cost · **base-model F# retention**.

Served by `harnessd /score` (harness §9) — the same Fold 2/3 machinery; LoRAValue is a diff of two eval sweeps, cached by (adapter digest × eval-set digest).

---

## 7. Packaging & provenance

Adapters are signed, first-class ForgePack citizens:

```fsharp
type LoRAIdentity =
    { AdapterDigest          : Sha256
      BaseModelDigest        : Sha256      // pinned Qwen3.5-9B revision
      TrainingCorpusDigest   : Sha256      // tagged, verified corpus
      TrainingRecipeDigest   : Sha256      // Unsloth config, mixture, seeds
      EvaluationReportDigest : Sha256
      CompatibleHarness      : VersionRange
      CanonFlowApiVersion    : ApiVersion }
```

**Do not merge the adapter into the base** (initially): keep it detachable to disable a defective adapter, A/B versions, roll back independently, and *prove which adapter authored a candidate* — `AdapterDigest` joins `PackDigest` and compiler version in every rule's provenance manifest (harness §8). A promoted rule's lineage is fully reconstructible: which law pack, which adapter, which harness, which seeds.

---

## 8. The boxed law

```
LoRA  =  FSharpSkill ⊕ CanonFlowDiscipline ⊕ CompilerRepair ⊕ AbstentionBehaviour
LoRA  ≠  GSTAuthority
```

The model becomes excellent at turning **supplied, cited** law into safe candidate F#. Current law lives outside the weights, versioned in `Knowledge_t`; the harness trusts neither component without independent validation; and only the Crucible + custodian make anything true.

---

## 9. Deltas to the master plan

1. Plan §2 reward table — add label-conditional abstention term (§2.5 here).
2. Plan §5.4 — add faculty tags to corpus schema; mixture table (§4 here) supersedes the 70/30 note.
3. Plan §6.3 metrics — add API-invention rate, source-binding completeness, bidirectional Unknown rate, per-faculty breakdown.
4. Harness §4 firewall — add `UngroundedFact` constant-provenance fold (§1 here).
5. Harness §8 manifest — add `LoRAIdentity` + `PackDigest` fields.
6. Eval set — add planted-conflict abstention slice.
7. Phase 0 baseline — reframed as the standing `LoRAValue` denominator, re-run per adapter candidate.
