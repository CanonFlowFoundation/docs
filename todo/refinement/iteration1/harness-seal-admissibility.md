# The Seal: Admissibility, Not Truth

**Third companion to `canonflow-statutory-forge-plan.md` and `forgepack-lora-knowledge-split.md`.**
*Cherry-picked from the authority critique. Each item marked: **[SEALS]** = confirms a call already in our design, now made explicit law · **[DELTA]** = genuine change.*

---

## 0. The corrected trinity **[DELTA — governing frame]**

The harness was drifting toward oracle status. Corrected:

```
System_t = LoRA_capability ⊕ Knowledge_t ⊕ Harness_verification ⊕ HumanApproval
```

| Pillar | Exact responsibility |
|---|---|
| LoRA | First-pass yield, F# fluency, repair behaviour |
| Knowledge pack | Time-indexed GST authority + provenance |
| Harness | Enforces **mechanically testable** properties |
| Custodian | Approves statutory interpretation and release |

The sealed slogan:

> **LoRA improves yield. Knowledge supplies authority. The Harness enforces admissibility. The Crucible supplies falsification. The custodian authorizes release.**

And the governing equation:

```
Ship(c) = HarnessPass(c) ∧ CruciblePass(c) ∧ SourceBound(c) ∧ HumanApproved(c)
```

---

## 1. What the harness can never prove **[SEALS §0 of the plan, strengthened]**

"The compiler is ruthless about types, but blind to law" now extends to the whole harness:

```
HarnessPass(c) ⇏ LegalTruth(c)
```

The harness guarantees that every accepted output satisfies the **encoded** compiler, security, determinism, evidence, and test obligations. It cannot prove: the correct provision was selected · an ambiguous circular was read correctly · the source set was complete · **the Gold vector's expected result is itself legally correct** · a future court will agree.

That fourth item is the sharp one: golden vectors are custodian *interpretations*, not ground truth. A `GoldenFailure` means "disagrees with the custodian's reading," never "illegal." Wording discipline everywhere: the harness makes output **admissible**; only sources + review make it **releasable**.

---

## 2. The three lanes — IR extraction sealed **[SEALS harness §7, now explicit]**

Arbitrary F# cannot generally be reflected into Canon IR. Reflection recovers types, signatures, attributes — never the *meaning* of an arbitrary recursive function. Our `IRExtraction = Extractable | Unextractable` was the Restricted lane implicitly; now the lanes are law:

| Lane | F# freedom | Promotion path |
|---|---|---|
| **Restricted subset** ← chosen | Bounded DUs, matches, comparisons, approved combinators | AST → Canon IR translation |
| Arbitrary F# | Recursion, HOFs, custom algorithms | Compiled plugin only; **no IR claim** |
| Experimental F# | Full model freedom | Human research lane; never auto-promoted |

`Unextractable` remains a rewrite request, never a grammar extension. This is **AST translation via FCS parse trees + symbols — not reflection** — and the FCS API surface is itself version-pinned in the toolchain digest, since it changes between releases.

---

## 3. Forge time ≠ evaluation time **[SEALS, worth a diagram]**

Rules are forged occasionally — legislation changes, a notification lands, an interpretation is proposed, a pack version is cut. Invoices are evaluated later, millions of times, by the deterministic engine executing signed packs.

```
Occasional forging ──► compile/test/approve ──► signed deterministic rule
                                                        │
                                                        ▼
                                     millions of fast invoice evaluations
```

**No FCS compilation, no LoRA generation, no agentic repair on the invoice path. Ever.** Any "repair cycles per invoice" framing is a category error — repair cycles are per *rule candidate*, amortized across every invoice the rule will ever judge.

---

## 4. Signing separation **[SEALS harness §8, hardened]**

The harness produces an **unsigned canonical artifact + evidence bundle**. An external authorized custodian signs after review.

```
Harness            → Candidate + Evidence
CustodianApproval  → SignedDecisionPack
```

Key hygiene, non-negotiable — the statutory signing key is never accessible to: the 9B model · the compiler sandbox · CI workers · the training environment · **any harness process that executes generated code**.

**[DELTA]** Two signatures, two meanings, never conflated:
- **Build attestation** (harness-side, machine key): "this artifact is byte-identical to what passed these gates" — attests *identity*.
- **Statutory approval** (custodian, offline key): "this rule is authorized for release" — attests *promotion*.

The manifest carries both, labelled.

---

## 5. Bubblewrap is a mechanism, not a policy **[DELTA — full policy spec]**

Bubblewrap constructs namespaces; the *caller* defines security. And the compiler genuinely needs writable scratch and some environment — "no writes, no env" is unimplementable. The realistic sealed policy:

- SDK, packages, harness mounted **read-only**
- One ephemeral writable `/work`, destroyed after evidence extraction
- Empty or controlled `$HOME`; explicit minimal allowlisted environment
- Private PID, IPC, UTS, and network namespaces; no external network
- seccomp filter; `no-new-privileges`
- CPU, memory, process-count, and wall-clock limits

Corrected phrasing: *no persistent or uncontrolled filesystem writes; only an explicit allowlisted environment.*

---

## 6. Policy analyzer ≠ compiler **[DELTA — new gate family]**

`let rate = 0.18` is **valid F#**. It only becomes FS0001 when it meets a `decimal` constraint. The F# compiler accepts many things CanonFlow must reject — so the firewall (harness §4) gains a named **CanonFlow policy analyzer** family, AST/symbol-level, reported with rule IDs like diagnostics:

| Rule | Violation |
|---|---|
| CF-NUM-001 | Floating-point literal in statutory monetary logic |
| CF-TIME-001 | `DateTime.Now` / implicit clock |
| CF-MUT-001 | Mutable state |
| CF-IO-001 | `System.IO` and friends |
| CF-RND-001 | `Random` / `Guid.NewGuid` |
| CF-EXN-001 | Exception-based control flow |
| CF-SRC-001 | Ungrounded constant (from ForgePack §1 — folds in here) |

These are **policy violations, not compiler errors** — separate rejection channel, separate metrics, and separate training data (the model must learn to read `CF-*` reports exactly as it reads `FS*` diagnostics — add a policy-repair slice to the Type C corpus).

---

## 7. RewardVector + non-compensable gates **[DELTA — replaces scalar shaping]**

Binary `Compiles ? 1 : 0` reward-hacks into empty implementations, constant `Unknown`, wildcards, deleted statutory branches. TRL GRPO supports multiple weighted reward functions — so `harnessd /score` returns a **vector**, not a Boolean:

```fsharp
type RewardVector =
    { Parsed               : bool
      TypeChecked          : bool
      PolicyClean          : bool       // NON-COMPENSABLE
      VisibleTestsPassed   : decimal
      SourceCoverage       : decimal
      Deterministic        : bool
      ComplexityWithinLimit: bool }
```

The security law:

```
¬PolicyClean(c) ⇒ Reward(c) = Rejected
```

**A candidate must never offset a forbidden API by passing more tests.** Security gates multiply; quality terms add. The label-conditional abstention term (ForgePack §2.5) slots into the additive region.

---

## 8. Tiered rewards — don't build millions of times **[SEALS harness §9 caching, adds the ladder]**

Full sandboxed build + FsCheck per GRPO rollout would dominate training on 273 GB/s hardware. The reward ladder, cheapest first, each tier gating the next:

```
1. Output-shape / token checks        (~free)
2. FCS parse                          (ms)
3. FCS typecheck                      (ms–tens of ms, warm)
4. AST + symbol policy (CF-*)         (ms)
5. Targeted deterministic examples    (fast subset)
6. Full sandboxed build + properties  (survivors only)
```

Cache key **[DELTA — supersedes "code hash"]**:

```
CacheKey = Hash(Source ⊕ Toolchain ⊕ Policy ⊕ Tests)
```

A cache keyed on source alone silently serves stale verdicts across SDK bumps, policy revisions, or eval-set updates. All four components are digests already in the manifest — the cache key is a fold over provenance.

---

## 9. Deployment representations must re-earn their eval **[DELTA]**

Do not auto-merge the LoRA or ship a quantized GGUF as if it were the evaluated artifact. A merged or quantized model **can behave differently** from the BF16 base+adapter configuration that passed eval. Law:

> Every deployment representation (BF16+adapter, merged, GGUF Q4/Q8, NVFP4 …) reruns the same held-out evaluation before it may author candidates. `AdapterDigest` in provenance is replaced by **`DeployedArtifactDigest`** — the thing that actually ran.

Retain separately: base-model digest · tokenizer digest · adapter · inference-runtime digest · quantization recipe · deployed-artifact digest.

Reproducibility corollary: destroying the training machine is fine; destroying the training **specification** is not. Preserve container digest, dependency lock, dataset manifest, training args, seeds, adapter, eval report. (Extends `LoRAIdentity`, ForgePack §7.)

---

## 10. Numbers are hypotheses until measured **[SEALS Phase 0 discipline]**

"Untuned fails 90%," "LoRA turns ten failures into one," "three minutes per rule" — benchmark *targets*, never architectural facts. The named metrics:

```
FirstPassYield  = #passing initial static gates / #generated
RepairYield_k   = #passing within k repair attempts / #generated
SemanticYield   = #passing hidden Gold / #compiling
```

The decisive one: **LoRA succeeds only if it raises SemanticYield, not merely FirstPassYield.** A model that compiles more but means less is a regression wearing a green checkmark. `LoRAValue` (ForgePack §6) is computed over all three, SemanticYield weighted heaviest.

---

## 11. First milestone — the harness as classifier **[DELTA — sharpens Phase 0]**

Before GRPO, before signing, before any IR extraction, the first buildable milestone is exactly:

fixed F# project template · FCS parse/typecheck service · AST + symbol allowlist (CF-* analyzer) · real diagnostic recorder · deterministic mutation engine · Bubblewrap policy (§5) · `dotnet build --no-restore` gate · five boundary-test families · append-only evidence bundle.

**Acceptance test: the harness classifies 500 known-good and known-bad samples with zero false acceptances.** (False rejections are tunable; false acceptances are disqualifying.) The mutation engine manufactures the known-bad set for free. Only a harness that can grade a fixed exam earns the right to grade a live model.

---

## 12. Deltas to the doc set

1. Plan §2 `Promotable` — restructure into `Ship(c) = HarnessPass ∧ CruciblePass ∧ SourceBound ∧ HumanApproved`; harness gates are the first conjunct only.
2. Plan §2 Fold 2 — reward becomes `RewardVector`; `PolicyClean` non-compensable; tiered ladder (§8) is the serving order.
3. Plan §9 weekend table — Sat AM scope replaced by milestone §11; 500-sample classification is the new Phase 0 gate alongside the baseline measurement.
4. Harness §4 — firewall renamed **CanonFlow Policy Analyzer**, rule-ID'd (CF-*), literal-level checks added beyond symbol allowlist.
5. Harness §5 — Bubblewrap section replaced by the §5 policy spec verbatim.
6. Harness §7 — three-lane table inserted; "Restricted subset" marked as the constitutional choice.
7. Harness §8 — dual-signature model; key-hygiene list; harness emits *unsigned* candidate + evidence.
8. Harness §9 — cache key upgraded to `Hash(Source ⊕ Toolchain ⊕ Policy ⊕ Tests)`.
9. ForgePack §7 — `DeployedArtifactDigest` + per-representation re-eval; reproducibility spec list.
10. All docs — wording sweep: "admissible" for harness verdicts, "releasable" only after custodian; no sentence may claim the harness proves law.
11. Type C corpus — add CF-* policy-repair slice.
12. Metrics — add FirstPassYield / RepairYield_k / SemanticYield as the canonical yield triple.
