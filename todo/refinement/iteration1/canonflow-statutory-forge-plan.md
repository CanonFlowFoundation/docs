# The Forge & The Crucible

**Unified plan: GST-compliant F# rule generation on DGX Spark (GB10)**
*CanonFlow statutory arm — merged from the Purist Blueprint and the Promotion-Boundary critique, corrected against verified facts.*

> எப்பொருள் யார்யார்வாய்க் கேட்பினும் அப்பொருள்
> மெய்ப்பொருள் காண்பது அறிவு — Kural 423
> Whichever mouth speaks the code — frontier, 9B, or human — the Crucible discerns மெய்ப்பொருள்.

---

## 0. Constitutional position

**The compiler is ruthless about types, but blind to law.** `dotnet build = 0` proves well-typedness, not statutory correctness, determinism, purity, or correct effective-date handling.

Therefore two boundaries, not one:

1. **Authoring surface** — AI writes native F#. Full DUs, recursion, refinement types. No JSON middleman in authoring. The Purist position stands.
2. **Promotion boundary** — the signed production artifact is never raw AI F#. It is the restricted **Canon IR projection** extracted from *verified* F#, round-tripped under the constitutional invariant:

```
introspect (emit rule) ≅ rule
```

JSON/CBOR is a serialization detail of the IR, not a language anyone authors in. The circular is the axiom set; the property suite is the theorem statements; only F# passing both is a proof; the IR pack is the derived theorem that ships.

**The unifying move:** the promotion predicate, the RL reward, and the eval metric are **the same object** — one F# function, three folds. It cannot drift between training and production because it is the same code.

---

## 1. Pipeline

```
GST source + provenance
        │
        ▼
Frontier teacher ──► Candidate F# (isolated worktree, GeneratedRule.fs only)
        │
        ▼
Inner loop: FCS typecheck + Fantomas + span-scoped repair   (ms-scale, warm)
        │
        ▼
Outer gate: FixedHarness.fsproj  --no-restore -warnaserror  (hardened, sandboxed)
        │
        ▼
Golden vectors ─► FsCheck properties ─► Mutation score ≥ τ
        │
        ▼
Canon IR extraction ─► round-trip law check
        │
        ▼
Frontier diff-review (law fidelity) ─► CA / custodian approval
        │
        ▼
Signed data-only rule pack (provenance trail attached)
```

---

## 2. The `Promotable` predicate — one spec, three folds

```fsharp
type GateResult =
    | Passed
    | Failed of gate: string * evidence: string list

type PromotionEvidence =
    { Compiles        : GateResult   // FCS + hardened build, zero warnings
      AllowedEffects  : GateResult   // no IO, clock, RNG, reflection, net
      TotalPatterns   : GateResult   // FS0025 as error; exhaustive DUs
      ExampleTests    : GateResult   // exact statutory examples
      PropertyTests   : GateResult   // FsCheck suite (see §7)
      Falsifiers      : GateResult   // known-wrong inputs rejected
      MutationScore   : GateResult   // ≥ τ against labelled mutants
      Canonicalizes   : GateResult   // Fantomas idempotent
      HostAgreement   : GateResult   // Native ≅ Fable where cross-host
      SourceBound     : GateResult   // RuleId ↔ SourceRef ↔ EffectiveOn
      HumanApproved   : GateResult } // custodian / CA sign-off

// Fold 1 — CI gate:        all gates Passed  → promote IR pack
// Fold 2 — GRPO reward:    weighted sum, HumanApproved excluded
// Fold 3 — Eval metric:    per-gate pass rates on held-out circulars
```

Reward shaping (Fold 2):

| Term | Role | Weight |
|---|---|---|
| Typecheck (FCS) | **hard gate** — reward 0 if failed | gate |
| Golden vectors + properties | dominant term | high |
| Mutation score | correctness depth | medium |
| Fantomas idempotence | style | small |
| Verdict-collapse penalty | punishes `let evaluate _ = Unknown "?"` | negative |

⚠️ Fail-closed `Unknown` is **correct for production** and **penalized in training** when used as a blanket escape. These are not in tension: the reward punishes hiding behind `Unknown`; the runtime honors it when genuinely reached.

---

## 3. Result shape (mandatory)

The original blueprint's `| _ -> Allowed` was the single most dangerous line in either document. Unknown classifications fail closed, and every verdict carries evidence.

```fsharp
type ITCVerdict =
    | Allowed
    | Blocked of reason: string
    | Unknown of reason: string          // fail-closed default

type Evaluation =
    { Verdict     : ITCVerdict
      RuleId      : RuleId
      SourceRef   : SourceRef            // circular / section / notification
      EffectiveOn : LocalDate            // NodaTime; interval-aware
      Evidence    : Evidence list }      // explanation trail
```

Discipline (standing law):

- `decimal` / `Money` — never `float`
- Clock, jurisdiction, effective date, reference data **injected explicitly** — no `DateTime.Now`, `Guid.NewGuid`, `Random`
- DUs over strings — HSN categories, usage classes are types, not `string`
- `-warnaserror`, FS0025 fatal
- Amendments modelled as effective intervals, not overwrites

---

## 4. Phase 0 — Crucible before Forge (the anti-Pugazh gate)

**Build the judge before the student.** Nothing trains until this exists, and none of it is wasted — GRPO consumes it as the reward function.

1. Eval set: held-out real circulars + golden decision-table vectors (custodian-reviewed).
2. FCS repair loop (§6.1) + hardened harness (§6.2).
3. Measure **untuned** `Qwen3.5-9B` and `Qwen3.5-27B` (128GB runs the 27B trivially), few-shot with retrieval over the verified corpus.
4. Decision gate: if pass@3-with-loop ≥ ~90% on held-out circulars, **stop — training is a hobby.** Ship the loop.

Model facts (verified July 2026, pin the revision hash):

- `Qwen/Qwen3.5-9B` — real. Released Feb/Mar 2026. Dense 9.65B, hybrid Gated DeltaNet + Gated Attention, 262K native context, Apache 2.0. Day-zero Unsloth support incl. RL.
- Family: 0.8B / 2B / 4B / 9B / 27B / 35B-A3B / 112B-A10B, all Unsloth-supported.
- GB10 reality: 128 GB coherent unified memory, **273 GB/s bandwidth** — capacity is free, throughput is not. This drives every training decision below.

---

## 5. Phase 1 — The Data Forge

**Iron law: nothing enters the corpus unverified.** Every sample — including "perfect" teacher output — must pass `fsc` *and* its own golden vectors before admission. Frontier models write plausible-but-broken F# often enough to poison an SFT set. Rejection sampling is not optional.

### 5.1 Type A — Verified translations (Law → F#)

Teacher reads the circular, emits F# against the CanonFlow scaffold (module header, opens, domain types **pre-provided in the prompt** — the model fills only the DU + `evaluate`). Sample admitted only if it compiles under the hardened harness and passes teacher-drafted, custodian-reviewed golden vectors. Fantomas-canonicalize on admission — one style across the corpus kills most offside-rule failures before they are learned.

### 5.2 Type B — Mock laws (quarantined)

Synthetic statutes with nested conditions, temporal deadlines, monetary thresholds — structure diversity against overfit. **Quarantine under `FICTIONAL_TRAINING_ONLY`.** Never enters GST retrieval, the Gold corpus, or production knowledge packs.

### 5.3 Type C — Bug & Fix (mechanical, never simulated)

Do **not** ask the teacher to invent compiler errors — it fabricates diagnostic wording, spans, and codes, training the model off-distribution. Build mechanically:

1. Start from a known-good, test-passing program.
2. Apply **one labelled mutation**: `0.18m → 0.18` · delete a DU match case · option/non-option mismatch · `Money → decimal` · break offside indentation · `list`/`seq`/`array` confusion · remove a required date case · flip `|>` direction.
3. Run the **pinned real compiler**. Capture actual code, span, stdout+stderr (diagnostics often land in stdout).
4. Generate/solicit the repair. Recompile, rerun tests.
5. Retain **only if** the repair succeeds without weakening validation.

Record schema:

```json
{
  "compiler_version": "pinned SDK",
  "project_hash": "…",
  "mutation": "decimal_to_float",
  "buggy_source": "…",
  "diagnostics": [{ "code": "FS0001", "line": 18, "column": 29, "message": "…" }],
  "corrected_source": "…",
  "compile_passed": true,
  "tests_passed": true,
  "source_hash_before": "…",
  "source_hash_after": "…"
}
```

After seeding, the mutation harness scales to ~50k authentic triplets with **zero frontier tokens** — the compiler generates the labels.

### 5.4 Corpus hygiene

- Loss **masked to completion tokens only** — training on the full text (law included) wastes capacity and teaches copying.
- Mix datasets by **explicit interleave probabilities** (~70% perfect / 30% bug-fix). `concatenate_datasets` merely appends. ⚠️
- Normalize to TRL conversational `messages` format; `dataset_text_field="text"` against a messages schema silently mangles. ⚠️
- Ground style on your own verified corpora (SqlHydra fork, Helios, CanonFlow) — Fantomas-formatted.

---

## 6. Phase 2 — Training (staged, ablated, throughput-aware)

### 6.0 Smoke test first

Unsloth on ARM64/GB10 has documented model-specific workarounds. Run a **20-sample smoke test** before committing any full run. GRPO on Qwen3.5 requires disabling fast vLLM inference in favor of Unsloth inference.

### 6.1 Stage 1 — SFT ablation (only if Phase 0 gate failed)

| Candidate | Purpose |
|---|---|
| Untuned 9B + compiler loop | Confirms training is needed at all |
| LoRA r=16 | Low-cost baseline |
| LoRA r=32 | Likely practical middle |
| LoRA r=64 | Only if held-out results justify it |

Config: bf16 LoRA (9B ≈ 22 GB — trivial on 128 GB; the constraint is **273 GB/s bandwidth**, so smaller ranks earn their keep), 16k seq, completion-only loss, adamw_8bit (call it what it is: BF16 compute with an 8-bit optimizer — "pure BF16" it is not ⚠️), held-out eval split from day one.

### 6.2 Stage 2 — GRPO with `Promotable` as reward

The 2026 method. Statutory F# is the ideal RLVR domain: the reward function already exists and cannot be gamed by a judge model.

- Reward = Fold 2 of §2. Typecheck gates; golden + property pass dominates; verdict-collapse penalized.
- **Throughput engineering** (this is where GB10 runs live or die): FCS as a warm service; parallel reward workers; verdicts **cached by code hash**; per-rollout compile must be milliseconds, not MSBuild seconds.

### 6.3 Metrics (track all, every run)

first-attempt compile rate · compile@3-repairs · tests-passed rate · **semantic regression rate** · fabricated-API rate · forbidden-effect rate · FS0025/incomplete-pattern rate · mutation score · base-model F# retention · statutory abstention quality.

**A candidate that compiles but fails a statutory test is a semantic failure, not a partial success.**

---

## 7. Phase 3 — Runtime (the two loops)

### 7.1 Inner loop — FCS, warm, span-scoped

Not `dotnet build` per attempt. Host **FSharp.Compiler.Service** in a warm process: millisecond typecheck, structured diagnostics with ranges. Per attempt:

```
Fantomas format → FCS typecheck → feed back ONLY diagnostic span + surrounding lines
```

Never dump raw stderr into the prompt. Stop when the **source hash or diagnostic fingerprint repeats** — the model is looping.

**The harness is written in F#** (provctl / Harness lineage), not Python. A Python wrapper around a type-derivation framework is a strange confession. ⚠️

### 7.2 Outer gate — hardened build

The model writes **only** `GeneratedRule.fs`. It never touches: `.fsproj` / `.props` / `.targets` · `global.json` · package sources / NuGet · compiler flags · test expectations · build scripts.

```
dotnet build FixedHarness.fsproj --no-restore --disable-build-servers -warnaserror
```

Sandbox per attempt: fresh temp dir · CPU/mem/output/wall-clock limits · **no network, no secrets** · SDK + deps mounted read-only. Reject at the gate: `#r` / `#load` / `#nowarn` · type providers · P/Invoke · custom MSBuild tasks · file/network/process/reflection-emit/mutable-global/environment APIs · `DateTime.Now` / `Guid.NewGuid` / `Random` / implicit time.

**Compilation success advances the candidate to testing. It never returns as completed code.**

### 7.3 Test matrix (minimum)

- Exact statutory examples
- Monetary boundaries: just below / at / just above
- Deadlines: day before / on / day after
- Missing and contradictory evidence
- Leap years, year-end, timezone boundaries
- Amendments and **overlapping effective dates**
- Unknown HSN / jurisdiction → fail-closed `Unknown`
- Metamorphic: irrelevant evidence does not change the verdict
- Diff against existing reference implementation
- Native ≅ Fable agreement where cross-host

### 7.4 Escalation ladder

```
9B (local, unlimited)
  → fingerprint repeat / attempt cap →
Sonnet (tiered)
  → still failing →
Opus (critical)
  → still failing →
Human custodian
```

Custodian is the ceiling, not rung three. Every promoted rule ships as a **signed IR pack** with the full provenance trail: RuleId, SourceRef, effective interval, evidence chain, gate results, model + revision that authored it.

---

## 8. Discard list

**From the Purist blueprint:**
- Simulated compiler errors — off-distribution poison
- Trusted ("perfect") teacher output — verify or reject
- `dotnet build` in the inner loop — MSBuild seconds vs FCS milliseconds
- Python harness — F# with FCS
- `| _ -> Allowed` — fail-open on unknown law
- "Air-gapped frontier session" — hosted teachers cannot be air-gapped; use hosted + public/redacted material, or a local open-weight teacher
- Hard-coded teacher brands ("Gemini 2.5 Pro / Claude 3.5 Sonnet") — retired/stale; select the teacher by current benchmark at run time

**From the critique doc:**
- The Qwen3.5-9B "unverifiable" correction — stale; the model is real, pin it
- Any drift toward hand-authoring Canon IR — IR is derived, never written

---

## 9. Execution order (this weekend → Monday)

| Slot | Work |
|---|---|
| Sat AM | FCS inner loop + hardened `FixedHarness.fsproj` sandbox (F#) |
| Sat PM | Eval set: 20–30 held-out circulars + golden vectors; custodian review pass |
| Sun AM | Baseline: untuned 9B + 27B few-shot w/ retrieval → **Phase 0 gate decision** |
| Sun PM | If gate failed: mutation harness + first 1k verified Type A samples; 20-sample Unsloth ARM64 smoke test |
| Mon | If proceeding: r=16 ablation run starts; mutation harness scaling to 50k in background |

The weekend goes to the Crucible, not the Forge. Build the judge before the student.
