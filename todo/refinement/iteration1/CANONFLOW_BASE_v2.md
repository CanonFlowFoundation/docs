# CANONFLOW_BASE_v2.md — The Compliance Execution Constitution (Verified Integrated Edition)

> *Source → Interpretation → Type → Rule → Test → Review → Evidence → Verdict.*
>
> Implementation is never the first proof. A feature exists only when its
> contract, falsifier, custodian, release evidence, and truthful status exist.
> This document is the normative base for CanonFlow, GSTFlow, Rule Pack Studio,
> Crucible, CanonFlow Format, and every future `Flow(K)` product.

**Edition note.** This is the standalone integrated v2: the reviewed
`CANONFLOW_BASE.md` text carried in full, with every amendment earned by the
companion documents merged inline and marked ▲. Provenance for each ▲ is in
§47; the self-verification record is §48. A third-party review of the v2
redline was submitted twice and arrived empty both times; it is **not**
incorporated. This edition is verified against internal consistency only —
that limitation is stated here rather than hidden (§31 discipline applied to
the constitution itself). Adoption still requires the §29 ADR process.

---

## 0 · PRIME LAW

```text
∀K : Flow(K) = CF ⊕ K
```

```text
CF          = CanonFlow       (domain-agnostic verification substrate)
K           = Knowledge       (GST, EDI, or another governed domain)
Flow(K)     = CF ⊕ K          (a domain product)

GSTFlow     = Flow(GST)
EDIFlow     = Flow(EDI)
```

The operational form is larger because compliance truth needs people and
evidence as well as code:

```text
TrustedFlow(K) = CF ⊕ K ⊕ H ⊕ E

H = accountable human interpretation and review
E = reproducible evidence from tests, sources, signatures, and execution
```

Therefore:

```text
Code without H = unreviewed automation
Code without E = an assertion
H without replay = an opinion that cannot be reproduced

∴ Trust = Determinism × Provenance × Review × Contact
```

If any factor is zero, production trust is zero.

CanonFlow also governs transformations between representations:

```text
introspect(emit(domain)) ≅ domain       with classified loss
decode(encode(value))    ≅ value        with classified loss
normalize(normalize(x))  = normalize(x)
evaluate(normalize(x))   = evaluate(x)
```

`≅` never means "close enough." Every mismatch is emitted as evidence using
the fidelity law in §34. Silent semantic loss is unconstitutional.

### 0.1 · ▲ TERMINOLOGY RESERVATION LAW

Names are contracts. The following are reserved, project-wide, in code,
docs, sites, and generated files:

```text
CFF                          = CanonFlow Format. Only. Ever.
Forge                        = the AI candidate-generation line (DORMANT)
Constitution-First Software  = the public movement (never "CFF")
CDC / CDL / CRL              = Canon Dynamic Crucible / Dynamic Law /
                               Refinement Law (assurance layers)
OKF                          = Open Knowledge Format (emitted projection)
lat.md/                      = the human-authored knowledge graph
```

A name collision between an evidence container and anything else is a §34.2
drift surface. CI greps for retired vocabulary (`CFDL`, `CFRL`,
`CFFAssurance`, "CFF" meaning the Forge or the movement) and fails on
violation.

---

## 1 · MISSION LAW

```text
GSTFlow exists to help honest businesses detect preventable GST defects
before filing, payment, transport, claiming credit, or accepting evidence.
```

GSTFlow is:

```text
offline-first · deterministic · advisory · evidence-producing
structural · arithmetic · authenticity-aware · reconciliation-capable
```

GSTFlow is never:

```text
✗ an ERP                 ✗ accounting books
✗ a filing authority    ✗ a legal opinion
✗ a promise of credit   ✗ a penalty guarantee
✗ a cloud data trap     ✗ an AI oracle
```

The product statement is binding:

> **GSTFlow is an offline deterministic GST preflight engine. It identifies
> structural, arithmetic, authenticity, statutory, and cross-document risks;
> reports missing facts and evidence; and preserves a reproducible audit trail.**

Any stronger claim requires independent evidence and a constitutional amendment.

### 1.1 · SYSTEM OF PROOF LAW

```text
CanonFlow = SystemOfProof ≠ SystemOfRecord

AuthoritativeBusinessState(CanonFlow) = ∅
SourceMutation(CanonFlow)              = ∅
WriteBack(CanonFlow, ERP|Bank|Portal)  = ∅

Verdict = f(CanonicalInput, RulePack, EvaluationContext)
same(Input, Pack, Context) ⇒ same(Verdict)

revise(Input) ⇒ new(InputDigest) ⇒ new(Verdict) ⇒ new(CFF)
finalize(CFF) ⇒ immutable(CFF)

CFF = evidence-of-evaluation ≠ commercial-fact ≠ legal-approval
```

The forbidden operation is mutation or custody of authoritative business
state—not the bytes required to read evidence and create an explicit derived
artifact. CanonFlow holds no drafts and edits no source document. Correction
occurs in the source system and produces a newly identified evaluation.

```text
AllowedEffects = read(source)
               ∪ write(user-requested-derived-artifact)
               ∪ delete(ephemeral-or-derived-local-state)

AccountState = UserProfile = PersistedPreferenceState = ∅
VerdictAffectingConfiguration ⊆ EvaluationContext
```

No application account, user profile, or persisted preference is required. A
session choice that can change a verdict is explicit, validated, and captured
in the evaluation context; presentation state never enters statutory semantics.

---

## 2 · OWNERSHIP LAW

```text
Generic(x)       ⇒ x ∈ CanonFlow
GSTSpecific(x)   ⇒ x ∈ GSTFlow
Authoring(x)     ⇒ x ∈ Studio
Assurance(x)     ⇒ x ∈ Crucible
PortableProof(x) ⇒ x ∈ CFF
Distribution(x)  ⇒ x ∈ Registry
```

| Component | Owns | Must not own |
|---|---|---|
| `CanonFlow.Core` | verdict envelope, evidence, canonical digest, generic pack verification | GST sections, rates, invoice semantics |
| `Canon.Contracts` | canonical semantic publication model, AgentContext, OpenMetadata and OKF projections, fidelity reports | statutory truth, verdict selection, vendor-specific catalog state |
| `GSTFlow.Core` | GST canonical facts and document types | UI, OCR, network, database |
| `GSTFlow.Rules` | compiled GST laws and bounded rule evaluation | AI, portal submission, visual concerns |
| `CanonFlow.Studio` | authoring, examples, review workflow, signing request | final legal authority, unrestricted code generation |
| `CanonFlow.Crucible` | corpus execution, properties, mutation, agreement, proof manifest | deciding law by itself |
| `CanonFlow.CFF` | deterministic evidence bundle and interchange | mutable database semantics |
| `GSTFlow.Intake` | raw adapters and normalization | statutory verdicts |
| `GSTFlow.Reconcile` | typed cross-document matching | changing source evidence |
| `GSTFlow.UI` | Avalonia presentation and user workflow | duplicate rule logic |
| Registry | discovery, trust metadata, supersession, revocation | executing packs or receiving taxpayer data |

Deletion proof:

```text
delete(GSTFlow)  ⇒ CanonFlow and EDIFlow still build
delete(EDIFlow)  ⇒ CanonFlow and GSTFlow still build
delete(Studio)   ⇒ released packs still execute and replay
delete(Registry) ⇒ files still verify and transfer offline
delete(AI)       ⇒ every verdict and release test still works
```

Promotion into `CanonFlow.Core` requires all three:

```text
DomainNeutral(x)
∧ ExercisedInRealFlow(x)
∧ SecondFlowUsesUnchanged(x)
```

GSTFlow may prove a need; EDIFlow or another independent `Flow(K)` must prove
generality. Until then, the capability remains domain-owned. Premature reuse is
another form of coupling.

### 2.1 · BOUNDED DOMAIN HORIZON

CanonFlow and CFF are designed for independently governed knowledge domains;
that extensibility is a compatibility obligation, not a commitment to build
every possible product.

```text
Current active Flow       = GSTFlow
Dormant horizon           = ONDC · EPCIS · X12/EDI
Horizon(K)                ≠ ActivatedFlow(K)
DesignedFor(K)            ≠ Implemented(K) ≠ Proven(K)

CFF(K)                    = Envelope(v) ⊕ Profile(K,vₖ) ⊕ Evidence
DomainSemantics(K)        ∉ CanonFlow.Core
K₁ ≠ K₂                   ⇒ independent(Profile(K₁), Profile(K₂))
```

The dormant horizon is recorded in documentation and architectural invariants,
not as empty projects, placeholder UI modules, marketplace entries,
dependencies, or public capability claims. A second `Flow(K)` wakes only with
proven user demand, authoritative sources, a named domain custodian, a typed
model, a canonical profile, an independently reviewed corpus, adapter
conformance, and read-only effect proof.

```text
activate(Flow(K₂))
  ⇒ GSTFlow and CFF v1 remain independently buildable and replayable

activate(Intersection(K₁,K₂))
  ⇒ Proven(K₁) ∧ Proven(K₂) ∧ preserves-source-evidence
```

An intersection produces new derived evidence; it never mutates either source
domain or silently upgrades correlation into factual or legal certainty. A new
domain may add versioned profiles and namespaces, but must not redefine CFF v1
canonicalization, digest identity, safe-archive, replay, or evidence semantics.

---

## 3 · TRUST HIERARCHY

The expected result for a statutory test must come from a traceable oracle:

```text
Authority
  > reviewed interpretation record
  > independently checked worked example
  > hand-calculated expected amount
  > approved invariant or metamorphic relation
  > prior implementation output
```

The current engine is the weakest oracle. Never execute the implementation and
save its answer as the expected answer without independent derivation.

```text
AI suggestion ≠ authority
Test count     ≠ legal correctness
Type safety    ≠ legal correctness
Signature      ≠ legal correctness
▲ Machine proof ≠ legal correctness
```

A signature proves who approved specific bytes. A test proves that an
implementation violated or satisfied a stated proposition for tested inputs.
▲ A proof certificate shows that an artifact satisfies a **stated**
proposition over a modelled domain; the proposition's statutory adequacy
remains a human interpretation record. UI and documentation say "invariant
proven," never "correct law." Neither test, signature, type, nor proof
establishes that an interpretation of law is universally correct.

Facts and transformations also carry a provenance grade:

```text
Observed = read directly from preserved source evidence
Derived  = reproducibly calculated from identified inputs and function
Declared = asserted by an accountable author/reviewer
Guessed  = proposed by OCR/AI/heuristic and awaiting confirmation
Opaque   = origin or derivation cannot be established
```

```text
Guessed ∪ Opaque cannot independently justify Pass or Fail.
Derived must identify its inputs and derivation version.
Declared must identify its accountable declarant.
```

▲ Solver-produced counterexample witnesses are **Derived-grade** facts:
reproducibly calculated, with solver version, encoder version, and seeds
recorded as the derivation. The **expected outcome** for any case built from
a witness is authored and reviewed by humans — the solver finds the hole; it
never decides what belongs in it.

---

## 4 · ONE-ENGINE LAW

```text
verify : CanonicalFacts → EngineVersion → RuleContext → VerdictEnvelope
```

The authoritative verdict path is pure managed F#/.NET using
`System.Decimal`. Every shell calls this path. No shell reimplements it.

```text
∀ host ∈ SupportedHosts :
    canonical(host.verify(x, e, p)) = canonical(reference.verify(x, e, p))
```

Primary hosts:

```text
CLI             = .NET NativeAOT reference executable
Desktop         = Avalonia + managed F#/.NET
Mobile Pro      = Avalonia + managed F#/.NET, after platform proof
Web inspector   = Fable/JS only within proven compatibility
```

Forbidden:

```text
✗ Flutter/Dart verdict logic
✗ Fable-Dart monetary logic
✗ native ABI verdict bridge
✗ JavaScript Number as statutory money
✗ LLM-selected Pass/Fail
✗ UI-local rule calculation
```

Fable is a target, not an assumption. If the Fable host fails one canonical
decimal or verdict agreement case, it becomes an editor/inspector only until
agreement is restored.

Core results contain structured meaning, never localized prose:

```text
RuleResult = RuleId + Outcome + MessageKey + TypedParameters
             + Evidence + Provenance
```

Formatting, currency display, interpolation, and translation occur at the
presentation boundary. This prevents locale or runtime formatting differences
from corrupting canonical agreement.

---

## 5 · EXACT-MONEY LAW

```text
Money = Decimal(amount, currency, scale, provenance)

∀ monetary operation in VerdictPath : numericType = System.Decimal
```

No conversion through binary floating point is permitted.

```text
JSON wire value     = canonical decimal string
Avro candidate      = logicalType decimal with explicit precision and scale
DuckDB candidate    = DECIMAL(p,s) with proven round-trip agreement
UI display          = formatting only; never the oracle value
```

Every monetary policy must state:

```text
scope · scale · midpoint mode · aggregation order · tolerance · authority
▲ · dimension (inr | fraction | percent | per-unit price | quantity)
```

Boundary tests are mandatory immediately below, at, and above each approved
threshold. A tolerance is a reviewed policy, not a convenient constant.

```text
Forbidden = float ∪ double ∪ JS Number ∪ Dart double
            on any statutory monetary path
```

▲ **Units of measure are normative on the statutory monetary path:**

```fsharp
[<Measure>] type inr
[<Measure>] type percent
[<Measure>] type fraction

// 0.18m<fraction> ≡ 18.0m<percent>; the type system forbids confusing them.
// taxable * rate : decimal<inr> × decimal<fraction> → decimal<inr>   ✓
// taxable * 18m<percent>                             → FS0001         ✗
```

The 18-vs-0.18 rate defect compiles silently as bare `decimal` and is a real
statutory bug class; under this law it cannot compile. Measures are erased:
zero runtime, AOT, Fable, or serialization cost — wire values remain
canonical decimal strings. Bare-dimension decimal arithmetic in a monetary
position on the verdict path is an analyzer failure (T0/static gate).

▲ The canonical JSON monetary encoding is the named, versioned profile
`cff-json-decimal-string/1` — a strict grammar (sign, integer, fraction,
scale bounds, no exponent, no leading zeros). RFC 8785/JCS canonicalizes
numbers with IEEE-754 semantics and is therefore insufficient for
`System.Decimal`-grade money; CFF pins its own profile rather than adopting
JCS for monetary fields. The grammar, not the serializer, is the reviewed
artifact.

---

## 6 · OUTCOME AND UNCERTAINTY LAW

The engine must not compress uncertainty into success.

```fsharp
type RuleOutcome =
    | Pass
    | Warning
    | Unknown of MissingFact list
    | NeedsEvidence of EvidenceRequirement list
    | RequiresProfessionalReview of LegalIssue
    | Fail
```

Precedence is an explicit function, never the declaration order of a
discriminated union:

```text
severity : RuleOutcome → Severity
aggregate = reduce(severity, explicit policy)
```

Required laws:

```text
Fail present                 ⇒ Overall = Fail
Missing decision fact       ⇒ Overall ≠ Pass
Unsupported rule family     ⇒ explicit NotSupported evidence
Conflicting interpretations ⇒ RequiresProfessionalReview
```

Logical composition of unknown facts follows an explicit three-valued policy,
such as strong Kleene logic, rather than accidental Boolean defaults:

```text
Not(Unknown)             = Unknown
Violated AND Unknown     = Violated
Satisfied AND Unknown    = Unknown
Satisfied OR Unknown     = Satisfied
Violated OR Unknown      = Unknown
```

Statutory result aggregation is separate from predicate evaluation and remains
governed by the explicit severity function.

Individual UI may compress outcomes into green/amber/red, but the canonical
envelope retains the full state and the CA view exposes it.

---

## 7 · CANONICAL INTAKE LAW

```text
External → Raw → Parsed → Normalized → CanonicalFacts → Verify
```

The engine receives facts, not guessed documents. Intake preserves:

```text
raw value · parsed value · normalized value · confidence class · source pointer
```

Supported adapter families, activated by evidence gates:

```text
GST e-invoice JSON          CSV / Excel ledgers
ERP and accounting exports PDFs and scanned images
IRP signed QR payloads      GSTR export files
e-way-bill exports          manual entry
CFF evidence bundles        rule-pack and corpus files
```

Canonical document classes include:

```text
TaxInvoice · BillOfSupply · CreditNote · DebitNote
AdvanceReceipt · ImportDocument · ExportDocument · ISDDocument
Delivery/TransportEvidence · UnknownDocument
```

Rules:

```text
OCR/AI result = candidate fact
guessed critical field ⇒ confirmation required
normalization never overwrites raw evidence
adapter failure ⇒ diagnostic Result, never process crash
```

Primitive identity must survive refinement. Two values with the same runtime
representation but different proofs remain different types:

```text
Refined<decimal, TaxableValue> ≠ Refined<decimal, TaxAmount>
Refined<string, GSTIN>         ≠ Refined<string, InvoiceNumber>
Digest<Subject>                ≠ Digest<RulePack>
```

Phantom tags or erased units may encode the identity without runtime cost.
▲ §5's units-of-measure law is the normative instantiation of this
permission on the monetary path.

Every intake family declares three separate layers:

```text
L1 Contract validation     = structure, types, code lists, arithmetic
L2 Transport/integration   = portal/API/file movement, acknowledgements
L3 Bilateral/legal mapping = business meaning, master-data equivalence,
                              professional judgment
```

Passing L1 does not prove L2 delivery or L3 business/legal correctness.

---

## 8 · GST CAPABILITY LAW

The full capability map is a country; release gates select the road.

### 8.1 · Document integrity

```text
document type · invoice number · date · amendments
seller/buyer identity · GSTIN structure/checksum · state codes
line structure · HSN/SAC form · quantity/UOM · totals · duplicates
```

### 8.2 · Arithmetic and tax structure

```text
taxable value · discounts · assessable value · rate application
IGST versus CGST+SGST/UTGST exclusivity · cess · totals
line-to-document reconciliation · reviewed rounding policy
credit/debit adjustments · inclusive/exclusive utility calculation
```

### 8.3 · Statutory transaction rules

```text
Place of Supply for scoped goods/services branches
interstate/intrastate determination
Reverse Charge screening
time and value of supply checks
rate/exemption/notification applicability
composition-document restrictions
exports, SEZ, deemed exports, zero-rated evidence
advances, continuous supply, job work where facts support them
TDS/TCS and special mechanisms only as separately reviewed families
```

### 8.4 · Input tax credit preflight

```text
document/evidence presence · Section 16 condition screening
blocked-credit classification · time-bound screening
duplicate/already-claimed risk · reversal indicators
purchase-register versus statement reconciliation
```

GSTFlow reports screening risk and missing evidence. It does not guarantee
credit eligibility because receipt, use, payment, filing, litigation, and other
facts may sit outside the document.

### 8.5 · Authenticity and movement

```text
IRN and signed-QR parsing · signature verification · field agreement
IRN duplicate detection · invoice/QR mismatch
e-way-bill applicability warning · invoice/e-way-bill consistency
vehicle/transporter/document reference checks where imported
```

Verification is not generation or submission.

### 8.6 · Returns and reconciliation

```text
sales ledger ↔ GSTR-1/1A
purchase register ↔ GSTR-2B
GSTR-1 ↔ GSTR-3B
books ↔ e-invoice ↔ e-way-bill
period totals · exceptions · explainable match confidence
```

### 8.7 · Advanced governed families

```text
refund preflight · export/LUT evidence · ISD
multi-registration and branch transfer · special valuation
retrospective amendments · conflicting authority handling
audit workpapers and historical replay
```

Each family is `NotSupported` until its own sources, interpretation, corpus,
custodian, implementation, and contact gate are complete.

---

## 9 · CONTEXT LAW

An invoice is not the whole transaction.

```text
Decision = DocumentFacts ⊕ TaxpayerContext ⊕ TransactionContext
           ⊕ EvidenceContext ⊕ TemporalContext
```

Potential required context includes:

```text
registration type · turnover band · filing frequency · supplier status
recipient status · SEZ/LUT/bond · business purpose · goods/services class
receipt/payment dates · related-party facts · transport facts
applicable notification · evidence of receipt/use · return-period records
```

If a required fact is absent:

```text
¬fact ⇒ Unknown | NeedsEvidence | RequiresProfessionalReview
```

Never infer legal facts from a convenient proxy such as a buyer GSTIN when
delivery, use, contract, or place-of-supply facts are required.

---

## 10 · TEMPORAL AND REPLAY LAW

```text
verify(d, engineDigest, packDigest, effectiveContext) = constant
∀ wall-clock time
```

Every verdict records:

```text
input digest · normalization version · engine version/digest
rule-pack ID/version/digest · effective date · evaluation timestamp
rules evaluated · evidence references · canonical outcome digest
```

Published artifacts are immutable. Correction creates a successor with:

```text
supersedes · reason · affected period · migration/re-evaluation guidance
```

The system must distinguish:

```text
law effective date
notification/publication date
pack publication date
evaluation date
retrospective applicability
```

Historical packs remain available for replay. The newest pack never silently
rewrites an old verdict.

---

## 11 · RULE CAPABILITY AND UPDATE LAW

The original binary-versus-pack split is retained, but Studio requires a
precise three-class model.

### 11.1 · Class P — Parameter pack

```text
P = tables, identifiers, dates, rates, mappings, templates, public keys
```

Examples:

```text
HSN/SAC rate map · state codes · UOM · notification effective dates
IRP public keys · language templates · compatibility metadata
```

Class P contains no predicates or control flow. It may be imported after
signature, schema, compatibility, and effective-date verification.

### 11.2 · Class D — Bounded decision pack

```text
D = finite acyclic decision graph over allowlisted facts and operators
```

Class D is data, not native code, but it can influence a verdict through a
fixed total interpreter. Therefore it receives a higher trust gate:

```text
finite graph · no loops · no recursion · no reflection · no I/O
no user-defined functions · bounded nodes/depth/time · decimal-only money
declared fact dependencies · exhaustive branches · explicit uncertainty
dual statutory review · mutation tests · Gold Corpus · revocable signature
```

Class D is not enabled for general distribution until the interpreter,
resource bounds, governance, and adversarial-pack suite are independently
reviewed.

▲ **THE DECIDABILITY DIVIDEND.** The Class D bounds were imposed for
security and interpreter cost. The same bounds purchase, at zero additional
restriction:

```text
1. IR extractability          (the graph is the portable artifact)
2. SMT decidability            (linear rational arithmetic + enumerated
                                sorts + epoch-day integers ⇒ the §13/§14
                                proof obligations are dischargeable)
3. Termination by construction (nothing to prove; nothing for an author
                                or model to get wrong)
```

Three dividends from one restriction. Any future proposal to relax a Class D
bound must account for all three losses, not only the first.

### 11.3 · Class E — Engine change

```text
E = new fact type, operator, outcome semantics, canonicalization, algebra,
    cryptographic policy, or document class
```

Class E requires source change, compilation, complete Crucible execution, and
engine release. Studio may prepare a proposal, but cannot publish it as a pack.

```text
rate/date/table change          ⇒ P
bounded reviewed decision tree ⇒ D
new semantic capability        ⇒ E
arbitrary source/binary        ⇒ REJECT
```

### 11.4 · Dhall disposition

```text
Decision = ABSORB THE PRINCIPLES; REJECT THE PRODUCT RUNTIME
```

Dhall has valuable properties for configuration: strong types, normalization,
total evaluation, pure expressions, semantic hashes, and frozen imports. Those
ideas reinforce CanonFlow's canonicalization and signed-pack laws. They do not
justify adding Dhall as a GSTFlow execution language.

Rejected uses:

```text
Dhall in CanonFlow.Core or GSTFlow.Rules              ✗
Dhall as Class D rule-pack bytecode                    ✗
Dhall Double for money, rates, thresholds, or rounding ✗
Dhall interpreter/CLI shipped with desktop or mobile   ✗
Dhall imports resolved during verification             ✗
Dhall as a second canonical semantic model              ✗
```

Reasons:

1. Dhall's native numeric types are `Natural`, `Integer`, and IEEE-754
   `Double`; it has no native fixed-scale decimal corresponding to
   `System.Decimal`. Encoding money as text or scaled integers would create an
   additional conversion contract rather than remove one.
2. Total evaluation guarantees termination, not a small or predictable cost.
   The Dhall safety guidance explicitly acknowledges pathological expressions
   whose finite normalization is still impractically long. Class D requires
   explicit node, depth, time, and memory bounds.
3. Dhall permits file, URL, and environment imports. Frozen semantic hashes are
   useful, but GSTFlow's offline verifier must perform no import resolution at
   verdict time.
4. The official Dhall integration list does not include .NET, F#, Fable, or
   Avalonia. Shipping an external executable or native binding would add a
   packaging, FFI/process, crash, update, and cross-host agreement surface.
5. Studio already needs a domain-specific finite Rule IR with evidence,
   effective dates, uncertainty, operator allowlists, and legal-review
   metadata. Dhall's general configuration calculus does not supply those
   statutory semantics.

Conditionally permitted outer-loop use:

```text
developer-only Dhall
→ generates non-authoritative JSON/YAML scaffolding
→ generated artifact is committed
→ canonical schema/fidelity/Crucible validation runs
→ reviewers sign only the canonical artifact
→ released product has no Dhall dependency
```

Allowlisted use cases:

| Use case | Generated output | Dhall is justified only when | Mandatory boundary |
|---|---|---|---|
| CI build/test matrix | committed GitHub Actions YAML or test-run JSON | OS × RID × host × configuration combinations are materially duplicated | generated workflow cannot alter release approval, signing, or expected verdicts |
| NativeAOT packaging matrix | committed build scaffolding for supported RIDs/artifacts | three or more packaging variants share the same typed structure | signed release manifest is produced by the release pipeline, never by Dhall |
| Conformance execution matrix | adapter × format × host × operation permutations | hand-maintained permutations are drifting or missing cases | Dhall selects what to run, never what result should pass |
| Cross-repository engineering boilerplate | workflow fragments, issue-form data, labels, contributor checks | CanonFlow, GSTFlow, and EDIFlow repeat the same configuration | repository-specific security and release policy remains explicit and reviewed |
| Capability/status document projection | committed JSON/YAML/Markdown tables used by README or site | one evidence-linked status record is repeated across three or more surfaces | generation cannot promote `STUBBED`/`EXPERIMENTAL` to `IMPLEMENTED`/`PROVEN` |
| Test-case skeleton generation | case IDs, folders, empty records, boundary-slot scaffolds | reviewers repeatedly create the same non-semantic structure | no facts, legal references, expected verdicts, amounts, or Gold-oracle values are generated as truth |
| Demo and sample topology | non-Gold synthetic manifests and example wiring | several demos require the same typed topology | output is labeled demonstration and cannot enter the Gold Corpus |
| Localization inventory | message-key lists and placeholder-shape manifests | UI, CLI, and share artifacts duplicate the same key inventory | Dhall does not author legal meaning, translated prose, interpolation values, or verdict parameters |
| Developer environment profiles | formatter/linter/test-tool JSON or YAML | contributors need several reproducible tool profiles | no secrets, taxpayer data, runtime feature flags, URL imports, or environment-variable imports |

Explicitly forbidden even in developer tooling:

```text
GST rates, thresholds, exemptions, effective dates, and legal mappings
Rule IR, decision graphs, predicates, outcome precedence, or rounding policy
Gold Corpus facts or expected results
CFF payloads, digests, signatures, proof manifests, or replay inputs
trust stores, signer policy, revocation data, or release approvals
CanonicalFacts, OpenMetadata, OKF, or AgentContext semantic projections
runtime feature flags that can change a verdict
```

This exception requires a named maintainer and demonstrated reduction of at
least three materially duplicated configurations. A Dhall source file is never
the signed pack, replay input, Gold oracle, CFF truth, or production verdict
dependency.

Adoption gate:

```text
three real duplicated configurations
∧ measured reduction in hand-maintained variants
∧ pinned Dhall tool/version/checksum
∧ local or frozen imports only; no URL/env imports
∧ generated files committed and human-reviewable
∧ CI regeneration followed by zero-diff check
∧ clean clone builds/tests from generated files without Dhall installed
∧ named maintainer + removal procedure
```

If this conjunction is false, use plain JSON/YAML or existing F# tooling. Dhall
is not introduced merely because a configuration could be written in it.

CanonFlow should absorb these Dhall principles without its runtime:

```text
normal form before digest · typed configuration · no ambient effects
pinned dependency hashes · explicit imports · reproducible generated output
```

---

## 12 · RULE PACK STUDIO LAW

Studio is a statutory authoring and review instrument, not a code vending
machine.

```text
Source material → Interpretation record → Visual graph/table
→ Candidate Rule IR → Crucible → Human review → Signature → Pack
```

Required features:

```text
visual decision graph          typed fact/operator palette
parameter-table editor         effective-date timeline
legal-source attachments       interpretation record
worked-example editor          boundary partition assistant
missing-branch diagnostics     unreachable/cycle detection
Gold Corpus runner             property and mutation runner
pack diff                      impact report
author/reviewer workflow       signing and export
revocation/supersession draft  proof-manifest viewer
```

Studio normalizes candidate graphs into a canonical form before review:

```text
normalize : CandidateRuleIR → NormalizedRuleIR

normalize(normalize(r)) = normalize(r)
evaluate(normalize(r))  = evaluate(r)
```

Normalization performs duplicate elimination, safe interval intersection,
contradiction detection, stable ordering, unreachable-node reporting, and
exhaustiveness analysis. A contradiction becomes a blocking diagnostic; it is
never optimized into an unexplained result.

▲ Where the proof backend (§13) is awake, Studio's exhaustiveness analysis
and contradiction detection are **witness-backed**: a missing-branch
diagnostic carries the concrete uncovered band (e.g. the ₹0–₹2,49,999
interval no branch mentions), and a contradiction diagnostic carries a
concrete fact assignment satisfying two branches. Structural analysis remains
the fallback when the backend is dormant or a check returns Unproven. A
witness-backed blocking diagnostic is shown to the author verbatim.

Primary implementation:

```text
CanonFlow.Studio = Avalonia + F#/.NET desktop utility
```

Desktop is primary because large corpora, filesystem access, certificate
stores, PKCS#11/DSC hardware, and offline review are first-class requirements.

Optional browser Studio Lite may draft graphs, run structural checks, inspect
packs, and export unsigned candidates. It cannot become the production signer
or a second semantic engine without all agreement and security gates.

Forbidden:

```text
✗ arbitrary F# generation for distributed execution
✗ arbitrary DLL/assembly packs
✗ author self-approval
✗ AI auto-publication
✗ hidden legal assumptions
```

---

## 13 · CRUCIBLE LAW

Crucible is an executable assurance product, not scattered workflow YAML.

```text
crucible restore
crucible validate-source
crucible validate-pack
crucible run-examples
crucible run-boundaries
crucible run-properties
crucible run-mutations
crucible compare-hosts
crucible test-aot
crucible test-security
crucible produce-proof
▲ crucible prove-graph     (DORMANT — wakes per §36: Class P table
                            obligations at Gate 6; full Class D battery
                            at Gate 7)
▲ crucible run-dynamic     (DORMANT — CDL protocol laws; wakes after CFF
                            v1 PROVEN, per CANON_DYNAMIC_CRUCIBLE.md §16)
```

The proof manifest records:

```text
source digests · interpretation digests · engine digest · pack digest
corpus digest · toolchain versions · seeds · test results
critical mutations · target agreement · AOT/trim warnings
review identities · artifact digests · known limitations
```

▲ Where the corresponding layer is awake, the proof manifest additionally
records: solver version/checksum · Graph→SMT encoder version ·
per-obligation proof outcomes (`Proven(certificate) | Refuted(witness) |
Unproven(reason)`) · certificate digests · unsat cores and counterexample
models · dynamic-law IDs with exploration policies · **collapse verification
per action family** (`[a]p ↔ ⟨a⟩p` checked by N-run and cross-host replay —
the kernel's membership card, named as such) · the **assumption ledger**
(§46.8).

A green badge without these inputs is insufficient evidence.

---

## 14 · TESTING LAW

### 14.1 · Test layers

```text
T0 repository integrity
T1 domain constructors and invalid-state prevention
T2 exact arithmetic and boundary matrices
T3 reviewed statutory examples
T4 FsCheck properties with reproducible seeds and shrinking
T5 metamorphic relations
T6 mutation testing
T7 Gold Corpus
T8 cross-host canonical agreement
T9 pack/parser/security fuzzing
T10 NativeAOT and FFI survival
T11 Avalonia headless and critical end-to-end flows
T12 historical replay and migration
```

### 14.2 · Required general properties

```text
same pinned input ⇒ same canonical verdict
serialization round-trip preserves meaning
missing required fact never becomes Pass
Fail cannot be hidden by weaker outcomes
irrelevant-field change does not alter a rule
expired/incompatible pack is rejected
every finding names a rule and evidence path
every crashable input becomes a typed diagnostic
arbitrary garbage at every public parser never escapes as an unhandled error
bounded reference corpus completes within its declared resource budget
```

### 14.3 · Mutation operators

```text
> ↔ >= · All ↔ Any · remove exception · alter decimal constant
swap IGST/CGST · bypass effective date · reorder severity
accept invalid signature · omit digest · truncate evidence
```

Coverage percentage is telemetry. Release approval depends on killing every
identified critical mutation and satisfying the governed corpus.

Snapshots and golden outputs are review artifacts, not self-writing oracles:

```text
missing snapshot ⇒ test failure + explicit review action
changed snapshot ⇒ reviewed semantic diff
test execution   ⇒ never auto-approves a new expectation
```

Skipping unsupported generated cases is permitted only when the fidelity report
records why they were skipped and the release capability manifest excludes the
claim. `if unsupported then true` without evidence is a vacuous test.

### 14.4 · ▲ Modal obligation classification

Every declared test obligation carries a modality and a discharge class:

```text
modality  = box      (universal: after the action, p ALWAYS holds)
          | diamond  (witness: the outcome is REACHABLE)

discharge = example | vector          → proves a diamond, fully, by one run
          | property (seeded)         → samples a box; truth at tested
                                        states, NEVER validity
          | proof (SMT / model-check) → discharges a box over a decidable
                                        fragment, with certificate
          | mutation                  → measures the suite's power to refute
          | referential-check         → resolves declared links only
          | trace-resolution          → resolves factRefs/sourceRefs/traceRefs
```

Rules: a box claim discharged only by an example is invalid · a diamond
advertised as universal in docs is a §31 violation · `PassedSamples` is never
relabelled `Proved` · fuzzing that finds nothing licenses exactly "no witness
found within this corpus and budget," never `[attack]¬Bad`.

### 14.5 · ▲ The vacuous-truth guard

`[BLOCK]p` is vacuously true because nothing terminates. Therefore every
required action carries a separate executability obligation
`Enabled(s,a) → ⟨a⟩true`, and reports explicitly detect: disabled action ·
zero explored transitions · divergence · timeout · vacuous box success. This
formalizes 14.3's `if unsupported then true` law and gives it a mechanical
detector. Making an action unreachable to win a vacuous pass is a rejected
result, and (for the dormant Forge) a rejected reward.

### 14.6 · ▲ Iterated invariants use induction pairs

An invariant over an iterated action (`[a*]p`) is declared as an induction
pair — initial establishment plus per-step preservation (`p ∧ [a](p)`) —
never as an end-state-only assertion, because an end-state check cannot
localize which step broke the invariant. FsCheck model-based testing is the
sampled form of the premise; the §46.4 run-ledger conservation law is proven
by exactly this pattern.

---

## 15 · AI-ASSISTED ASSURANCE LAW

AI is an adversarial assistant outside the deterministic release path.

### 15.1 · Permitted work

```text
extract candidate propositions from cited sources
propose decision tables and missing branches
generate boundary partitions and synthetic facts
propose FsCheck properties and mutations
compare source/pack versions
cluster failures and minimize explanations
detect documentation/code/status disagreement
draft review notes and lay explanations
```

### 15.2 · Forbidden authority

```text
AI expected verdict          ✗ unless independently derived and approved
AI changing Gold expectation ✗
AI merging statutory PR      ✗
AI signing/publishing pack   ✗
AI receiving taxpayer PII    ✗ unless locally authorized and controlled
AI in production verdict     ✗
```

### 15.3 · Candidate-to-proof pipeline

```text
AI proposal
→ CandidateCase status
→ schema/static validation ▲ (includes the proof battery where awake)
→ statutory author review
→ independent reviewer derivation
→ ApprovedCase status
→ committed deterministic artifact
→ Crucible execution without an LLM
```

▲ An AI-proposed Class D graph arrives at human review carrying either
certificates or witnesses, so reviewers spend attention on statutory meaning
rather than coverage holes a solver finds in milliseconds. The gates bind the
**artifact, not the author** — a CA-drawn graph and a model-proposed graph
face identical obligations. This narrows AI authority: one more mechanical
gate between proposal and approval.

Agent-generated changes are PR-only, time/diff bounded, and require witnessed
red-run evidence before implementation. Model name, prompt/template digest,
source digest, and human disposition may be retained for provenance, but the
release must not depend on reproducing a model response.

AI agents receive an emitted, machine-readable contract rather than guessing
from source code or prose:

```text
AgentContext = allowed facts + refined constraints + lineage grades
               + fidelity gaps + safe operations + forbidden operations
               + schema/pack/engine digests
```

The context is generated from the same canonical model as runtime validation.
If an emitter cannot represent a constraint exactly, the limitation is included
as data. The agent is never shown an approximate contract as exact truth.

### 15.4 · Semantic publication surfaces

OpenMetadata and OKF were previously implicit in "agent context," lineage, and
fidelity. That is insufficient. They are distinct publication contracts with
different consumers and must be named, versioned, and tested explicitly:

```text
Canonical Semantic Model
  ├─ AgentContextProjection   → bounded runtime context for an AI/tool
  ├─ OpenMetadataProjection   → enterprise catalog, ownership, lineage, discovery
  └─ OkfProjection            → portable Git-native human/agent knowledge bundle
```

| Surface | Purpose | Authority |
|---|---|---|
| Canonical Semantic Model | loss-aware internal meaning shared by emitters | source for all projections; not a GST verdict by itself |
| AgentContext | minimum facts, constraints, provenance, fidelity gaps, and allowed operations for a specific agent task | operational context only |
| OpenMetadata | integration with enterprise metadata catalogs, governance, ownership, lineage, and discovery | descriptive projection; never the statutory source of truth |
| OKF — Open Knowledge Format | portable Markdown plus YAML-frontmatter knowledge that people and agents can inspect, diff, and version | publication projection; never executable rule authority |

`OKF` means **Open Knowledge Format**. It does not mean Open Knowledge
Foundation or Open Knowledge Framework. Those expansions refer to different
things and must not appear in CanonFlow claims, types, docs, or generated files.

The two external projections complement rather than replace each other:

```text
OpenMetadata = enterprise catalog plane
OKF          = portable knowledge plane
AgentContext = least-authority execution plane
Verdict      = deterministic GSTFlow rules plane
```

Publication law:

```text
canonical model first
→ projection with pinned target version
→ schema/spec validation
→ classified field-level fidelity report
→ round-trip or semantic-equivalence fixtures where possible
→ explicit unsupported fields where equivalence is impossible
```

No external format may silently lose a refined constraint, effective date,
precision/scale, provenance grade, confidence/uncertainty, evidence link,
parameter, or safe-operation boundary. Vendor extension fields use the target
standard's supported extension mechanism; invented top-level fields are not
called compliant.

Current implementation finding on 2026-07-14:

```text
src/Canon.Contracts/OpenMetadata.fs = candidate projection; EXPERIMENTAL
src/Canon.Contracts/OkfCatalog.fs   = OKF-style catalog; not OKF v0.1
src/Canon.Emit/OkfEmitter.fs        = candidate emitter; EXPERIMENTAL
```

The current OpenMetadata JSON is an ad-hoc candidate until it validates against
a pinned official OpenMetadata entity schema/API. Fields such as constraints,
AI lineage, and safe queries must use supported schema fields, custom
properties, or separately governed artifacts.

The current OKF emitters do not yet establish OKF v0.1 conformance. In
particular, OKF requires a directory of Markdown resources with YAML
frontmatter and a required `type` field. "OKF-style Markdown" is an honest
intermediate status, not a compliance claim. Rename misleading expansions now;
rewrite and validate the projection before marking it `IMPLEMENTED` or
`PROVEN`.

▲ **TWO KNOWLEDGE PLANES, ONE BOUNDARY EACH:**

```text
OKF        = machine-EMITTED projection OF canonical semantics
             (pinned spec, fidelity-classified, generated)
lat.md/    = human-AUTHORED, reviewed prose ABOUT the system
             (explanations, rationale, workflows, decisions)

lat may LINK to OKF artifacts        ✓
lat may restate OKF wholesale        ✗ (drift)
OKF emitters may generate lat nodes  ✗ (generated prose ≠ reviewed knowledge)
either claiming the other's role     ✗ (§34.2 drift surface)
```

### 15.5 · ▲ THREE-PLANE AGENTCONTEXT COMPOSITION

Three governed documents emit "agent context." They are planes of one
context with strict authority ordering, never three competing briefings:

```text
AgentContext(task) = PacketProjection(task)     ← AUTHORITY
                     (work-packet validator: permissions, digests,
                      allowed/forbidden effects, commands, stop
                      conditions, approvals)
                   ⊕ CanonicalProjection(task)  ← SEMANTICS
                     (this section: schemas, refined constraints,
                      lineage grades, fidelity gaps)
                   ⊕ LatSections(task)          ← EXPLANATION
                     (reviewed narrative; enters as UNTRUSTED DATA)

Authority ∈ PacketProjection only. A lat node asserting a permission the
packet denies is a drift finding, not an authorization. One validator, one
emitter, one graph — three folds, one source of authority.
```

---

## 16 · GOLD CORPUS LAW

```text
Gold = PublicSynthetic ⊕ RestrictedSanitized ⊕ HistoricalRegressions
```

Every case contains:

```text
case ID · facts · expected outcome/value · applicable period
authority references · interpretation record · boundary rationale
author · legal reviewer · technical reviewer · approval status · digest
```

Every confirmed defect adds:

```text
minimal reproducer ⊕ surrounding boundary family ⊕ missed-risk explanation
```

Corpus privacy and licensing are explicit. Real invoices are never committed
without permission and sanitization. Synthetic data must remain legally
meaningful rather than random noise.

`Contact ≥ 1` is separate: a synthetic corpus cannot replace an external user
or professional validating the product on a real workflow.

---

## 17 · HOST AND DEPLOYMENT LAW

### 17.1 · Avalonia

```text
GSTFlow Desktop = Avalonia UI + F#/.NET
GSTFlow Mobile Pro = Avalonia only after platform-specific proof
```

Testing order:

```text
view-model/service tests → Avalonia headless tests
→ visual regression where meaningful → critical real-platform automation
```

### 17.2 · NativeAOT

```text
build success ≠ NativeAOT success
```

CI publishes the real artifact, treats unresolved relevant AOT/trim warnings as
release blockers, launches the binary, and executes the canonical corpus.

### 17.3 · Fable

```text
Fable host = supported only if agreement suite is green
```

The web gateway may provide stateless intake, draft inspection, and preflight
only within proven numeric/serialization compatibility. It is never assumed to
inherit `System.Decimal` semantics automatically.

### 17.4 · Platform activation

```text
Desktop + CLI first
Web only after agreement
Mobile only after demand, lifecycle, camera, signing, and AOT proof
```

No platform exists merely because a project compiles.

---

## 18 · CFF LAW

CanonFlow Format is a portable evidence container, not a magical database.

```text
CFF = deterministic archive(manifest, sources, normalized facts,
                            verdicts, evidence, attachments, proof)
```

▲ Normative logical layout (amended per `cff.invoice-evidence/1`):

```text
manifest.json
sources/
facts/
verdicts/
evidence/
attachments/
evaluation/context.json        ▲ execution context (see below)
proof/proof-manifest.json      ▲ normative machine-readable evidence
                                 (renderings beside it are DERIVED ONLY)
signatures/
```

Required properties:

```text
path safety · size/count limits · per-entry digests · canonical manifest
schema versions · engine/pack digests · privacy classification
optional source inclusion · deterministic ordering · safe extraction
```

▲ **Evaluation context.** `evaluation/context.json` makes the determinism
tuple explicit: engine id+version+buildSha, rule-pack id+version+sha,
parameter-pack and interpretation-snapshot digests, jurisdiction,
effectiveAt, executionProfile, canonicalizationProfile. A digest alone
cannot reproduce a decision; "same rule-pack digest" **never** implies "same
verdict." The replay claim is over `(input, engine, packs, context)`.
`evaluatedAt` lives in the verdict record (§10 provenance), never in the
context, because the timestamp must not determine the result.

▲ **Acyclic digest model.** Manifest entries = all payload entries
**excluding** `manifest.json` and `signatures/**`. `payloadDigest` = SHA-256
of the exact canonical manifest bytes. Signatures sign a scope object
containing `payloadDigest`. No self-reference, no signature cycle; multiple
signatures attach without changing payload identity.

▲ **Envelope purity.** Canonical verdict envelopes carry the full §6 outcome
algebra and `messageKey + typed params` only — a prose message inside
canonical content is a profile-validation failure, because locale-dependent
bytes corrupt cross-host canonical agreement (§4). No probabilistic
confidence score in statutory outcomes (§22).

▲ **Explicit redaction.** An omitted or redacted attachment appears in the
manifest with `redacted: true` and a reason code. The bundle states what it
does not contain — §6 discipline applied to the container itself.

▲ **Governed ID schemes.** `gstref://` (statutory sources) and `fact://`
(canonical fact paths) are registered vocabularies; an unregistered scheme
in a bundle fails validation. Verdicts cite source IDs, never inline web
links.

▲ **Evidence ≠ proof, modally.** `evidence/trace.json` documents one
**diamond witness** — why this evaluation produced its verdict;
`proof/proof-manifest.json` reports seeded **box-discharge attempts** over
the engine and pack. Conflating them claims a box from a witness (§14.4).
Bundle validation resolves every traceRef/sourceRef/factRef.

Storage evolution:

```text
v1 canonical JSON        = first interoperable proof
Avro                     = enabled after decimal/union round-trip proof
Parquet/Arrow            = enabled for analytical projection after proof
DuckDB                   = optional query/index sidecar, never verdict oracle
```

Digest is not signature. Encryption is not authenticity. Signature is not
legal correctness. UI and documentation must use the exact term implemented.

---

## 19 · RULE-PACK AND SIGNATURE LAW

```text
RulePack = manifest ⊕ content ⊕ proof ⊕ approvals ⊕ signatures
```

Verification order:

```text
safe container parse
→ canonical digest
→ signature and certificate/trust policy
→ revocation/supersession state
→ schema and engine compatibility
→ jurisdiction/effective period
→ semantic validation
→ activation
```

Trust roles are separate:

```text
author approval        = professional identity/DSC workflow where validated
review approval        = independent signer
release signature      = Foundation release key
transport integrity    = SHA-256 digests
```

▲ **Two signature meanings, never conflated, both labelled in manifests:**

```text
Build attestation    (machine key)   = "this artifact is byte-identical to
                                        what passed these gates" — attests
                                        IDENTITY
Statutory approval   (offline key)   = "this rule/evidence is authorized
                                        for its declared purpose" — attests
                                        PROMOTION
```

▲ **Wire form:** detached CMS/PKCS#7 with a `scope.json` stating
`payloadDigest` · signer role · intent (`prepared` | `reviewed` |
`approved-for-specified-scope`) · covered period and jurisdiction ·
signature-policy identifier — mapped bijectively onto this section's trust
roles. "CA" is never used unqualified (Chartered Accountant vs Certifying
Authority); a PEM certificate is not a signature; a private key is never
packaged.

▲ **Key hygiene:** statutory signing keys are never accessible to: any
AI/model process · compiler or test sandboxes · CI workers · training
environments · any process that executes generated or candidate code.

Browser Web Crypto is not assumed to support government DSC USB-token flows.
Production signing belongs in the desktop Studio through a validated OS
certificate-store or PKCS#11/provider integration.

Offline revocation uses signed trust snapshots and explicit expiry/staleness
warnings. The engine never silently trusts an unknown signer.

---

## 20 · AUTHENTICITY LAW

```text
Authentic(payload) ≠ CorrectTaxTreatment(payload)
```

QR/e-invoice verification requires:

```text
real payload parser · algorithm allowlist · trusted key set
signature verification · certificate/key validity policy
QR-to-visible-invoice field agreement · malformed-input fuzzing
known-answer vectors · key rotation and staleness handling
```

The result distinguishes:

```text
SignatureValid · SignatureInvalid · KeyUnknown · PayloadMalformed
FieldsAgree · FieldsMismatch · VerificationNotSupported
```

No hardcoded success, demo key, or base64 decode may be presented as signature
verification.

---

## 21 · RECONCILIATION LAW

```text
reconcile : SourceSet → MatchGraph → Exceptions → Evidence
```

Reconciliation is not one Boolean. It produces:

```text
exact match · deterministic normalized match · candidate match
missing left · missing right · duplicate · conflicting amounts
period mismatch · amendment chain · unresolved
```

Rules:

```text
source records remain immutable
normalization version is recorded
fuzzy/AI match never becomes exact without confirmation
every aggregate drills down to documents
DuckDB accelerates selection/aggregation, not statutory judgment
```

Initial high-value order:

```text
purchase register ↔ GSTR-2B
sales ledger ↔ GSTR-1
GSTR-1 ↔ GSTR-3B
invoice ↔ IRN/QR ↔ e-way bill
```

---

## 22 · AI PRODUCT LAW

AI features are optional peripherals:

```text
PDF/image extraction · source summarization · candidate classification
natural-language query drafting · explanation drafting · voice NLU
```

Required quarantine:

```text
AI output labeled · confidence class not false percentage
critical facts confirmed · prompt injection treated as hostile input
no tool authority · no signing key access · no verdict mutation
deterministic template remains explanation of record
```

Local AI may run out-of-process. Its crash, timeout, thermal limit, or model
absence must degrade to manual/deterministic workflows.

```text
delete(AI) ⇒ validate, explain-by-template, replay, export all remain true
```

---

## 23 · FFI AND CRASH-CONTAINMENT LAW

F# reduces some error classes; it is not immunity from process crashes.

```text
Managed verdict kernel = no unsafe/native dependency
Native peripheral      = explicit adapter + bounded data contract
```

Native candidates include OCR, camera codecs, DuckDB, compression, and local
AI runtimes. Every boundary requires:

```text
ABI/version check · ownership/lifetime specification · cancellation policy
size limits · malformed-response tests · repeated open/close soak
missing-library behavior · crash recovery · platform matrix
```

Where feasible, crash-prone native work runs in a helper process:

```text
native crash ⇒ operation failure + recoverable diagnostic
             ≠ corrupted verdict or lost workspace
```

Money crossing a peripheral boundary uses canonical decimal strings or scaled
integers with explicit scale; never `double`.

---

## 24 · OFFLINE AND PRIVACY LAW

```text
VerdictPath ⇒ ¬Network
```

Product modes:

```text
Web inspector     = stateless by default
Installed utility = local workspace, explicit retention and wipe
Backup/transfer   = user-carried file
```

"Read-only" is a source-system and authority boundary, not a claim that the
process writes no bytes. GSTFlow may create explicit local derived indexes,
proof material, diagnostics, and user-requested CFF exports; it never mutates
source records, filed returns, ledgers, portal state, or accounting books. Any
local state is disclosed, removable, non-authoritative, and never the sole copy
of taxpayer evidence.

Privacy features:

```text
offline badge · network-call evidence · storage disclosure
one-tap wipe · source-inclusion choice · privacy receipt
no accounts · no telemetry in verdict path · no silent crash upload
```

Online operations, if ever added, are explicit adapters outside verification
and disabled by default. Offline claims must be demonstrated by tests and
observable behavior, not copy alone.

---

## 25 · PRODUCT AND UX LAW

The individual utility remains:

```text
give document → confirm facts → receive verdict → understand → act/share
```

Required surfaces:

```text
single-document check       extracted-field confirmation
plain-language finding      monetary impact where defensible
why/evidence drill-down     missing-fact request
shareable verdict card/PDF  privacy receipt
pack/version freshness      unsupported-capability matrix
```

Installed professional modes may add:

```text
batch/ZIP/folder intake · pending/verified workspace
filters and exceptions · client workpapers · print/export
pack pinning · reconciliation · backup/restore · duplicate detection
```

Modes arrange features; they are not server-side roles or paid authority.

UI copy never says "compliant", "approved", "guaranteed", or "ready to file"
when the engine has performed only preflight checks.

---

## 26 · ACCESSIBILITY AND LANGUAGE LAW

```text
Meaning(rule) is language-independent
Template(rule, language) is deterministic presentation
```

Every shipped language requires native-speaker review and template completeness.

Features:

```text
screen readers · keyboard navigation · high contrast · scalable text
large touch targets · color-independent outcomes · reduced motion
deterministic voice output · digit-by-digit confirmation for voice input
```

Voice output may use platform TTS. Voice input is always a candidate fact path
through confirmation. A misheard GSTIN digit never reaches validation as a
confirmed fact.

Language rollout follows real user demand and reviewer availability, not the
number of machine translations that can be generated.

---

## 27 · STORAGE, BACKUP, AND PORTABILITY LAW

```text
Local-only without export = user lock-in
```

Installed workspaces store:

```text
source reference · input digest · normalized facts · verdict envelope
pack/engine versions · review state · timestamps · user annotations
```

Backup is a versioned user-carried archive:

```text
manifest · per-file digests · verdicts · optional sources · schema version
```

Restore performs safe parse, digest verification, explicit migration, and
deduplication. It never silently changes historical verdicts.

Optional encryption uses reviewed standard cryptography and clear recovery
warnings. CanonFlow does not invent a cryptographic format or promise recovery
from a forgotten passphrase.

---

## 28 · EXTERNAL-SYSTEM WALL

Offline preflight may prepare and validate payloads, but government actions are
separate state-changing integrations.

```text
validate IRN payload      ✓
verify signed QR          ✓
prepare filing/export     ✓ after schema proof
submit to IRP/GSTN        outside core; separately authorized adapter
pay tax                   never implicit
declare filing success    only from verified acknowledgement
```

An online connector, if built, must be a separate product boundary with
credentials, consent, audit, retry/idempotency, acknowledgement verification,
and applicable authorization. Its absence never blocks offline GSTFlow.

---

## 29 · CUSTODIAN LAW

Custodianship is a system, not a heroic founder.

| Role | Binding responsibility |
|---|---|
| Technical Custodian | engine contracts, architecture, target agreement |
| Statutory Custodian | interpretation records, scope, effective dates |
| Corpus Custodian | case quality, privacy, licensing, regression integrity |
| Security Custodian | threat model, signing, disclosure, revocation |
| Release Custodian | CI evidence, artifacts, provenance, publication |
| Community Custodian | contribution path, translations, reviewer growth |

Minimum production approval:

```text
author ≠ statutory reviewer
statutory approval ∧ technical approval ∧ release approval
```

Required governance files:

```text
GOVERNANCE.md · SECURITY.md · CONTRIBUTING.md · CODE_OF_CONDUCT.md
RULE_PACK_POLICY.md · LEGAL_REVIEW_POLICY.md · RELEASE_POLICY.md
SUPPORTED_CAPABILITIES.md · THREAT_MODEL.md
```

Disagreement is preserved as an issue or competing reviewed interpretation;
it is never resolved by silent majority or AI confidence.

Foundation code is stewarded as auditable public infrastructure under
Apache-2.0. Contributions use a transparent sign-off policy; architectural
authority remains human and documented through ADRs. AI agents receive no
main-branch or release-signing credentials.

---

## 30 · SUPPLY-CHAIN AND RELEASE LAW

Every release starts from a clean clone and produces:

```text
source commit · locked dependencies · compiler/toolchain versions
SBOM · dependency/license report · deterministic build metadata
artifact digests · signatures/attestations · test/proof manifest
changed rules · known limitations · rollback/revocation instructions
```

CI gates:

```text
restore/build/test                  mandatory
format/static checks               mandatory
properties/mutations/corpus        mandatory by activated capability
NativeAOT publish + execution      mandatory for AOT artifact
Avalonia headless                  mandatory for UI release
Fable agreement                    mandatory if web verdict is claimed
malicious-pack/parser suite        mandatory for pack/CFF release
documentation capability diff     mandatory
stranger workflow exercise        mandatory before first public production claim
```

Forbidden:

```text
✗ || true on a required gate
✗ skipped failing target presented as supported
✗ mutable unpinned release input
✗ unsigned artifact presented as signed
✗ release from a dirty or unreproducible workspace
```

---

## 31 · TRUTHFUL STATUS LAW

Every capability has exactly one public status:

| Status | Meaning |
|---|---|
| `PROVEN` | implementation, falsifier, agreement, clean CI, docs, custodian evidence |
| `EXPERIMENTAL` | works in bounded conditions; limitations displayed |
| `STUBBED` | interface exists; production behavior unavailable |
| `DESIGNED` | specification/ADR exists; no implementation claim |
| `DORMANT` | intentionally deferred with a wake condition |
| `REJECTED` | violates a constitutional boundary |

```text
Fixed = Implementation ∧ Falsification ∧ Agreement ∧ CleanCI ∧ TruthfulDocs
```

README, website, UI, release notes, and supported-capability manifest must agree.
CI should fail when a claimed feature lacks its required evidence artifact.

---

## 32 · DEPENDENCY LAW

```text
∀ lib ∈ VerdictPath :
    deterministic(lib) ∧ offline(lib) ∧ decimalSafe(lib)
    ∧ AOTCompatible(lib) ∧ maintained(lib)
```

For a Fable-supported path, add:

```text
FableCompatible(lib) ∧ AgreementProven(lib)
```

Preferred foundations:

```text
F#/.NET 10                  domain and execution
System.Decimal              statutory money
FsCheck                     property testing
existing test framework     examples and integration; avoid aesthetic churn
Avalonia                    installed UI
NativeAOT                   release artifact after proof
Fable                       conditional browser target
DuckDB                      optional analytical accelerator
Avro/Parquet/Arrow          gated interchange/projection formats
```

A dependency must remove more complexity than it adds. Reflection-heavy
serialization, dynamic code generation, and opaque native dependencies require
specific AOT and crash evidence. Explicit wire DTOs/codecs are preferred at
trusted boundaries.

Build-time, diffable generated artifacts are preferred over type providers,
runtime code generation, or hidden compiler magic. Generation output is checked
into review or reproduced in CI with a semantic diff and fidelity report.

---

## 33 · REPOSITORY SHAPE

```text
/src
  CanonFlow.Core
  CanonFlow.Packs
  CanonFlow.CFF
  CanonFlow.Crucible
  CanonFlow.Studio
  GSTFlow.Core
  GSTFlow.Intake
  GSTFlow.Rules
  GSTFlow.Reconcile
  GSTFlow.Cli
  GSTFlow.UI

/tests
  Unit · Boundaries · Properties · Mutations · GoldCorpus
  Agreement · Security · AotSmoke · AvaloniaHeadless

/governance
  interpretations · approvals · revocations · trust-snapshots

/corpus
  public-synthetic · restricted-manifest · regressions

/schemas
  canonical-facts · verdict-envelope · cff · rule-pack · proof-manifest
```

Physical repositories may remain separate. The ownership and dependency
direction above remain binding.

---

## 34 · FIDELITY, DRIFT, PROFILE, AND CONFORMANCE LAW

### 34.1 · Classified fidelity

Every transformation reports both value and meaning preservation:

```fsharp
type RepresentationFidelity =
    | Lossless
    | Widened of reason: string
    | Narrowed of reason: string
    | Unrepresentable of reason: string

type SemanticFidelity =
    | Exact
    | Approximate of reason: string
    | Unsupported of reason: string
    | Unknown
```

Applications include:

```text
external schema → CanonicalFacts
CanonicalFacts  → JSON/Avro/Parquet/DuckDB
Rule IR         → F#/.NET/Fable host
source rule     → UI explanation/OpenAPI/agent context
```

No emitter may silently discard parameters, effective dates, evidence,
precision, scale, uncertainty, or legal references.

### 34.2 · Semantic drift

```fsharp
type DriftStatus =
    | Aligned
    | StrictTarget
    | LooseTarget
    | Disjoint
    | ComplexOrUnknown
```

```text
Aligned          = same admitted/rejected meaning
StrictTarget     = target rejects values the source admits
LooseTarget      = target admits values the source rejects
Disjoint         = source and target are mutually incompatible
ComplexOrUnknown = implication cannot be proved by the available model
```

Drift is computed across:

```text
law source ↔ interpretation ↔ Rule IR ↔ runtime
runtime ↔ Fable host ↔ NativeAOT host
verdict schema ↔ CFF/analytics projection
capability manifest ↔ README ↔ website ↔ UI
```

High-risk loose, disjoint, or unknown drift blocks release. Approximation is a
first-class result with a reason and remediation—not a warning hidden in logs.

▲ **Proof backend.** For constraint pairs in the decidable fragment, the
drift implication is discharged directly by the solver: source admits x ∧
target rejects x ⇒ `StrictTarget`, etc. `ComplexOrUnknown` is thereby
**reserved** for genuinely undecidable or timed-out pairs — its
honest-fallback meaning is preserved and its population shrinks. Solver
timeout ⇒ `ComplexOrUnknown`, recorded; never a hang, never a silent
`Aligned`.

### 34.3 · Profile bundle

Every governed rule family is distributed and reviewed as a profile:

```text
Profile = source extracts
        ⊕ interpretation record
        ⊕ canonical rule/parameter content
        ⊕ passing fixtures
        ⊕ failing fixtures
        ⊕ boundary/property/mutation set
        ⊕ proof manifest
        ⊕ approvals
```

This absorbs EDIFlow's useful `schema/profile/proof/fixtures` separation and
generalizes it to GST. A profile states exactly which contract layer it covers
and which transport, bilateral, factual, or professional questions remain out
of scope.

▲ **INVARIANT-BINDING LAW.** Every invariant stated in a profile's
interpretation record, a canonical type's documentation, or a pack's review
notes **names its discharging checker** (a §14.4 discharge class +
identifier). An invariant with no named discharge is a blocking finding at
profile approval and a Contract gap under §37. Documentation thereby becomes
the table of contents of the evidence bundle rather than testimony beside it.

### 34.4 · Conformance kit

Extension points ship with executable conformance suites:

```text
Intake adapter      ⇒ raw preservation + normalization + round-trip laws
Storage projection ⇒ decimal/DU/evidence fidelity laws
Verdict host        ⇒ canonical agreement + crash boundary
Rule-pack authoring ⇒ schema + bounds + proof + review laws
Registry mirror     ⇒ digest/signature/revocation consistency
OpenMetadata output ⇒ pinned official schema/API + extension-field validation
OKF output          ⇒ pinned OKF spec + frontmatter/type/link conformance
AgentContext output ⇒ least-authority fields + constraint/fidelity preservation
```

A new driver or host is accepted by passing the suite, not by inheriting an
interface or copying a sample. Conformance fixtures are versioned and reusable
by third-party contributors without access to private data.

### 34.5 · Soundness and optimizer laws

```text
evaluate(simplify(x)) = evaluate(x)
simplify(simplify(x)) = simplify(x)
roundTrip(x)          ≅ x with classified fidelity
target admits unsafe value ⇒ LooseTarget
target rejects valid value ⇒ StrictTarget
```

Optimizer, drift, and fidelity functions receive hostile deeply nested
property tests and mutation tests. Structural equality is never claimed to be
semantic equality without canonicalization or a bounded equivalence proof.

---

## 35 · COMPLETE FEATURE LEDGER

This ledger prevents older proposals from becoming invisible. Inclusion here
means "governed by this constitution," not "implemented today." Current status
is always determined by §31 and the release capability manifest.

### 35.1 · Foundational engine

```text
F# domain model · explicit outcome precedence · System.Decimal money
GSTIN/document primitives · canonical facts · evidence paths
verdict envelope · deterministic templates · replay · provenance
effective dates · unsupported/unknown states · exact canonical digest
```

### 35.2 · Individual utility

```text
paste/import document · camera/PDF intake · confirmation screen
single-screen verdict · plain-language correction · defensible ₹ impact
why/legal-reference view · offline/privacy receipt · share image/PDF
tap-to-call supplier · print · HSN/rate lookup · reverse-GST calculator
```

The reverse-GST calculator is a fenced utility, never part of the verdict
unless an activated reviewed rule explicitly uses its facts.

### 35.3 · Professional utility

```text
batch/folder/ZIP intake · pending/verified/needs-fix workspace
exception filters · bulk workpapers · client export · pack pinning
duplicate/double-billing detection · annotations · reconciliation
local DuckDB analytical projection · deterministic query console
CLI/TUI presentation · backup/restore · cross-device file handoff
```

Natural-language-to-query assistance may draft allowlisted read-only queries;
it is labeled AI/demonstration until a real local model, grammar enforcement,
result validation, and injection tests exist. Query results never change the
canonical verdict.

### 35.4 · Sovereign offline features

```text
IRP signed-QR verification · signed rule packs · signed trust snapshots
staleness disclosure · HSN/SAC directory · user-carried CFF bundles
offline key rotation imports · optional encrypted backup
```

### 35.5 · Studio and professional knowledge

```text
visual rule graph · parameter tables · effective-date timeline
source/interpretation records · case authoring · AI boundary assistant
Crucible runner · pack diff/impact · approval workflow · DSC signing
supersession/revocation · proof viewer · unsigned browser drafting
```

### 35.6 · Semantic contracts and publication

```text
canonical semantic model · AgentContext projection
OpenMetadata catalog/lineage projection · OKF knowledge-bundle projection
pinned target versions · schema/spec validators · fidelity reports
projection fixtures · unsupported-field inventory · semantic diff
```

OpenMetadata and OKF are contract surfaces, not new verdict engines. Their
status remains `EXPERIMENTAL` until their emitted artifacts pass the pinned
external conformance suites and CanonFlow's field-level fidelity checks.

### 35.7 · Assurance and operations

```text
clean-clone CI · example/boundary/property/metamorphic/mutation tests
Gold Corpus · cross-host agreement · NativeAOT execution smoke
Avalonia headless tests · parser/pack fuzzing · malicious container suite
SBOM/license report · reproducible manifest · signatures/attestations
security disclosure · rollback/revocation drill · capability/status check
```

### 35.8 · Channels

```text
NativeAOT CLI reference · Avalonia desktop workhorse
Avalonia mobile only after demand/proof · conditional Fable web inspector
stateless web check-and-go · installed local workspace
```

Flutter, Fable-Dart, Tauri, Electron, and a native verdict ABI are outside the
current GSTFlow platform decision. Reintroducing one requires an explicit ADR
showing a capability unavailable through Avalonia/.NET and proving that no
second verdict implementation is created.

### 35.9 · Accessibility, language, and community

```text
screen reader · keyboard · contrast · scalable text · large targets
reviewed language template packs · deterministic voice output
confirmed voice input · multilingual share artifacts
SEVA/community check-desk playbook · translation contribution lane
```

No account, directory, rating, or server marketplace is implied by the
community playbook.

### 35.10 · Registry and ecosystem

```text
pack discovery · signer/reviewer identity · trust level · compatibility
supersession · revocation · mirrors · offline snapshot · dispute record
competing reviewed interpretations · adoption/contact evidence
```

The term "marketplace" wakes only after governance, liability, revocation, and
dispute handling work. Before then it is a signed registry.

### 35.11 · Dormant features

```text
tamper-evident verdict hash chain · full mobile camera workflow
large on-device LLM · conversational explanation · voice NLU
Avro/Parquet/Arrow CFF payloads · multi-million-row performance claims
portal connectors · advanced refund/ISD/special-valuation families
developer-only Dhall configuration generator
▲ the Forge (AI candidate-generation line: LoRA model, harness, GRPO)
▲ CDC engine (Canon Dynamic Crucible executable law layer)
▲ Class D proof battery and drift proof backend (LIQUID annex)
```

Each dormant item has a wake condition: proven user demand, a named custodian,
a threat model, and an exit test — ▲ recorded in the item's own governed
document and registered in §46.8. Dormant is not promised.

---

## 36 · EXECUTION PROGRAM

The sequence is gate-driven. Time estimates never override exit evidence.

### Gate 0 · Truth reset

```text
freeze surface expansion
restore clean-clone CI
remove stale projects/workflows and generated debris
classify every claim using §31
publish supported/unsupported matrix
name provisional custodians
```

**Exit:** a stranger can build the repository and understand what exists.

### Gate 1 · Trusted kernel

```text
explicit outcome precedence
reviewed money/rounding policy
canonical verdict envelope
typed uncertainty/evidence
no AI/native/UI logic in verdict path
JIT ↔ NativeAOT corpus agreement
```

**Exit:** the smallest kernel is deterministic, replayable, and green.

### Gate 2 · Assurance skeleton

```text
case and interpretation schemas
Crucible CLI
boundary/property/mutation suites
proof manifest
AI candidate-case workflow
fidelity model + semantic drift report
adapter/host conformance-kit skeleton
canonical semantic publication model
AgentContext/OpenMetadata/OKF projection contracts and pinned target versions
```

**Exit:** every engine change produces inspectable evidence.

### Gate 3 · One statutory vertical

Choose one narrow, high-value document journey.

```text
official/authoritative sources
CA author + independent reviewer
decision table + boundary family
Gold Corpus + mutations
sanitized real workflow
```

**Exit:** `Contact ≥ 1` and the professional reviewer will sign the record.

### Gate 4 · Canonical intake and Avalonia utility

```text
one official JSON adapter + one common ledger adapter
raw/parsed/normalized evidence
classified round-trip fidelity
confirmation workflow
single + batch preflight
plain verdict + provenance + export
Avalonia headless and AOT artifact tests
```

**Exit:** a user completes the North Star flow without developer help.

### Gate 5 · CFF v1

```text
canonical JSON evidence bundle
safe archive parser
digest/signature terminology
round-trip/replay/import/export
projection fidelity report
malicious-container suite
```

**Exit:** another clean machine reproduces the verdict from the bundle.

### Gate 6 · Class P rule packs

```text
pack schema · signing · trust store · expiry/staleness
supersession · revocation snapshot · rollback
HSN/rate/date/key/template parameters
▲ VERIFY-BOUND / VERIFY-INTERVAL over Class P tables — rate-map range
  coverage and effective-interval disjointness are trivially decidable,
  need no Class D interpreter, and deliver the first live proof value.
```

**Exit:** a parameter change ships without engine rebuild and replays exactly.

### Gate 7 · Crucible-governed Studio

```text
parameter editor first
interpretation/case/review workflow
canonical normalization + contradiction diagnostics
pack diff and impact report
DSC/provider integration proof
Class D interpreter threat model and prototype
standards-valid OpenMetadata and OKF projections with fidelity fixtures
▲ full Class D proof battery (TOTAL · DET · UNKNOWN · SEV · BOUND ·
  INTERVAL · META · EQUIV), wired into witness-backed Studio diagnostics
  (§12) and crucible prove-graph
▲ encoder differential agreement suite green
▲ the classification benchmark passed — a corpus of known-proven and
  known-refuted graphs classified with ZERO FALSE PROVEN. False Unproven
  is tunable friction; false Proven is disqualifying.
```

**Exit:** a non-programmer authors a constrained candidate; independent
reviewers approve it; Studio cannot bypass policy.

### Gate 8 · Authenticity and reconciliation

```text
real IRP QR known-answer vectors and key lifecycle
purchase register ↔ GSTR-2B first
immutable match graph and drill-down evidence
performance corpus and duplicate detection
```

**Exit:** pilot users confirm that exceptions are correct and actionable.

### Gate 9 · Broader rule families

Activate one family at a time from §8:

```text
POS expansion → RCM → rate/exemption → ITC screening
→ return reconciliation → exports/SEZ → advanced families
```

Each repeats Gate 3. No family inherits another family's legal approval.

### Gate 10 · Web/mobile and registry

```text
Fable agreement before web verdict
mobile demand + camera/AOT/lifecycle proof
registry governance before marketplace language
offline trust snapshots and revocation drills
```

**Exit:** every activated channel passes the same canonical corpus and its own
platform threat model.

---

## 37 · RELEASE DEFINITION OF DONE

A feature is complete only when:

```text
Contract
∧ Implementation
∧ Red-run falsifier
∧ Boundary family
∧ Failure diagnostics
∧ Cross-host agreement where applicable
∧ Security/adversarial evidence where applicable
∧ Custodian approval
∧ Clean-clone CI
∧ Truthful documentation
∧ Rollback/revocation plan where applicable
∧ External contact for production claim
```

"Code merged" is not a completion state.

---

## 38 · LANDMINE REGISTER

| Landmine | Constitutional countermeasure |
|---|---|
| Scope expansion outruns trust | gated families and wake conditions |
| Law forced into binary answers | Unknown/NeedsEvidence/ProfessionalReview |
| Tests mistaken for legal proof | oracle hierarchy and independent review |
| Rule pack becomes malware | P/D/E classes, bounded interpreter, no arbitrary code |
| Decimal drift across hosts | managed reference and agreement corpus |
| FFI/native crash | peripheral isolation and soak/fault testing |
| A total language is mistaken for a resource-bounded rule VM | explicit depth/node/time/memory bounds; Dhall excluded from verdict execution |
| Historical verdict changes | pinned replay and immutable packs |
| AI poisons oracle | CandidateCase workflow and human derivation |
| Marketplace signs bad law | governance, review identity, revocation, disputes |
| OCR invents facts | confirmation and raw evidence preservation |
| README becomes fiction | status law and evidence-linked claims |
| Founder becomes bottleneck | separated custodian roles and reviewer growth |
| Offline becomes stale | explicit freshness, signed human-carried updates |
| Privacy becomes lock-in | portable CFF/backup and explicit retention |
| Platform count fragments engine | one-engine law and channel activation gates |
| Generator silently drops meaning | classified fidelity and semantic drift gate |
| "Format-inspired" output is advertised as standard-compliant | pin the external version and require official schema/spec conformance |
| New driver copies old assumptions | executable conformance kit |
| Snapshot becomes self-fulfilling | missing/changed snapshots require explicit review |
| Agent sees approximate contract as truth | emitted agent context carries fidelity gaps |
| ▲ Encoder unsoundness produces a false `Proven` | classified encoding fidelity; differential agreement between solver model and real interpreter; encoder mutation suite; a wrong Proven ranks with §46.5 nondeterminism incidents |
| ▲ Solver version/config drift changes verdicts of record | solver + encoder pinned by version/checksum in the toolchain manifest; outcomes recorded with toolchain digest |
| ▲ `Proven` label read as legal truth | §3 amendment; UI/docs say "invariant proven"; the proposition's adequacy stays human |
| ▲ Solver or model checker becomes a runtime dependency | §4/§32; CI asserts shipped artifacts have no solver linkage; proofs live in Studio/Crucible only |
| ▲ A vacuous box passes as safety | §14.5 executability obligations and vacuity detector |
| ▲ lat and OKF blur into one knowledge plane | §15.4 demarcation; capability/drift gates watch both |
| ▲ lat prose read as permission | §15.5: authority lives only in the packet projection |
| ▲ Name collision breeds vocabulary drift | §0.1 reservation law + CI grep |
| ▲ Retro learnings drift into model memory | Gate L facts land as candidate lat nodes under review; free-form model memory stays rejected |
| ▲ Retry loops on permanent failures | §46.2 + the broken-TV theorem: `b → [k]b ⊢ b → [k*]b` — the failure CLASSIFIER, not the retry budget, carries the correctness burden |

---

## 39 · SUCCESS LAW

Vanity metrics are subordinate:

```text
downloads, stars, LOC, test count, speed benchmark < trusted contact
```

Primary measures:

```text
confirmed defects caught before action
false-verdict rate by governed family
Unknown resolved through requested facts
corpus mutations killed
cross-host agreement
historical replay success
time from reviewed parameter change to signed pack
external CA/business contacts
documented corrections and rollback time
▲ verification friction per statutory seam (repair turns + review findings
  + change requests + assumption-ledger growth) — rising friction on one
  seam signals a primitive or spec defect, not a need for more effort;
  friction reports feed operator and combinator evolution
```

The binding constraint remains:

```text
Contact ≥ 1 per activated statutory family
```

A mathematically elegant architecture with no independent user or reviewer is a
sealed room.

---

## 40 · IMMEDIATE ORDER

Before adding another major surface:

```text
1. Name provisional custodians.
2. Make clean-clone CI green.
3. Correct claims and publish the capability matrix.
4. Freeze the canonical verdict envelope.
5. Resolve outcome precedence and money policy with proofs.
6. Create ten independently derived Gold cases.
7. Expand them into boundaries, properties, and mutations.
8. Run them through JIT and the published NativeAOT binary.
9. Produce the first Crucible proof manifest.
10. Put one bounded workflow before one external CA and one real user.
```

Studio implementation begins only after its Rule IR, approval model, pack
classes, and Crucible evidence contract are frozen.

---

## 41 · CONSERVATION LAWS

```text
H = Duplication + Exceptions + SpecialCases + UnsupportedClaims

∀ PR : ΔH ≤ 0
```

```text
Min(Code) · Min(Coupling) · Min(PlatformsBeforeProof)
Max(Determinism) · Max(Evidence) · Max(Replay) · Max(Contact)
```

Weekly questions:

```text
What can be deleted?
What claim lacks evidence?
What uncertainty is being hidden?
What can crash outside the type system?
Who besides the author has challenged this?
```

---

## 42 · FINAL FORM

```text
CanonFlow = Reasoning + Evidence Infrastructure
GSTFlow   = CanonFlow + GST Knowledge + GST Context
Studio    = Governed Authoring
Crucible  = Executable Falsification
CFF       = Portable Reproducibility
Registry  = Governed Distribution
AI        = Candidate Generator, never Oracle
Custodian = Accountable Continuity
```

Therefore:

```text
Trusted GSTFlow
  = Exact Kernel
  ⊕ Canonical Facts
  ⊕ Reviewed Rules
  ⊕ Adversarial Tests
  ⊕ Signed Evidence
  ⊕ Honest Uncertainty
  ⊕ Independent Contact
```

> **The moat is not F#, Avalonia, Fable, NativeAOT, AI, DuckDB, or Avro.
> The moat is the accountable chain from authoritative source to reproducible
> verdict—and the discipline to stop whenever one link is missing.**

---

## 43 · DEPENDENCY, VERSION, GIT, AND RELEASE TRAIN LAW

Dependency resolution, Git history, package publication, channel promotion,
CFF compatibility, and human approval form one delivery protocol. None may be
operated as an independent convenience.

### 43.1 · Immutable release set

```text
ReleaseSet
  = source commit
  ⊕ toolchain manifest
  ⊕ resolved dependency graph
  ⊕ canonical schema set
  ⊕ CFF compatibility declaration
  ⊕ engine and rule-pack set
  ⊕ capability manifest
  ⊕ evidence/proof manifests
  ⊕ produced artifact digests
  ⊕ channel and approvals
```

Every candidate receives a machine-readable release-set manifest containing at
least:

```text
release_set_id · channel · source_sha · source_tree_digest
dotnet_sdk · workload/tool versions · package-lock digest
CanonFlow/GSTFlow package versions · CFF format/schema digest
Rule IR schema · pack IDs/versions/digests · trust-snapshot digest
supported hosts/RIDs · capabilities · test/proof-manifest digests
SBOM/provenance/signature references · created_at · approvals
```

▲ Where the corresponding layers are awake, the toolchain manifest and
release evidence additionally include: SMT solver version + checksum ·
Graph→SMT encoder version · CDC engine version · proof-outcome summaries and
certificate digests. The verdict cache key for any proof-serving service is
`Hash(Source ⊕ Toolchain ⊕ Policy ⊕ Tests)` — source-only caching silently
serves stale verdicts across SDK, policy, or eval-set bumps.

```text
channel pointer may move
artifact/version/tag/digest/signature may not move
```

`latest`, `main`, a floating version, or a mutable container tag is never an
identity inside CFF, a proof manifest, a released application, or a signed rule
pack.

The independently versioned surfaces are:

| Surface | Version meaning | Compatibility authority |
|---|---|---|
| Product/application | user-visible feature and behavior release | capability manifest and release evidence |
| NuGet/library package | public API and package dependency contract | package API tests and downstream candidate graph |
| CFF format | portable container and canonical payload contract | CFF conformance/replay suite |
| Rule IR/pack schema | operators, facts, serialization, and interpreter contract | Class P/D conformance suite |
| Rule-pack content | reviewed law/parameter interpretation for an effective period | proof manifest and statutory approvals |
| Trust snapshot | allowed signers, revocations, and freshness | trust policy and signed snapshot |

These versions may be released together but are never assumed equal.

### 43.2 · Dependency pinning law

Protected branches and all published channels use deterministic inputs:

```text
exact .NET SDK/workload manifest
one root Directory.Packages.props per repository
explicit direct package versions
packages.lock.json for shipped apps, CLIs, tools, and test executables
locked-mode restore in CI and release builds
package-source mapping and an allowlisted feed set
GitHub Actions pinned to full commit SHAs
containers pinned by digest
npm/pnpm lock when a Fable/web surface is active
Dhall/compiler/tool binaries pinned by version and checksum when used
```

Publishable libraries declare intentional dependency ranges for consumers, but
their build proof records the exact graph used to compile and test them. A
library's lock file does not falsely claim control over the graph selected by a
downstream application.

Forbidden on protected branches and release builds:

```text
floating NuGet versions · unbounded ranges · mutable GitHub Action tags
unmapped package feeds · silent transitive pinning · per-project VersionOverride
restore warnings ignored · lock regeneration during ordinary CI
```

`CentralPackageVersionOverrideEnabled=false` is the default. Transitive
pinning is off unless an ADR explains why the promoted transitive dependency
must become part of the published package contract. Restore downgrade,
approximate-version, source-mapping, and framework-fallback warnings are
release failures unless a time-bounded reviewed exception names the exact
package and reason.

Floating resolution is allowed only inside an ephemeral dependency-discovery
job. The job proposes an exact candidate graph and lock diff; no floating
reference is committed or published.

### 43.3 · Resolver before Git integration

NuGet solves package version constraints; it does not solve CanonFlow product
compatibility. CanonFlow adds a policy layer:

```text
Valid(candidate)
  = NuGetGraphResolves
  ∧ ToolchainCompatible
  ∧ TargetFrameworkCompatible
  ∧ LicenseAllowed
  ∧ VulnerabilityPolicySatisfied
  ∧ NativeAotCompatible
  ∧ CanonicalAgreementPreserved
  ∧ CffReadWriteCompatible
  ∧ RulePackCompatible
  ∧ PublicApiPolicySatisfied
  ∧ CrucibleAndHostSuitesPass
```

The resolver's objective is lexicographic:

```text
1. reject every invalid graph
2. satisfy a critical security correction
3. retain the current locked graph where valid
4. prefer stable over prerelease dependencies
5. minimize the number of changed packages
6. minimize SemVer distance and transitive churn
7. prefer the smallest version satisfying the declared constraint
```

The correct order is:

```text
policy constraints
→ candidate version discovery
→ full transitive graph resolution
→ exact lock and graph diff
→ isolated Git dependency PR
→ producer build and candidate package
→ exact downstream candidate consumption
→ cross-repository Crucible/host/CFF checks
→ human review
→ squash merge
→ immutable channel snapshot
→ gated promotion
```

Git records the reviewed decision. Git branching must never be used as a
substitute for dependency solving.

Three propagation lanes keep updates ordered:

```text
Lane A — contracts: CFF/schema/Rule IR/core compatibility
Lane B — domain: GST facts, rules, packs, intake, reconciliation
Lane C — delivery: CLI, Avalonia, web inspector, registry, docs/site

A → B → C
```

Readers and contracts move before writers and applications. A new writer is
never promoted while the supported stable reader cannot safely interpret its
output.

### 43.4 · One-package update protocol

One direct dependency is updated per PR unless the packages form an inseparable
vendor/toolchain unit such as SDK + workload or a documented bill of materials.

```text
1. Create dep/<package>-<target-version> from current main.
2. Discover candidate versions in an isolated resolver job.
3. Select one exact version under §43.3 policy.
4. Regenerate locks intentionally; ordinary CI remains locked.
5. Diff direct and transitive graphs, assets, licenses, vulnerabilities,
   target frameworks, native assets, trimming/AOT warnings, and package size.
6. Add a red regression or compatibility test for the upgrade's claimed need.
7. Build the producer and publish an immutable CI candidate such as
   X.Y.Z-ci.YYYYMMDD.RUN.gSHA to a quarantined feed.
8. Open exact-version downstream PRs or worktrees against that candidate.
9. Execute CanonFlow → GSTFlow/EDIFlow → hosts in topological order.
10. Attach graph diff, proof manifests, artifact digests, risk class,
    rollback version, and downstream results to the PR.
11. Obtain the human approvals required by §43.10.
12. Squash merge; main rebuilds from locks and produces the nightly snapshot.
```

An internal package upgrade that changes CFF bytes, canonical digests, Rule IR,
verdict envelopes, decimal behavior, evidence paths, trimming behavior, or
public APIs is not a routine dependency update. It is classified as a contract
or engine change and receives the corresponding tests and reviewers.

Release branches accept no dependency refresh after RC cut except a critical
security fix or release blocker. A major dependency upgrade belongs on `main`,
not in a patch or hotfix branch.

### 43.5 · Day 1 / Day 2 end-to-end loop

The two-day loop integrates a change into a trustworthy nightly. It does not
promise a stable release every two days.

#### Day 1 — falsify and construct

| Time | Human/automation work | Evidence produced |
|---|---|---|
| Intake | issue, affected capability, risk class, custodian, rollback and channel target | change record |
| Design | contract/ADR when semantics or compatibility change | approved decision boundary |
| Red | failing example/property/mutation/security or compatibility test | witnessed red-run |
| Branch | short `feat/`, `fix/`, or `dep/` branch from current `main` | traceable source base |
| Implement | smallest change; no unrelated package refresh | focused diff |
| Resolve | intentional candidate solve and lock regeneration when dependencies change | direct/transitive graph diff |
| Local proof | format, analyzers, unit/property/mutation tests, F# build | developer evidence |
| PR proof | clean restore, build, Crucible, parser/fuzz/security, JIT/AOT and affected host checks | candidate proof manifest |
| Downstream | publish quarantined CI package and test exact consumers | compatibility report |

AI and bots may draft the issue, propose tests, minimize failures, update the
dependency branch, and prepare evidence. They cannot approve a Gold result,
merge, sign, promote, or waive a gate.

#### Day 2 — review, integrate, and observe

| Time | Human/automation work | Gate |
|---|---|---|
| Refresh | update branch against current `main`; rerun if base/lock changed | no stale proof |
| Review | domain, technical, security, schema, or release review by risk | required approvals complete |
| Merge | squash normal PR into protected `main` | one auditable logical commit |
| Rebuild | clean environment, locked restore, full affected graph | merge-result proof |
| Snapshot | packages, applications, CFF fixtures, SBOM, attestations and capability manifest | immutable nightly release set |
| Smoke | install/launch/verify/export/import/replay on declared hosts | deployment evidence |
| Observe | nightly soak, crash/failure telemetry without taxpayer data, user/internal trial | no unresolved blocker |
| Decide | retain nightly, promote commit to preview candidate, or revert/supersede | recorded disposition |

If the change misses Day 2 gates, it waits. No reviewer or release operator is
penalized for stopping the train.

### 43.6 · Git and branch strategy

`main` is protected, releasable, and the sole source of ordinary nightlies.
Feature branches are short-lived. A `release/X.Y` branch is created only when
the first RC freezes scope.

```text
main
├─ feat/<issue>-<slug>
├─ fix/<issue>-<slug>
├─ dep/<package>-<version>
├─ release/X.Y          only during RC and supported maintenance
└─ hotfix/X.Y.Z         from the affected immutable stable tag
```

Exactly three integration operations are recognized:

| Operation | Use | Rule |
|---|---|---|
| Squash | normal feature, fix, dependency, documentation PR into `main` | one issue/decision/evidence unit becomes one commit |
| Fast-forward | promote an already reviewed identical commit through an internal candidate/promotion pointer | no new source or lock diff; never move a published tag |
| Carry | propagate a fix across supported release lines | cherry-pick with origin (`-x` equivalent), linked issue, conflict review, and full target-branch tests |

Ordinary corrections land on `main` and are carried back only when a supported
stable line needs them. An emergency production fix begins from the oldest
affected supported stable tag and is carried forward through newer release
lines to `main`. It is never reimplemented independently on each branch.

Merge commits are not used for routine PRs. Force-push is forbidden on
protected branches and release tags. Published tags are signed and immutable.

▲ Every commit on a work-packet branch carries `Work-Id` and
`Contract-Digest` trailers; CI rejects contradictions (§46.8).

### 43.7 · Channel and snapshot strategy

A snapshot is an immutable release set addressed by digest. A channel is a
policy-controlled pointer to a snapshot.

| Channel | Source | Version example | Automation/human gate | Consumer promise |
|---|---|---|---|---|
| `nightly` | every green `main` merge or daily tip | `X.Y.Z-nightly.20260714.153.gSHA` | automatic after full required CI | diagnostic; may break within declared future scope; short retention |
| `preview` | chosen green `main` snapshot | `X.Y.Z-preview.N` | release custodian + affected technical/domain custodian | public evaluation; migration notes required; opt-in feed |
| `rc` | protected `release/X.Y` | `X.Y.Z-rc.N` | full release evidence + security/domain sign-off; blockers only | stable candidate; compatibility frozen except approved blocker |
| `stable` | quarantined final candidate from approved RC commit | `X.Y.Z` and immutable `vX.Y.Z` | release ceremony and production signing | supported, replayable, rollback-ready |
| `hotfix` | affected stable tag | `X.Y.(Z+1)` | accelerated but unchanged critical gates | narrow correction; forward-carried |

Suggested cadence:

```text
nightly = every green merge or once per day
preview = weekly or milestone-driven
RC      = when scope and compatibility freeze; minimum soak defined by risk
stable  = evidence-driven, never calendar-forced
hotfix  = incident-driven
```

Nightly retention may be 30 days and preview/RC retention 90 days, but their
release manifests and proof summaries remain. Stable artifacts, tags, CFF
fixtures, SBOMs, signatures, revocation records, and proof manifests are
retained according to the legal/replay policy.

An `rc.N` NuGet package is never renamed into a stable package. At final
approval, the exact approved RC commit and locked release set produce the final
`X.Y.Z` packages in a quarantined stable feed. The complete final-version bytes
are tested there and then promoted unchanged to the public stable feed. Public
RC artifacts remain immutable prereleases.

Channel isolation:

```text
nightly feed  ≠ preview feed ≠ stable feed
nightly trust ≠ preview trust ≠ production trust
nightly data/profile directories ≠ stable user directories
```

Stable applications never resolve prerelease packages. Preview/nightly hosts
display their channel prominently and cannot silently mutate a stable user's
workspace or trust store.

### 43.8 · CFF level and channel crossing

Every CFF manifest carries:

```text
cff_format_version · schema_digest · required_capabilities
producer_product/version/channel/source_sha
engine_version/digest · rule_pack_id/version/digest
trust_snapshot_digest · canonicalization_version
▲ canonicalizationProfile and executionProfile names (explicit)
```

Import crosses four independent levels:

```text
L0 Container = safe archive limits, paths, sizes, hashes
L1 Structure = known format/schema and valid canonical encodings
L2 Semantics = required capabilities, decimal/evidence/rule fidelity
L3 Trust     = signatures, signer policy, revocation, freshness, channel policy

Accept = L0 ∧ L1 ∧ L2 ∧ L3
```

Channel crossing matrix:

| Producer CFF | Consumer | Default | Required behavior |
|---|---|---|---|
| stable | same/newer compatible stable | allow | ordinary four-level validation |
| stable | preview/nightly | allow | preserve original bytes and provenance |
| preview | same/newer preview/nightly | allow conditionally | opt-in channel and complete fidelity report |
| nightly | nightly | allow conditionally | exact snapshot available; never production-trusted |
| preview/nightly | stable | deny | explicit quarantine inspector only; no stable import or overwrite |
| RC | stable | deny as ordinary input | only approved release promotion may cross trust channel |
| newer CFF major | older reader | deny | explicit converter or newer reader required |

Within one CFF major version, a reader may accept a newer minor only when every
required capability is understood and unknown optional material is preserved.
Major compatibility is never inferred from successful ZIP/JSON parsing.

Downgrade/export creates a new derived CFF with new provenance, digest, fidelity
report, and signature. It never rewrites the original bundle or pretends to be
lossless. Channel labels do not establish statutory truth; pack review,
signatures, evidence, and replay still govern the verdict.

### 43.9 · Package and deployment promotion order

Stable publication uses staging/quarantine and promotes a complete release set,
not whichever project finishes first:

```text
1. format/schema fixtures and compatible readers
2. CanonFlow core and contract packages
3. CFF, Crucible, emitters, and conformance tools
4. GSTFlow core/rules/intake/reconciliation packages
5. signed GST rule packs and trust snapshot
6. CLI and Avalonia applications; conditional web/mobile artifacts
7. registry channel pointer and update metadata
8. capability matrix, documentation, site, and release notes
```

All packages are built before public stable publication. Downstream packages
consume exact quarantined upstream versions. Only after the whole set passes is
the stable feed/release pointer made visible. Partial stable publication is an
incident.

For a reader/writer change:

```text
expand reader support
→ deploy/test readers
→ enable new writer behind explicit capability
→ observe
→ retire old writing only after support policy permits
```

Database changes follow expand/migrate/contract. CFF files are immutable and
use explicit conversion rather than in-place migration.

Required deployment smoke tests:

```text
install from declared channel
launch without network
verify known invoice and known failure
export CFF
import/replay CFF on a clean second machine
verify signatures and capability manifest
uninstall/upgrade/rollback without data loss
```

### 43.10 · Human involvement and separation of duties

| Risk | Examples | Minimum human involvement |
|---|---|---|
| R0 | prose, comments, non-executable docs | one maintainer; automated evidence |
| R1 | dev tooling or patch dependency outside trust path | component custodian; graph/test review |
| R2 | parser, storage, UI/runtime, NativeAOT, intake adapter | component custodian + independent technical reviewer |
| R3 | kernel, decimal, canonicalization, CFF/schema, crypto, trust, Class D interpreter | core/schema custodian + security reviewer + release custodian |
| R4 | statutory semantics, effective dates, Gold expectations, rule-pack approval | statutory author + independent legal reviewer + technical custodian + release custodian |

Channel authority:

```text
nightly promotion = automation after required CI
preview promotion = release custodian + affected component/domain custodian
RC creation       = release + core/schema + affected statutory/security roles
stable promotion  = recorded release ceremony; no self-approval by author
revocation        = authorized trust/release custodians with incident record
```

No person may be the sole author, sole semantic reviewer, sole release approver,
and sole signer for an R3/R4 change. Small-project reality may require one
person to hold multiple operational roles temporarily, but independent review
of statutory meaning and release evidence remains mandatory before a
production claim.

Bots and AI may:

```text
discover versions · solve candidate graphs · open/update PRs
generate lock/SBOM/API diffs · run tests · draft release notes
detect channel/CFF incompatibility · propose rollback
```

They may not:

```text
approve exceptions · suppress warnings · change Gold truth
merge protected branches · create production signatures
promote stable · revoke trust · decide legal meaning
```

### 43.11 · Failure, rollback, and recovery

Rollback never mutates history:

```text
bad commit       → revert/successor PR
bad package      → deprecate/yank where supported + publish successor
bad app          → point update channel to prior immutable stable snapshot
bad rule pack    → signed revocation/supersession + replay guidance
bad CFF writer   → disable capability + preserve originals + converter
compromised key  → revoke + rotate + signed trust-snapshot update
```

Moving an update-channel pointer back is allowed; moving the associated Git
tag, package version, release asset, CFF digest, or signature is forbidden.

Every stable train performs before publication:

```text
rollback rehearsal · previous-version upgrade test · previous-snapshot restore
CFF backward-read and replay · trust/revocation drill · clean-machine install
```

### 43.12 · Minimum automation backlog

Implement in this order:

```text
1. root dependency/version policy and source mapping
2. locked restore and graph-diff CI
3. protected main + CODEOWNERS + three Git-operation policy
4. release-set manifest and immutable candidate naming
5. quarantined CI/preview/stable feeds
6. cross-repository candidate-package workflow
7. nightly snapshot with SBOM/provenance/proof links
8. preview/RC/stable approval environments
9. CFF four-level crossing tests and channel isolation
10. atomic release-set promotion and rollback drill
```

Suggested automation commands are conceptual contracts, regardless of their
eventual implementation language:

```text
canon deps discover       # propose versions; never mutate protected state
canon deps resolve        # produce exact graph/lock/policy report
canon candidate build     # build quarantined upstream/downstream set
canon release snapshot    # write immutable ReleaseSet manifest
canon release verify      # Crucible, hosts, CFF, SBOM, signatures
canon release promote     # move authorized channel pointer only
canon release rollback    # select prior immutable snapshot and record incident
```

The delivery invariant is:

```text
Resolve → Lock → Review → Build → Prove → Snapshot → Promote → Observe
```

Never:

```text
Merge → discover dependencies → patch production → reconstruct evidence
```

---

## 44 · FOUR-CHANNEL DEVELOPMENT AND TEST LAW

CLI, desktop, mobile, and web are delivery channels around one application and
one verdict path. They are not four applications.

### 44.1 · Predominant channels

Two forms of predominance are deliberately separated:

```text
reference development host = CLI
primary interactive product host = Avalonia desktop
```

The CLI predominates engine development because it is the fastest deterministic
way to execute CanonicalFacts, exact `System.Decimal`, rule packs, CFF replay,
properties, mutations, fuzz cases, and NativeAOT smoke tests without UI or
platform noise.

Desktop predominates product/workflow development because GSTFlow is an
offline utility with file intake, batch work, evidence review, corrections,
export, printing, and professional use. Avalonia desktop supplies the shortest
feedback loop for the real interactive experience and shares application/view
model behavior with Avalonia mobile.

```text
CLI proves truth quickly.
Desktop proves that truth is usable.
Mobile proves capture and field operation.
Web proves zero-install inspection within its declared fidelity.
```

Web or mobile becomes predominant only after measured users show that its
workflow, not architectural enthusiasm, dominates actual usage.

### 44.2 · Ninety-percent sharing target

At least 90% of behavioral capability belongs outside channel shells:

```text
shared behavior
  = CanonFlow/GSTFlow domain types
  ⊕ rules and canonicalization
  ⊕ application use cases
  ⊕ validation and evidence composition
  ⊕ CFF import/export/replay
  ⊕ capability and presentation models
  ⊕ canonical scenarios and fixtures
```

Channel-owned behavior is limited to:

```text
CLI     = argument/stdin/stdout formatting and process exit codes
Desktop = windowing, file picker, keyboard, print, drag/drop, desktop storage
Mobile  = camera, QR, share sheet, permissions, lifecycle, constrained storage
Web     = browser file APIs, download/share, sandbox/storage, responsive DOM
```

The 90% target is measured by supported use cases and scenario reuse, not by
gaming line counts. For every supported use case, the shell should call the
same application command/query and consume the same presentation model.

Recommended development-effort allocation until external usage changes it:

| Area | Approximate effort | Reason |
|---|---:|---|
| Pure core, application services, CLI reference scenarios | 50–60% | establishes deterministic behavior and rapid evidence |
| Avalonia desktop workflow and headless UI | 25–30% | primary utility experience and professional workflow |
| Web inspector/gateway | 8–12% | zero-install reach, but only within proven Fable/browser fidelity |
| Mobile platform integration | 5–10% | camera/share/lifecycle risks need targeted device proof, not duplicated logic |

This is planning guidance, not a quota. A camera release temporarily moves more
effort to mobile; a browser compatibility defect moves more to web.

### 44.3 · Shared application boundary

```text
Channel input
→ channel adapter
→ ApplicationCommand
→ CanonicalFacts
→ one verify function
→ VerdictEnvelope
→ PresentationModel
→ channel renderer
```

Suggested layers:

```text
GSTFlow.Core          pure facts, values, errors, outcomes
GSTFlow.Rules         pure deterministic statutory evaluation
GSTFlow.Application   import/confirm/verify/explain/export/replay use cases
GSTFlow.Presentation  message keys, typed parameters, view state, commands
GSTFlow.Ports         capability records and adapter contracts
GSTFlow.Cli           reference process shell
GSTFlow.Desktop       Avalonia desktop composition root and views
GSTFlow.Mobile        Avalonia mobile composition root and platform adapters
GSTFlow.Web           Fable/browser shell within proven capability manifest
```

Desktop and mobile may share Avalonia controls, styles, view models, and
navigation state where their interaction model is genuinely the same. They may
use different views when screen size, camera, keyboard, or batch work demands
it. Web shares contracts, presentation models, scenario fixtures, and only the
F# logic that passes cross-host agreement; it does not duplicate verdict code
to mimic .NET behavior.

### 44.4 · Ports, fakes, and mock discipline

Pure domain/rule functions use values, not mocks. Effects enter only through a
small application port record:

```fsharp
type AppPorts =
    { Now: unit -> System.DateTimeOffset
      ReadFile: FileRef -> Async<Result<byte array, IoError>>
      WriteFile: FileRef * byte array -> Async<Result<unit, IoError>>
      PickFile: unit -> Async<Result<FileRef option, UiError>>
      ScanQr: unit -> Async<Result<QrPayload option, CameraError>>
      Share: ShareArtifact -> Async<Result<unit, ShareError>>
      Print: PrintArtifact -> Async<Result<unit, PrintError>>
      LoadTrustSnapshot: unit -> Async<Result<TrustSnapshot, TrustError>>
      LoadRulePack: PackId -> Async<Result<VerifiedPack, PackError>> }
```

Prefer deterministic handwritten fakes over interaction-heavy mocking
frameworks. The exact fake syntax may change, but these laws do not:

```text
fixed clock · deterministic IDs · seeded generators · no real network
in-memory files · synthetic taxpayer data · explicit success/failure scripts
recorded outputs for assertions · same contract suite against fake and real adapter
```

Do not fake the rule engine, decimal arithmetic, canonical digest, signature
verification algorithm, or CFF parser in application acceptance tests. Use
known inputs and the real deterministic implementation. Fake only the platform
boundary that supplies or stores bytes and invokes OS capabilities.

### 44.5 · Canonical mock/scenario bundle

Every shared scenario is a portable deterministic fixture:

```text
ScenarioBundle
  = scenario ID and purpose
  ⊕ fixed clock/locale/time zone
  ⊕ input bytes and raw-evidence digest
  ⊕ canonical facts or expected normalization failure
  ⊕ exact rule pack and trust snapshot
  ⊕ expected VerdictEnvelope or independently derived properties
  ⊕ expected presentation message keys/typed parameters
  ⊕ expected files/shares/prints
  ⊕ allowed channel capabilities
```

Minimum mock corpus before a channel is activated:

```text
1. valid invoice happy path
2. malformed GSTIN/document structure
3. ₹0.01 and Section 170 rounding boundaries
4. missing fact/evidence producing Unknown or NeedsEvidence
5. stale/expired/revoked pack or trust snapshot
6. hostile/truncated/oversized input and CFF archive
7. import → verify → explain → export → clean-machine replay
8. offline execution with network denied
9. cancellation, storage-full, permission-denied, and interrupted write
10. cross-channel canonical verdict/digest agreement
```

Mocks never author the legal oracle. Expected statutory results follow §3 and
§16 review. Platform-failure expectations may be authored from the adapter
contract.

### 44.6 · Test pyramid

| Layer | Runs | Purpose |
|---|---|---|
| Pure unit/example | every save/PR | values, parsers, rules, precedence, exact decimal boundaries |
| Property/metamorphic/mutation | PR/nightly by risk | laws, optimizer, canonicalization, hostile boundaries |
| Application scenario with fakes | every PR | complete use cases without OS/UI instability |
| Adapter contract | every affected PR | same behavioral contract against fake and real file/camera/share/storage adapter |
| CLI in-process/process | every PR | reference host, JSON/text output, exit code, cancellation, NativeAOT candidate |
| Avalonia headless | every UI PR | binding, layout, commands, keyboard/input, accessibility state |
| Web browser | affected PR + nightly | Fable agreement, file flow, offline behavior, Chromium/Firefox/WebKit |
| Mobile emulator/simulator | affected PR + nightly | lifecycle, permissions, camera/share/file adapters, install/upgrade |
| Real device/OS smoke | preview/RC/stable | camera, QR, memory, storage, print/share, signing and packaging reality |
| Cross-channel agreement | nightly/RC/stable | same scenario yields same canonical verdict/digest where capability is claimed |

Fast tests are numerous; full-device tests are few and high value. A passing UI
snapshot never substitutes for a semantic assertion.

### 44.7 · Channel-specific proof

#### CLI — reference host

```text
stdin/file/CFF input · canonical JSON output · deterministic text output
exit-code contract · cancellation · broken pipe · read-only directory
locale/time-zone matrix · locked offline run · NativeAOT execution smoke
```

Every new application use case is exposed through a testable CLI command or
scenario runner before or alongside UI work. The CLI is not required to expose
every visual convenience; it must expose the behavior needed to reproduce and
diagnose the verdict.

#### Desktop — primary product host

```text
Avalonia headless view/view-model tests
keyboard-only and screen-reader semantics
drag/drop, file picker, print and batch-work fakes
visual regression only for stable high-value layouts
real Windows install/upgrade/uninstall and NativeAOT smoke
large batch, storage-full, interrupted export, and offline tests
```

#### Mobile — focused field host

```text
shared application/view-model scenarios first
fake camera/QR/share/permission/lifecycle adapters on every PR
Android emulator and iOS simulator for affected changes
at least one representative low-memory Android device before preview
physical iPhone/iPad proof before iOS stable release
camera focus/rotation/denial/background/resume/storage-pressure cases
```

Mobile does not inherit support merely because Avalonia compiles. Camera,
permissions, lifecycle, signing, packaging, and real-device behavior are
separate release gates.

#### Web — constrained inspector/gateway

```text
canonical fixture agreement with CLI
Playwright on Chromium, Firefox, and WebKit
mobile/desktop viewport and touch/keyboard/accessibility checks
offline service-worker/cache behavior where activated
file-size/memory limits · malicious ZIP/input · no server upload assertion
decimal/DU/canonical digest compatibility corpus
```

If Fable/JavaScript cannot reproduce one statutory decimal, rule outcome, or
canonical digest, the web host remains an intake/editor/inspector and delegates
no production verdict claim to that implementation.

### 44.8 · Smooth feature-development loop

For each feature:

```text
1. Define one application use case and capability declaration.
2. Add an independently derived scenario/boundary or platform contract case.
3. Implement pure domain/application behavior.
4. Make the scenario pass in-process and through the CLI.
5. Add/update the desktop presentation and Avalonia headless test.
6. Reuse the same scenario for supported web/mobile shells.
7. Add only the platform adapter tests unique to each shell.
8. Run cross-channel agreement on canonical outputs.
9. Execute NativeAOT/browser/emulator smoke according to changed paths.
10. Update the capability matrix truthfully; unsupported channels remain explicit.
```

The fast loop should remain short enough to run continuously. Expensive fuzz,
browser, emulator, AOT, install, and real-device suites run by affected-path
selection plus nightly/RC gates; they are never removed merely to shorten CI.

### 44.9 · CI matrix and activation gate

| Event | Required channels |
|---|---|
| Core/rule/application PR | pure + application fake + CLI; affected agreement |
| Desktop UI PR | preceding set + Avalonia headless |
| Web PR | preceding set + CLI/Fable agreement + Playwright Chromium; full browsers nightly |
| Mobile PR | preceding set + shared Avalonia + affected emulator; real device at preview |
| Nightly | all active channel agreement, browsers, emulator, NativeAOT |
| Preview/RC | installed desktop, browser matrix, emulator and declared real-device smoke |
| Stable | complete release set, clean-machine install, offline/replay/rollback and channel-specific proof |

A channel moves from `STUBBED` or `EXPERIMENTAL` to `IMPLEMENTED` only when:

```text
capability manifest exists
∧ canonical scenarios are reused
∧ platform adapters pass their contract suites
∧ crash/resource/offline boundaries pass
∧ package/install artifact is executed
∧ unsupported features are disclosed
∧ a named channel custodian accepts maintenance
```

It moves to `PROVEN` only with cross-host agreement, release evidence, and at
least one independent real workflow on that channel.

The four-channel invariant is:

```text
One truth · one application workflow · four thin adapters · explicit capability gaps
```

---

## 45 · IMPLEMENTATION REFERENCES

The reviewed reference list of `CANONFLOW_BASE.md` §45 is carried unchanged
(original law-driven format · CanonFlow Manifesto/GOVERNANCE/ADR-015/ADR-016 ·
OpenMetadata schema and custom-properties documentation · OKF v0.1 draft ·
Dhall safety/integration/grammar references · honest-state tickets · EDIFlow
850 profile pattern · GSTFlow source · FsCheck · Microsoft.Testing.Platform ·
Avalonia testing/headless/Appium · NativeAOT deployment and warning guidance ·
Fable releases · NuGet resolution/lock/CPM · GitHub Actions security and
immutable releases · Playwright browsers/emulation · functional-skills pinned
review commit `f990956`), with the review-snapshot commit set:

```text
CanonFlow 4669b5f · GSTFlow 18fe5c0 · EDIFlow 72f007b
docs fdb37bc · site 97b1796          (reviewed 2026-07-14)
```

▲ Added reference entries — the governed companion documents:

```text
CFF_PIPELINE.md                   execution overlay (packets, gates, cage)
LIQUID_CANONFLOW.md               proof annex (SMT over Class D / Class P)
DYNAMIC_CFF_TESTING.md            modal testing vocabulary
CANON_DYNAMIC_CRUCIBLE.md         CDL engine specification
LAT.md                            knowledge-graph profile
CFF_INVOICE_EVIDENCE_PROFILE.md   cff.invoice-evidence/1 draft
harness-seal-admissibility.md     Forge-era authority corrections (dormant)
manifesto.md                      public projection, draft 0.1
```

Repository prose and checklists are intention evidence, not implementation
proof. Only source, executable tests, CI, artifacts, and independent contact
can advance a capability to `PROVEN`.

---

## 46 · FUNCTIONAL BOUNDARY, BOUNDED EXECUTION, AND RUN-LEDGER LAW

Functional style is useful only where it strengthens observable contracts. F#
syntax, a `Result`, a discriminated union, or the word "SOTA" is not proof.

This law applies to CFF readers/writers, source intake, batch adapters,
reconciliation, rule-pack processing, exporters, and every potentially
unbounded application workflow. It does not mandate a specific library.

### 46.1 · Boundary promotion

External representations never enter the trusted core directly:

```text
UntrustedBytes
→ BoundedBytes
→ ParsedWire
→ ConstrainedInput
→ VerifiedArtifact
→ ReplayableInput
```

Each transition states exactly what it proves:

```text
BoundedBytes      = media/size/count/depth limits satisfied
ParsedWire        = declared syntax/schema decoded
ConstrainedInput  = representational/domain invariants satisfied
VerifiedArtifact = declared digests/signatures/trust policy assessed
ReplayableInput   = versions, evidence, and deterministic inputs are complete
```

These guarantees remain distinct:

```text
parse success       ≠ authenticity
type construction   ≠ statutory truth
digest verification ≠ signer trust
signature validity  ≠ legal correctness
schema validity     ≠ supported capability
```

Trusted-state constructors are private or otherwise uncallable from the wire
boundary. The only promotion path returns a typed outcome and evidence.
`unsafeCreate`, unchecked casts, null-forgiving assertions, reflection bypasses,
and default values cannot promote external input, Gold expectations, rule-pack
content, trust material, or CFF data.

Raw, parsed, normalized, and verified forms remain linked by provenance; a
trusted type never overwrites the bytes from which it was derived. Outbound
conversion is total only when the target can represent the meaning exactly.
Otherwise it returns a classified fidelity result under §34.

For CFF, the type-state sequence is at least:

```text
UntrustedArchive
→ InspectedArchive
→ ParsedManifest
→ VerifiedEntries
→ TrustAssessedBundle
→ ReplayableCff
```

No API accepting `UntrustedArchive`, `InspectedArchive`, or `ParsedManifest` may
execute a verdict or expose entry bytes as trusted content.

### 46.2 · Workflow signature before implementation

Every public workflow declares before its body:

```text
input state · output state · typed failures · evidence produced
effects/dependencies · cancellation · ordering · resource budget
```

Conceptually:

```text
Workflow : Dependencies → Context → Input → Cancellable<Result<Output × Evidence, Error>>
```

The concrete F# shape may use a plain value, `Result`, `Async`, `Task`, or a
composed effect appropriate to the boundary. The signature must not hide I/O,
failure, time, randomness, network access, mutable global state, or cancellation.

Lifecycle stages are separate types when skipping a stage would violate a
security, fidelity, statutory, or replay invariant. Dependencies are explicit
parameters/ports and are wired at a composition root. DTO, serializer,
filesystem, database, UI, clock, and network types remain outside the pure
verdict kernel.

Typed error families distinguish at least:

```text
InputRejected · ContractViolation · SecurityViolation · ResourceLimit
TransientDependency · PermanentDependency · Cancelled · InternalDefect
```

Expected failures are values. Unexpected library/infrastructure exceptions are
observed at the owning boundary and converted where recovery is safe. Process-
integrity failures are not swallowed or misreported as ordinary validation.

Retry is permitted only for a classified transient failure with bounded
attempts, cancellation, backoff where applicable, and an idempotency contract.
Input, contract, security, statutory-interpretation, and permanent failures
require disposition—not a retry loop. ▲ Formally: for a permanent failure
class `b`, `b → [retry]b` holds in every state, hence `b → [retry*]b` — a
retry loop on a permanent error is a proven no-op with a resource bill. The
failure classifier, not the retry budget, carries the correctness burden.

### 46.3 · Bounded and structured execution

Every edge that can receive unbounded or attacker-controlled work declares:

```text
item and byte capacity
producer and consumer ownership
overflow/backpressure behavior
ordering guarantee
maximum concurrency
cancellation and timeout
normal completion
fault propagation
retry/quarantine policy
depth/throughput/latency/error evidence
CPU/memory/disk/file-count budget
```

If any item is unknown, the pipeline is `DESIGNED` or `EXPERIMENTAL`; it is not
production-complete.

Required laws:

```text
every child task is awaited, supervised, or explicitly owned
every fan-out is bounded
every queue is bounded unless boundedness is proven by construction
every failure reaches the supervisor and proof record
cancellation stops new work and accounts for in-flight work
terminal completion is awaited before success is reported
```

Forbidden on potentially unbounded input:

```text
unbounded task/future lists · unbounded queues · whole-corpus materialization
fire-and-forget verdict work · nested uncontrolled parallelism
per-item logging without a governed budget · silent overflow/drop
```

Concurrency is an execution optimization, never a semantic input:

```text
schedule/order of completion changes
⇒ canonical facts, verdict, evidence ordering, and digest remain unchanged
```

Where output order is significant, it is declared and canonicalized. Where it
is not significant, the runtime may complete work out of order but must emit the
same canonical result. Performance changes require realistic before/after
evidence. Local mutation, pooling, structs, or buffers are allowed only inside a
measured hot path when they cannot escape or alter semantics.

### 46.4 · Conservation and run ledger

No intake, transformation, reconciliation, or export may silently lose an item.
Each discovered item occupies exactly one terminal state or a visible incomplete
state:

```text
Discovered
= Accepted ⊎ Rejected ⊎ Quarantined ⊎ Deferred ⊎ Unprocessed
```

For a run declared complete:

```text
Deferred = 0 ∧ Unprocessed = 0
```

If product policy permits deferred work, the run is explicitly partial and its
capability/status must say so. Counts alone are insufficient where content can
be duplicated or changed; governed stages also record byte counts, stable IDs,
input/output digests, duplicate keys, and transformation/rejection reasons.

Every material stage writes an append-only run record containing at least:

```text
run/stage ID · contract/schema/profile version · source/code/tool versions
input locators/digests/counts/bytes · output locators/digests/counts/bytes
accepted/rejected/quarantined/deferred/unprocessed counts
start/end time · status · resource budget/usage · errors/retries
proof/evidence locator · correction/supersession reference
```

Old records are never edited to make a run look successful. A correction or
reclassification is a new linked record. Dashboards and mutable databases may
index the ledger but never replace it as evidence.

Reconciliation reports explain cardinality changes and drill down to item-level
reasons. Aggregate equality without traceable membership is not sufficient.

▲ The conservation invariant is declared and tested as an induction pair
(§14.6): established at intake, preserved per stage, therefore held for the
whole run — never asserted only at the end state.

### 46.5 · Atomic finalization and restart

Material output follows a commit protocol:

```text
temporary write
→ flush/close
→ compute digest and counts
→ validate contract
→ durable finalization
→ append success record
→ expose to downstream readers
```

`.partial`, incomplete, uncommitted, or manifest-less outputs are never inputs
to the next stage and are never included in a release/CFF proof. On filesystems
with reliable same-volume atomic rename, finalization uses it. Other storage
profiles use a versioned two-phase commit/marker whose conformance tests prove
that readers cannot observe an uncommitted result.

A restart:

```text
reads the ledger
verifies committed bytes before reuse
never treats temporary output as committed
never rewrites a succeeded deterministic unit silently
records every retry, invalidation, correction, and replacement
```

Retrying a deterministic unit with identical pinned inputs must produce the
same canonical digest. A mismatch is a blocking nondeterminism incident.

### 46.6 · Context and skill budget

The normative Base remains complete, but an agent receives the smallest emitted
`AgentContext` needed for its approved task. Deep guidance is loaded on demand
by stable identity; stack facts already inferable from locked project files are
not copied into every prompt.

```text
AgentContext
= governing invariants
 ⊕ allowed/forbidden operations
 ⊕ exact input/output schemas and digests
 ⊕ relevant source/evidence
 ⊕ commands, budgets, stop conditions, and approvals
```

▲ Composed per §15.5: packet projection (authority) ⊕ canonical projection
(semantics) ⊕ lat sections (explanation, untrusted).

An external skill, prompt pack, style guide, or model memory:

```text
may guide a candidate implementation
may not amend this Base, an ADR, Gold truth, legal interpretation, or release policy
may not auto-update inside a proof/release environment
may not become an undeclared runtime or build dependency
```

When a skill materially influences generated work, record its repository,
commit/version, and disposition in the work packet. The deterministic release
must remain reproducible without replaying the model response. External prose
is linked or independently restated only under compatible licensing and
provenance; repository availability is not a reuse license.

### 46.7 · Import filter

A practice enters the normative Base only when:

```text
DomainNeutral
∧ Enforceable
∧ Falsifiable
∧ NonDuplicative
∧ ReducesFailureOrAmbiguity
∧ RespectsOwnershipLaw
```

It enters `CanonFlow.Core` only under the stricter second-flow promotion rule in
§2. Language style, named libraries, vendor procedures, operating-system
commands, ETL topology, and convenience syntax remain implementation guidance.

Disposition of the reviewed functional-skill material:

| Candidate | Decision |
|---|---|
| Boundary parsing into constrained/private states | **ADAPT** through §46.1; types do not prove legal truth |
| Typed workflows, explicit effects and error families | **ADOPT** through §46.2 |
| Bounded queues, fan-out, cancellation, completion and fault ownership | **ADOPT** through §46.3 |
| Reconciliation math, append-only manifests and restart discipline | **ADOPT** through §§46.4–46.5 |
| Minimal constitution plus deep guidance loaded on demand | **ADAPT** through §46.6 and canonical `AgentContext` |
| Pure core, DUs/records, explicit uncertainty, property tests, dependency minimalism | **ALREADY COVERED**; no duplicate law |
| Oracle/Elasticsearch/TSV/Parquet/DuckDB procedures and exact F# libraries | **KEEP OUTSIDE BASE**; profile/implementation guidance only |
| `unsafeCreate` for external/verdict/CFF/trust data | **REJECT** |
| Writer-style telemetry as a mandatory abstraction | **REJECT**; evidence contract matters, not one abstraction |
| Property tests, type safety, or "SOTA" label treated as proof | **REJECT** under §§3, 14, and 31 |

The source reviewed for these principles was the initial `functional-skills`
commit pinned in §45. At review time it had no repository license, automated
tests, or CI; therefore CanonFlow imports no files, dependencies, or authority
from it. Only the independently stated, enforceable laws above enter this Base.

### 46.8 · ▲ EXECUTION OVERLAY AND ANNEX REGISTRY

The Base is the constitution; execution and specialized assurance live in
governed companion documents with one-way authority:

```text
Document                          Role                        Status
CFF_PIPELINE.md                   execution overlay           active
  (work packets, gates A–L, agent cage, GSD/gstack/
   Superpowers dispositions, capsule pause/resume)
LIQUID_CANONFLOW.md               proof annex (SMT/Class D)   DESIGNED
DYNAMIC_CFF_TESTING.md            modal testing vocabulary    DESIGNED
CANON_DYNAMIC_CRUCIBLE.md         CDL engine specification    DESIGNED
LAT.md                            knowledge-graph profile     DESIGNED
CFF_INVOICE_EVIDENCE_PROFILE.md   cff.invoice-evidence/1      DESIGNED
Forge dossier                     AI generation line          DORMANT
manifesto.md                      public projection           draft 0.1

Authority: this Base > accepted ADRs > overlay > annexes > profiles.
A conflict resolves upward; an annex never amends the Base except through §29. Each annex passed the §46.7 import filter on entry
and records its own wake conditions; §31 governs every status above.
```

Two overlay mechanisms are promoted into the Base because they are
load-bearing everywhere:

```text
▲ THE ASSUMPTION LEDGER. Any invariant deliberately left at sampled
  confidence when a proof was plausible, and any trusted-but-unproven
  lemma, is a Declared-grade entry (§3): claim · accountable declarant ·
  StandIn (the discharging property holding the fort — NON-OPTIONAL; an
  entry with no falsifier fails the evidence lint) · review-by date. The
  ledger ships inside the proof manifest. Honest debt, with an expiry.

▲ PACKET–COMMIT BINDING. Every commit on a work-packet branch carries
  Work-Id and Contract-Digest trailers; CI rejects contradictions. The
  release-set manifest can enumerate contributing packets by trailer
  query — provenance without a second ledger.
```

---

## 47 · ▲ AMENDMENT LOG (provenance of every ▲)

| § | Amendment | Source document |
|---|---|---|
| 0.1 | Terminology reservation law | CFF_PIPELINE §0.1 · manifesto vocabulary · CDC §0 |
| 3 | Proof ≠ legal correctness; witnesses = Derived | harness-seal §1 · LIQUID §0/§4 |
| 5 | Dimension declaration; normative UoM; analyzer rule; cff-json-decimal-string/1 | LIQUID §2 · invoice profile §7 |
| 7 | UoM noted as normative instantiation of refinement-identity permission | LIQUID §2 |
| 11.2 | Decidability dividend | LIQUID §1/§3 |
| 12 | Witness-backed Studio diagnostics | LIQUID §3 (VERIFY-TOTAL/DET) |
| 13 | prove-graph + run-dynamic (dormant); manifest additions; collapse verification; assumption ledger | LIQUID §4/§9 · DYNAMIC §3 · CDC §4/§13 |
| 14.4 | Modal obligation classification + discharge classes | DYNAMIC §2/§7 · CDC §4 |
| 14.5 | Vacuous-truth guard | CDC §8 · Base 14.3 formalized |
| 14.6 | Induction-pair rule | DYNAMIC §4 · CDC §7 |
| 15.3 | Proof battery in candidate validation; author-agnostic gates | LIQUID §7 · CDC §6 |
| 15.4 | lat/OKF two-plane demarcation | LAT §0.1 |
| 15.5 | Three-plane AgentContext | LAT §4.1 · CFF_PIPELINE §5.4 |
| 18 | evaluation/ + proof/ layout; acyclic digest; envelope purity; redaction; ID schemes; evidence ≠ proof | invoice profile §§1–6 |
| 19 | Dual signature meanings; scope.json bijection; key hygiene | harness-seal §4 · invoice profile §8.2 |
| 34.2 | Drift proof backend; ComplexOrUnknown reserved | LIQUID §3/§8 |
| 34.3 | Invariant-binding law | LIQUID §6 (via the Liquid Haskell case study) |
| 35.11 | Forge, CDC, proof battery registered as governed dormant items | CFF_PIPELINE §0.1 · CDC §16 · LIQUID §9 |
| 36 | Gate 6 table proofs; Gate 7 battery + zero-false-Proven benchmark | LIQUID §9 |
| 38 | Ten new landmines | LIQUID §5 · CDC §8 · LAT §16 · CFF_PIPELINE §0.1 |
| 39 | Verification-friction measure | LIQUID §7 (via the Liquid Haskell case study) |
| 43.1 | Solver/encoder/CDC digests; provenance cache key | LIQUID §9 · harness-seal §8 |
| 43.6 | Packet–commit trailer rule cross-reference | CFF_PIPELINE §5.1 |
| 43.8 | canonicalizationProfile/executionProfile explicit in CFF manifest fields | invoice profile §3/§7 |
| 45 | Companion documents added as references | this edition |
| 46.2 | Broken-TV formalization of the retry law | DYNAMIC §4 · CDC §5 |
| 46.4 | Conservation as induction pair | DYNAMIC §4 |
| 46.6 | Three-plane composition cross-reference | LAT §4.1 |
| 46.8 | Annex registry; assumption ledger; packet–commit binding | CFF_PIPELINE · LIQUID §6 · LAT §7.1 |

---

## 48 · ▲ VERIFICATION RECORD (this edition)

Checks performed on this integrated text, 2026-07-18. Each is a bounded
referential/consistency claim in the sense of §14.4 — none is a claim of
legal or implementation correctness:

```text
V1  Terminology sweep: no occurrence of CFDL, CFRL, CFFAssurance, or "CFF"
    denoting the Forge or the movement; §0.1 vocabulary used throughout.
V2  Cross-references: every ▲ section referenced by another ▲ section
    exists (§0.1↔§38 · §5↔§7 · §12↔§13 · §14.4↔§18/§34.3 · §14.6↔§46.4 ·
    §15.5↔§46.6 · §43.6↔§46.8 · §36↔§13).
V3  Status coherence: every capability introduced by an amendment carries
    DORMANT or DESIGNED status with a named wake condition in its source
    document; none is stated as implemented (§31 respected).
V4  Authority ordering: no annex text in this edition grants an annex
    amendment power over the Base; §46.8 ordering is consistent with every
    ▲ insertion.
V5  Import filter: each amendment's source document records its §46.7
    self-test; no amendment introduces a new semantic law outside those
    filters — each mechanizes or names an existing Base law.
V6  Provenance completeness: every ▲ in the text has a row in §47.
V7  Conservation: no reviewed normative text was dropped or weakened; the
    edition adds gates and never removes a stop.

NOT verified (stated honestly): the twice-submitted third-party review of
the v2 redline (arrived empty; unincorporated) · any implementation claim ·
statutory adequacy of any example. Adoption requires §29.
```

> A good constitution is one whose amendments feel like discoveries rather
> than repairs. The Class D bounds imposed for safety turned out to buy
> proofs; the vacuous-test law stated informally gained a detector; the
> drift engine gained the model it said it lacked; the knowledge the Base
> insisted must be governed got a plane to live on. The moat is still the
> accountable chain from authoritative source to reproducible verdict — and
> the discipline to stop whenever one link is missing. The amendments only
> add links; none removes a stop.
