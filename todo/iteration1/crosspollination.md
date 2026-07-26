# $\mathcal X^3$ · $\nu_3 \rightarrow \nu_{3.1}$ — Validation and Uplift Delta

**Scope:** internal validation of the $\nu_3$ specification, plus eight cherry-picked additions.
**Posture:** $\nu_3$ is the first document in this line whose numbers survive checking. This is a delta, not a rewrite.

---

## $\S 0$. Validation Boundary

Mirroring $\nu_3$'s own discipline, and with the same honesty:

$$
\operatorname{Validated}
=
\operatorname{InternalConsistency}
+
\operatorname{ArithmeticProof}
+
\operatorname{SemanticReview}
+
\operatorname{CriticalPathAnalysis}
$$

$$
\operatorname{Validated}
\not\supseteq
\operatorname{RepositoryObservation}
\cup
\operatorname{CIObservation}
$$

I did not read the three repositories or the six workflow runs. **Every census figure below is checked for coherence with the other census figures, not for correspondence with the pinned commits.** $\nu_3$'s §0 boundary applies unchanged and is not weakened by this document.

---

## $\S 1$. What Verifies

### 1.1 — Counts

$$|\mathcal X^3| = 4_{\mathcal X} + 7_{\mathcal C} + 8_{\mathcal F} + 5_{\mathcal P} + 3_{\mathcal I} = 27 \quad ✓$$

$$|\pi_0|=19,\; |\pi_1|=7,\; |\pi_2|=1 \;\Rightarrow\; \textstyle\sum = 27 \quad ✓$$

$$|\varepsilon_S|=2,\; |\varepsilon_M|=17,\; |\varepsilon_L|=8 \;\Rightarrow\; \textstyle\sum = 27 \quad ✓$$

$$\mathbb E_{\text{raw}} \in [2(1)+17(4)+8(8),\; 2(3)+17(7)+8(15)] = [134, 245] \quad ✓$$

Every figure recomputes from the requirement matrix. No requirement is orphaned, double-counted, or missing a band.

### 1.2 — Census coherence

$$40_{\text{occurrences}} - 1_{\text{name collision}} = 39_{\text{proof rows}} = 28_{\text{Proven}} + 11_{\text{Manual}} \quad ✓$$

$$12_{\text{aggregate occurrences}} - 1_{\text{collision}} = 11_{\text{stubs}} \quad ✓$$

$$52+46+20+7+7+6+2 = 140 = \operatorname{CommittedSarifResults} \quad ✓$$

The `chk_shipped_before_delivered` collision propagates correctly through all three derived figures. This is the arithmetic that failed in every prior draft.

### 1.3 — The judgment reducer

`deriveVerdict` is **total**, and its four branches reproduce all seven rows of the required matrix exactly. Branch order is load-bearing and correct: `Broken` absorbs first, `NonConformant` second, so $\langle\operatorname{Partial},\operatorname{NonConformant}\rangle \mapsto \operatorname{Fail}$ as specified. No unreachable branch, no fall-through gap.

### 1.4 — Law 12

$$\Delta_S \land \Delta_J \Rightarrow \operatorname{ReleaseBlocked}$$

This is the strongest idea in the document and it is new. The obvious split attack — weaken the judge in change $A$, alter the subject in change $B$ — is closed by requiring `HumanCodeOwnerApproved` on every $\Delta_J$. §2.5 identifies the one hole that remains.

---

## $\S 2$. Defects — Internal Inconsistencies

### 2.1 — X-01's acceptance makes the census a regression test against a hand count

$\nu_3$ requires the census be machine-generated from pinned inputs, then writes into its acceptance:

> *census reports 9 tables, 40 `CHECK` occurrences, 39 unique names, and 11 references*

If the generated census disagrees with those literals, which is authoritative? Pinning expected values into acceptance reinstates the hand count as the judge — the precise failure C-07 forbids when it says `PROOF.md` *"is generated from the ledger and is never an authority itself."*

**Correction.** The census emits; a **separately reviewed baseline digest** records what was accepted; drift from the digest fails.

$$
\operatorname{CensusAccepted}
\iff
H(\operatorname{Census}) = \operatorname{ApprovedCensusDigest}
$$

The literals move from acceptance criteria into the reviewed baseline artefact, where a human signed for them. `ApprovedCensusDigest` joins $\mathcal J$ (F-08).

### 2.2 — Law 8 contradicts F-06

$$
\text{Law 8}:\;
\operatorname{DelegatedFindingId} = \langle \operatorname{Engine}, \operatorname{NativeRuleId}, \operatorname{EngineVersion} \rangle
$$

$$
\text{F-06}:\;
\operatorname{DelegatedId} = \texttt{FSharpLint/}\langle\operatorname{NativeId}\rangle
$$

The target format omits the version the law requires. Worse, **Law 8 is the one that should yield**: version-in-identity means every FSharpLint bump invalidates every waiver, producing churn that pressures teams toward blanket suppressions.

**Correction.** Identity stable, version recorded, waiver optionally scoped:

$$
\operatorname{Id} = \langle\operatorname{Engine},\operatorname{NativeRuleId}\rangle
\qquad
\operatorname{EngineVersion} \in \operatorname{Attributes}(f)
$$

$$
\operatorname{Waiver} \ni \operatorname{VersionScope} \in \{\operatorname{Any}, \operatorname{Pinned}(v)\}
$$

F-06's acceptance ("no collision across engines or versions") is then satisfied by attribute comparison, not by identity inflation.

### 2.3 — $\mathbb F$ has no value for "correctly owned by PostgreSQL"

$$
\mathbb F = \{\operatorname{Exact}, \operatorname{Conditional}, \operatorname{Approximate}, \operatorname{Manual}, \operatorname{Unsupported}\}
$$

Law 2 counts **all** qualified occurrences, so `UNIQUE(sku)`, foreign keys and `EXCLUDE` constraints are in $\mathcal K$. None is translatable to a value-level enforcement. None is `Manual` — nobody hand-wrote it. So each lands in `Unsupported`, and:

$$
U > 0 \;\;\text{permanently} \;\Rightarrow\; \text{strict release never passes}
$$

The escape hatch is to quietly drop uniques from $\mathcal K$, which violates Law 2. **The ledger as specified is unsatisfiable on any real schema.**

**Correction.** Add a passing fidelity for intentional database ownership — §6.

### 2.4 — $\mathbb H \times \mathbb C$ is not fully inhabited

The product has nine points; the pipeline can construct fewer. $\langle\operatorname{Broken}, \operatorname{Conformant}\rangle$ requires a mandatory evaluator to have crashed while contributing a positive conformity verdict — unreachable by construction.

Totality of the reducer is correct and should stay. But F-07's *"property tests cover the complete judgment-product matrix"* then spends assurance on states the system cannot reach.

**Correction.** Two obligations, not one:

$$
\text{(a)}\quad \forall j \in \mathbb H\times\mathbb C:\; \operatorname{deriveVerdict}(j) \text{ terminates} \quad \text{(totality)}
$$

$$
\text{(b)}\quad \forall e \in \operatorname{EvaluatorStates}:\; \operatorname{Judgment}(e) \in \operatorname{Inhabited} \quad \text{(reachability)}
$$

Property (b) is the one that catches real bugs: it fails when an evaluator can report `Failed` while its compliance is folded to `Conformant`.

### 2.5 — Δ_J is admitted without measuring its effect

`HumanCodeOwnerApproved` proves a human clicked. It does not prove the human knew what the change admits. An agent can propose a policy-only diff that is innocuous in isolation and load-bearing for a subject change queued behind it.

**Correction.** Every $\Delta_J$ carries its verdict delta on a frozen corpus — §5.1.

### 2.6 — `Broken` masks a known blocking finding at the exit code

`Broken _, _ -> ToolFailure` is right for the verdict. But exit code $3$ tells an operator *"retry the tool,"* while a retained blocking finding means *"fix the code."* Both are true; the channel carries one.

**Correction.** Verdict unchanged. Reporting obligation added: when $\operatorname{Health}=\operatorname{Broken} \land \operatorname{Compliance}=\operatorname{NonConformant}$, the CI summary and MCP envelope must surface both, and the summary line must not read as a transient failure.

---

## $\S 3$. Law 3 Has No Teeth

Law 3 states PostgreSQL's three-valued acceptance correctly. Nothing in the 27 requirements implements it. There is no truth type, no Kleene algebra, no acceptance predicate, no rule preventing a generator from emitting `bool`.

**This is the highest-severity semantic risk in the programme**, because its failure mode is silent and inverted: a two-valued translation is typically *stricter* than PostgreSQL, so generated validators reject rows already resident in production. It surfaces on read, not on write.

Straight from the pinned schema's own shape:

```sql
CREATE TEMP TABLE t (
    col   text,
    other text,
    CONSTRAINT chk CHECK (col <> 'X' OR other IS NOT NULL)
);
INSERT INTO t VALUES (NULL, NULL);   -- ✅ ACCEPTED
-- col <> 'X'        → UNKNOWN
-- other IS NOT NULL → FALSE
-- UNKNOWN OR FALSE  → UNKNOWN       → admitted (not FALSE)
```

Naive translation `col <> "X" || other.IsSome` evaluates `false || false = false` and rejects. Divergence on the first nullable row.

### 3.1 — Kleene $K_3$, mandatory

Lattice $\operatorname{False} < \operatorname{Unknown} < \operatorname{True}$, with $\wedge = \min$, $\vee = \max$, $\neg$ order-reversing.

```fsharp
/// PostgreSQL truth. Never collapsed to bool before the acceptance test.
type SqlTruth =
    | True
    | Unknown
    | False

module SqlTruth =

    let negate = function
        | True    -> False
        | False   -> True
        | Unknown -> Unknown

    let conj a b =
        match a, b with
        | False, _   | _, False   -> False       // absorbing — matched first
        | Unknown, _ | _, Unknown -> Unknown
        | True, True              -> True

    let disj a b =
        match a, b with
        | True, _    | _, True    -> True        // absorbing — matched first
        | Unknown, _ | _, Unknown -> Unknown
        | False, False            -> False

    /// Law 3. The ONLY SqlTruth -> bool in the codebase.
    let admits = function
        | True | Unknown -> true
        | False          -> false

    /// Comparisons propagate Unknown; IS NULL never does.
    let compare3 (f: 'a -> 'a -> bool) (l: 'a option) (r: 'a option) =
        match l, r with
        | Some a, Some b -> if f a b then True else False
        | _              -> Unknown

    let isNull (v: 'a option) = if Option.isNone v then True else False
```

### 3.2 — New rule: FSA-CF03

$$
\text{FSA-CF03}:\quad
\exists\, g : \operatorname{SqlTruth} \rightarrow \operatorname{bool},\;
g \neq \operatorname{SqlTruth.admits}
\;\Rightarrow\;
\operatorname{Violation}
$$

Authority `Core`, certainty `Certain`, engine typed-tree. Detectable structurally: any `match` on `SqlTruth` producing `bool` outside the sanctioned module. Law 3 becomes enforceable rather than aspirational.

### 3.3 — Three fixtures that are one-line defects

| Defect | Consequence | Fix |
|---|---|---|
| `^…$` in emitted .NET regex | `$` also matches before a trailing newline; `"PROD-001\n"` passes F#, fails PG | emit `\A…\z` |
| `String.Length` for `length(col)` | PG counts characters, .NET counts UTF-16 units; diverges on the first surrogate pair | `s.EnumerateRunes() |> Seq.length` |
| unbounded `NUMERIC` → `decimal` | PG range strictly exceeds `System.Decimal` | classify `Conditional`, emit range guard |

These belong in C-02's acceptance as named fixtures. Each is silent, each corrupts a fidelity claim, none is architectural.

---

## $\S 4$. C-01 Is the Schedule Risk, and It Is Avoidable

C-01 is $\pi_0\varepsilon_L$ and sits at the head of the longest dependency chain. It observes three competing parsers and targets one — but never names which survives. The `Expr` grammar it specifies (`Call`, `Cast`, `Binary`, `Regex`, …) is where `gram.y`'s difficulty actually lives: operator precedence, user-extensible operators with user-defined precedence, casts, quoted and schema-qualified identifiers, array types.

### 4.1 — Decode the authoritative tree; do not rebuild the grammar

`libpg_query` extracts PostgreSQL's own parser and returns its parse tree. It compiles to WebAssembly, so **one front end serves both the CLI and the browser path**, dissolving C-01's WASM branch entirely.

$$
\text{Claim}:\;
\text{PgPrism interprets } 3\% \text{ of the authoritative parse tree}
$$

$$
\text{not}:\;
\text{PgPrism reimplements } 3\% \text{ of the grammar}
$$

Unsupported nodes become typed `Unsupported` IR — which C-01 already requires, and which is far easier to guarantee against a total parse tree than against a partial grammar, where an omission is invisible.

$$\varepsilon_L \rightarrow \varepsilon_M \quad\text{(decoding, not parsing)}$$

### 4.2 — Decouple F-04 from C-01

Current spine: `C-01 → C-02 → C-04 → C-07 → F-04 → P-02`.

F-04's real dependency is on the **manifest schema**, not on the manifest being populated for all 40 occurrences. Lift the schema into X-03 and F-04 can proceed against a three-constraint manifest while C-01 is still in flight.

$$
\operatorname{Deps}(\text{F-04}) : \{\text{C-07}, \text{F-02}\}
\;\longrightarrow\;
\{\text{X-03}, \text{F-02}\}
$$

This removes $\varepsilon_L + 2\varepsilon_M$ from the critical path and lets $\mathcal F$ and $\mathcal C$ proceed in parallel — the single highest-leverage graph edit available.

---

## $\S 5$. Three Missing Evidence Obligations

### 5.1 — Policy-change impact (closes §2.5)

```fsharp
type VerdictDelta = {
    CaseId     : string
    OldVerdict : AssayVerdict
    NewVerdict : AssayVerdict
}

type PolicyImpact = {
    OldDigest    : string
    NewDigest    : string
    CorpusDigest : string
    Deltas       : VerdictDelta list
    Weakening    : bool          // ∃ delta moving toward Pass
}
```

$$
\operatorname{Weakening}(\Delta_J)
\iff
\exists d \in \operatorname{Deltas}:
\operatorname{Rank}(d.\operatorname{New}) < \operatorname{Rank}(d.\operatorname{Old})
$$

with $\operatorname{Rank}(\operatorname{Pass})=0 < \operatorname{Rank}(\operatorname{Inconclusive})=1 < \operatorname{Rank}(\operatorname{Fail})=2 \le \operatorname{Rank}(\operatorname{ToolFailure})$.

Law 12 is amended:

$$
\boxed{
\operatorname{JudgeChangeAccepted}
\iff
\operatorname{PolicyOnly}
\land
\operatorname{HumanApproved}
\land
\operatorname{ProtectedCI}
\land
(\neg\operatorname{Weakening} \lor \operatorname{WeakeningJustified})
}
$$

The reviewer now approves a **measured** change. Corpus is frozen; `CorpusDigest` joins $\mathcal J$.

### 5.2 — Publication-path enumeration

I-01 asserts a local bypass cannot produce a trusted release. That is the one claim in the document that is genuinely provable, and it is not stated as an obligation.

$$
\boxed{
\forall w \in \operatorname{Workflows}:\;
\operatorname{HasPublishCredential}(w)
\Rightarrow
\operatorname{ConsumesReducer}(w)
}
$$

**Acceptance:** enumerate every principal holding a publish credential — workflow, PAT, deploy key, environment secret, NuGet API key, container registry token. For each, show the reducer verdict is a required predecessor. An unenumerated credential holder is a hole regardless of how strong the gate is elsewhere. This is finite, auditable, and closes §13's real gap.

### 5.3 — Loss projection

C-07 counts $U$. A count tells you a gap exists; it does not let a reviewer read what was dropped. Add `LOSS.md` as a fourth deterministic projection of the envelope alongside SARIF, `PROOF.md` and the CI summary: one row per non-`Exact` occurrence with `qid`, source expression, fidelity, reason, owner, enforcement locus.

The habit is structural rather than aspirational when the artefact ships empty from day one.

---

## $\S 6$. Fidelity Domain, Polished

Two corrections: the direction of divergence is recoverable and operationally decisive; database ownership is not a defect.

```fsharp
/// Direction matters. These have different owners and different blast radii.
type Divergence =
    /// F# rejects rows PostgreSQL admits — breaks READS of resident data.
    | Stronger of reason : string
    /// F# admits rows PostgreSQL rejects — breaks WRITES, surfaces at INSERT.
    | Weaker   of reason : string

type Fidelity =
    | Exact
    | Conditional   of assumptions : string list
    | Approximate   of Divergence                        // was: string
    | DatabaseOwned of enforcer : string                 // NEW — passes strict
    | Manual        of owner : string * evidenceRef : string
    | Unsupported   of reason : string
```

$$
\operatorname{StrictAdmissible}(k)
\iff
\operatorname{Fidelity}(k) \in
\{\operatorname{Exact}, \operatorname{Conditional}, \operatorname{DatabaseOwned}, \operatorname{Manual}\}
$$

$$
\operatorname{Approximate} \lor \operatorname{Unsupported}
\;\Rightarrow\;
\text{Block}
$$

`DatabaseOwned` restores satisfiability (§2.3): `UNIQUE`, foreign keys and `EXCLUDE` are classified honestly, counted in $\mathcal K$, and pass — because PostgreSQL is enforcing them, which was always the correct answer.

$$
\operatorname{Unique}(sku) \not\equiv \operatorname{ValidSku}(sku)
$$

A smart constructor validates a value's shape. It cannot prove no other row holds it. That is not a translation failure; it is a correct division of labour, and the type system should be able to say so.

$$
\operatorname{Coverage} = \langle I, E, R, M, U, D \rangle
\qquad
D = |\{k : \operatorname{Fidelity}(k) = \operatorname{DatabaseOwned}\}|
$$

Strict release: $I=1$, $R=1$, $U=0$, $|\operatorname{Approximate}|=0$.

---

## $\S 7$. Schedule — What $\nu_3$ Does Not Compute

$\nu_3$ gives a portfolio interval and correctly refuses to divide it by headcount. It never computes the two numbers that determine whether this is doable.

### 7.1 — Effort by gate

| Gate | Requirements | Bands | Effort (person-days) |
|---|---|---|---:|
| $\Gamma_0$ Truth and trust | X-01, X-02, X-03, F-01, F-02, F-07, F-08 | $2\varepsilon_S + 5\varepsilon_M$ | **22 – 41** |
| $\Gamma_1$ Canonical semantics | X-04, C-01, C-02, C-03, C-04, C-07, P-01 | $5\varepsilon_M + 2\varepsilon_L$ | 36 – 65 |
| $\Gamma_2$ Trusted slice | F-04, P-02, P-03, I-01 | $2\varepsilon_M + 2\varepsilon_L$ | 24 – 44 |
| $\Gamma_3$ Differential evidence | C-05, F-03, F-05, P-04, P-05, I-02 | $4\varepsilon_M + 2\varepsilon_L$ | 32 – 58 |
| $\Gamma_4$ Multi-target | C-06, F-06, I-03 | $1\varepsilon_M + 2\varepsilon_L$ | 20 – 37 |
| | | | $\textbf{134 – 245}$ ✓ |

Recomposes to the stated portfolio interval exactly.

### 7.2 — Critical path

$$
\text{X-01} \prec \text{X-03} \prec \text{X-04} \prec \text{C-01} \prec \text{C-02} \prec \text{C-04} \prec \text{C-07} \prec \text{F-04} \prec \text{P-02} \prec \text{I-01} \prec \text{I-02} \prec \text{I-03}
$$

$$
\mathbb E_{\text{path}} \in [61, 112] \text{ person-days}
$$

$$
\frac{\mathbb E_{\text{path}}}{\mathbb E_{\text{raw}}} \in [0.45, 0.46]
$$

**Roughly half the portfolio is strictly sequential.** No amount of headcount moves the finish line below ~61 days of serial work, and this is a project running alongside a day job. §4.2's decoupling is the only structural lever: removing $\varepsilon_L + 2\varepsilon_M$ takes the path to approximately $[45, 83]$.

### 7.3 — $\Gamma_0$ ships alone

$\Gamma_0$ touches **no CanonFlow semantics, no IR, no parser, no Playground code.** It is 22–41 days and it buys the entire honesty property:

$$
\Gamma_0 \Rightarrow \neg\exists x:\; \operatorname{Verdict}(x)=\operatorname{Pass} \land \operatorname{Health}(x)\neq\operatorname{Complete}
$$

After $\Gamma_0$ the system still finds fewer defects than it eventually will — but it can no longer **claim** one it cannot support. Given that three of six inspected workflows are red and the Playground carries 140 committed SARIF results, that is the property with the highest marginal value per day, and it is available first.

Everything downstream of $\Gamma_1$ is capability. $\Gamma_0$ is integrity. Ship integrity.

---

## $\S 8$. Delta Summary

| # | Change | Target | Kind |
|---|---|---|---|
| 1 | Census emits; reviewed digest authorises | X-01 | Defect fix |
| 2 | Delegated identity stable; version an attribute | Law 8, F-06 | Contradiction fix |
| 3 | `DatabaseOwned` fidelity added | $\mathbb F$, C-07 | Satisfiability fix |
| 4 | Totality and reachability as separate obligations | F-07 | Assurance fix |
| 5 | `Broken` × `NonConformant` dual reporting | F-07 | Reporting fix |
| 6 | Kleene $K_3$ + FSA-CF03 + three fixtures | Law 3, C-02 | Missing semantics |
| 7 | `libpg_query`; F-04 decoupled from C-01 | C-01, F-04 | Schedule |
| 8 | `PolicyImpact`, publication enumeration, `LOSS.md` | Law 12, I-01, C-07 | Missing evidence |

Net requirement count unchanged at 27; two dependency edges rewritten, one rule added, one fidelity case added.

---

## $\S 9$. Decision

$\nu_3$ is correct in structure and should be executed. Its three real gaps are:

$$
\text{Law 3 without an implementation}
\;\wedge\;
\text{C-01 without a parser decision}
\;\wedge\;
\text{a ledger that cannot be satisfied}
$$

All three are closable inside the existing 27 requirements. None needs a new phase.

$$
\boxed{
\Gamma_0 \text{ alone}
\;\prec\;
\text{decide the parser}
\;\prec\;
\text{one slice}
\;\prec\;
\text{generalise}
}
$$

And the one line worth keeping from all of it, because it is the property every prior draft in this line lacked:

$$
\boxed{\;\operatorname{Pass} \Rightarrow \operatorname{Health}=\operatorname{Complete}\;}
$$

A verdict that cannot be produced from missing evidence is worth more than a verdict that finds more defects.

$$\blacksquare$$
