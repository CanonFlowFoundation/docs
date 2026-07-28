# GSTFlow Iteration 2: Roadmap & Refinement

> **Status:** Iteration 2 Active Planning
> **Mission:** Establish the core GSTFlow engine adhering to the `CFF_PIPELINE` and `GSTFLOW_KNOWLEDGE_SDK` protocols.

## 🎯 The Core Philosophy: GSTFlow as an Observer
GSTFlow is **not** a large collection of tax `if` statements. It is a deterministic, replayable evidence chain.
* **GSTFlow observes and explains external state.**
* **It may create user-owned evidence about that state.**
* **It never silently changes the state it is observing.** (No filing, no mutating ledgers, no managed record custody).

## 🚀 What TO DO (The Action Plan)

### Phase 1: Classification & Core Map (Stages S0 & S1)
Before we calculate a single tax rate, we must classify our sources.
- **Implement the Authority Ladder (A0-D):** Build the types that categorize sources (Constitutional, Statutory, Interpretive, etc.).
- **Construct the Dependency Graph:** `Person → Supply → POS → Time → Value → Rate → Documents → ITC → Returns`. Every edge must be backed by a source family.
- **Implement the Conflict Protocol:** When rules collide, the system emits `Unknown` or `RequiresProfessionalReview` — it does not guess.

### Phase 2: One Narrow Vertical E2E (Stage S2)
- Select **one** hyper-narrow GST rule (e.g., a specific ITC eligibility or basic invoice validation).
- Push it through the full *Crucible* pipeline: Official Bytes → Interpretation → F# Rule → FsCheck Golden Vectors → Canon IR Extraction.
- Prove the rule passes the `Promotable` predicate (FCS check, 0 warnings, property tests, exhaustive DUs, human sign-off).

### Phase 3: Enforce the CFF Pipeline Discipline
- **One branch, one work item.**
- **No code without a witnessed failing check.**
- All rules must use explicit injected dependencies (Time, Jurisdiction). No `DateTime.Now` or `Random`.

---

## 🚫 What NOT TO DO (The Anti-Goals)

1. **Do not build a tax-filing engine:** GSTFlow is an observer. It never files, pays, disputes, or mutates a live ERP.
2. **Do not dump "Official PDFs" into a single bucket:** A GST Council Press Release (B1) is NOT operative law (A1/A2). The hierarchy must be mechanically strictly enforced.
3. **Do not use blind AI generation:** All AI-authored F# must pass through the *Crucible*. If it fails `fsc` or the golden vectors, it is rejected.
4. **Do not use primitive obsession:** Never use `float` for money (use `decimal`/`Money`). Never use `string` for HSN codes (use DUs).
5. **Do not swallow conflicts:** The fail-closed `Unknown` verdict is the structurally correct response to ambiguity. Do not write hacky fallbacks.
