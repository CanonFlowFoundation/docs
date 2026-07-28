# CFF Verification-First, Constructive-When-Proven

## XP/Lean milestone plan for regulated software

Status: active strategy; CM0-CM4 completed 2026-07-28
Scope: CanonFlow Foundation, CanonFlow, FsAssay, ONDCFlow, GSTFlow, EDIFlow and EPCIS flows

## 1. Decision

CanonFlow Foundation remains a **verification-first business and technical
ecosystem**.

Constructive modelling is retained as an optional capability:

- dormant by default;
- activated by verified customer or regulatory demand;
- limited to explicitly admitted patterns;
- subordinate to authoritative external rules;
- removable without weakening verification;
- never presented as universal domain truth.

The stable product promise is:

> CFF verifies whether regulated software preserved its admitted obligations and
> produces reproducible evidence of what was and was not established.

The optional stronger promise is:

> For a bounded, admitted rule pattern, CFF may also generate a representation
> that makes the observed invalid states unconstructible.

The second promise never replaces the first.

## 2. Why this boundary matters

“Correct by construction” is always relative to an admitted rule and context.
It is not a universal theorem about the business.

For example:

```text
Write permission implies read permission
```

may be true for one RBAC policy and false for another. CanonFlow may construct a
permission model encoding `Write -> Read` only when an authoritative policy
states that implication and the projection has passed its proof gates.

Without that evidence, the relationship is classified as one of:

- `CandidateRequiringApproval`
- `Conditional`
- `DatabaseOwned`
- `Unsupported`

It is never silently upgraded to `Exact`.

## 3. Two operating modes

### Mode V — Verification

Always available and always the foundation:

```text
Authoritative source
    -> admitted obligation
    -> observed implementation/evidence
    -> Pass | Fail | Inconclusive | ToolFailure
    -> optional sealed receipt
```

Mode V must remain useful even if no constructive projection exists.

### Mode C — Constructive projection

Optional and pattern-scoped:

```text
Admitted obligation
    -> recognized constructive pattern
    -> generated type + boundary mapping + laws
    -> compiler/FsAssay preservation checks
    -> oracle/differential evidence
    -> constructive projection result
```

Mode C adds evidence and developer ergonomics. It does not determine regulatory
authority and cannot turn an unsupported rule into a verified one.

## 4. Component responsibilities

| Component | Fixed responsibility | Constructive responsibility |
|---|---|---|
| CanonFlow | Normalize obligations, execute verification, classify fidelity, aggregate evidence and issue receipts | Recognize admitted patterns and generate types, mappings, laws and obligation manifests |
| FsAssay | Verify F# engineering policy and report honest static-analysis evidence | Detect structural weakening or bypass of a CanonFlow obligation; never infer business semantics |
| ONDCFlow | Own ONDC sources, source locks, profiles, lifecycle rules and trace corpus | Adopt constructive representations for individually admitted ONDC concepts |
| GSTFlow | Own GST sources, tax-context rules, derivations and fixtures | Adopt constructive representations for bounded document/status rules |
| EDIFlow | Own EDI standards, transaction-set rules and interchange evidence | Adopt constructive representations for bounded segment/state alternatives |
| EPCIS flows | Own EPCIS event semantics, vocabularies and trace evidence | Adopt constructive representations for admitted event-type/payload relationships |
| SDKs and Actions | Make verification accessible in consumer languages and CI | Expose generated models only when their projection status and source digest are visible |

No vertical is rewritten. Each vertical adopts the shared contract incrementally.

## 5. SDK and numeric-parity policy

SDK convenience must never create a second interpretation of a regulated rule.
All SDKs delegate to the same F#/.NET engine through either a native .NET façade
or the versioned OCI protocol.

Support status is independent of vertical-profile authority:

| Language | Status | Boundary |
|---|---|---|
| F# | Authoritative implementation | Native engine and advanced F# API |
| TypeScript/JavaScript | Active alpha; primary external SDK | Hardened OCI client |
| C# | Planned first-class .NET consumer | Thin native façade over the same F# assembly |
| Python | Planned preview | Thin OCI client; no independent rule implementation |
| Dart | **Experimental / under evaluation** | Opaque transport experiments only until decimal parity is discharged |

The C# façade must expose ordinary .NET classes, enums, overloads,
`IReadOnlyList<T>`, nullable-reference annotations and `System.Decimal`. It must
not leak `FSharpOption`, `FSharpList`, `FSharpResult`, public F# union
representations or `FSharpFunc` into the supported C# API.

Python initially remains an OCI wrapper. Python `Decimal` may be used in
consumer helpers, but the SDK must serialize regulated decimal values through
the canonical protocol representation and must not independently re-evaluate
rules.

### Decimal boundary

Cross-language regulated decimal values use canonical decimal strings:

```json
{ "price": "1234.50" }
```

Native F# and C# may parse that representation into `System.Decimal`; Python may
parse it into `decimal.Decimal`. TypeScript and Dart must not convert regulated
money to an IEEE-754 `number`/`double` inside an authoritative evaluation path.

A canonical string transport prevents accidental floating-point conversion. It
does **not** prove that an embedded target implements .NET decimal semantics.

### Dart containment

Fable currently classifies Dart as a beta target. CFF's observed decimal
representation gap is therefore treated as an unresolved verification blocker,
not patched with approximate floating-point arithmetic.

A thin Dart client may be prototyped only when it:

- labels itself `Experimental`;
- treats input bundles and receipts as opaque canonical bytes or strings;
- delegates authoritative evaluation to the pinned OCI engine or another
  explicitly admitted remote boundary;
- performs no regulated decimal arithmetic;
- makes no offline embedded-engine or parity claim.

Dart cannot be promoted to supported embedded evaluation until all of these
gates pass:

1. Exact finite decimal representation with declared precision and scale.
2. Parse/format vectors for signs, leading and trailing zeros, scale, extrema and
   locale independence.
3. Arithmetic and comparison agreement with the admitted .NET operations.
4. Explicit rounding-mode and midpoint vectors.
5. Overflow, underflow and division-by-zero agreement.
6. Canonical JSON byte agreement.
7. Verdict and receipt-digest agreement against native F#.
8. Property-based and hostile mutation tests.
9. Pinned compiler/runtime versions and reproducible clean builds.
10. A real Flutter/Dart design partner justifying continued maintenance.

Until those gates pass, CFF must not claim:

- supported Dart evaluation;
- a Dart “same engine” implementation;
- Dart correct-by-construction monetary models;
- cross-runtime decimal or receipt parity.

If exact decimal support never becomes viable or no customer pulls it, Dart
remains experimental or returns to dormancy without affecting F#, C#,
TypeScript or Python support.

References:

- [F# component design guidelines for cross-.NET APIs](https://learn.microsoft.com/en-us/dotnet/fsharp/style-guide/component-design-guidelines)
- [Fable target status](https://fable.io/docs/getting-started/cli.html)

## 6. Constructive admission

A constructive candidate may enter active work only when all of the following
hold:

1. A design partner, production defect, regulatory change or repeated support
   problem demonstrates demand.
2. The authoritative source and version are pinned.
3. The normalized rule is representable without guessed business meaning.
4. A closed pattern recognizer exists; opaque fallback is not allowed.
5. `encode` and `decode` can be specified.
6. A real oracle or finite truth table can challenge the projection.
7. Positive, negative and mutation specimens are available.
8. Required gates are non-empty and executable.
9. Constructive WIP is currently zero.

If any condition is absent, the candidate remains dormant and Mode V continues.

## 7. Projection states

```fsharp
type ProjectionState =
    | Dormant
    | Candidate
    | Experimental
    | Admitted
    | Retired

type Derivation =
    | Exact
    | EquivalentUnderNormalization of normalizationId: string
    | CandidateRequiringApproval
    | Conditional of assumptionIds: string list
    | DatabaseOwned of enforcerId: string
    | Unsupported of reasonKey: string
```

Only `Exact` and reviewed `EquivalentUnderNormalization` projections may emit
production domain models. Candidate, conditional, database-owned and unsupported
rules remain verification/reporting outputs.

Constructive coverage is reported separately from verification coverage.

## 8. XP operating law

Every constructive work item follows:

```text
Admit -> RED witness -> smallest GREEN -> REFACTOR -> cumulative re-gate -> review
```

The following CanonFlow XP requirements are adopted:

- WIP equals one constructive pattern.
- Every work item has a non-empty gate set.
- RED pins the expected failing test and assertion digest before implementation.
- Agent-authored success text is not evidence; runner observations are.
- GREEN for an empty gate set is `Inconclusive`, never `Pass`.
- Integration reruns all accumulated gates.
- Refactor equivalence means equivalence under the admitted gates, not universal
  semantic equivalence.
- Rejected and abandoned attempts remain visible.
- Policy changes are separate work items and cannot legalize a failing change
  retroactively.
- Seal state and verdict remain orthogonal.

## 9. Lean operating law

- Pull constructive work from demonstrated demand; do not push speculative
  frameworks into every vertical.
- Deliver one end-to-end slice before adding a second pattern.
- Keep verification independently deployable.
- Prefer reversible generated artifacts and small commits.
- Measure customer outcome, not emitted type count.
- Stop when evidence is missing; do not fill gaps with implementation volume.
- Reuse the kernel across verticals, but keep vertical semantics with their
  owning profile.
- A dormant capability is an acceptable outcome, not unfinished work.

## 10. Milestones

### CM0 — Claim and ownership boundary

Objective: make the verification-first decision mechanically visible.

RED witnesses:

- an unsupported rule can currently receive “exact,” “certified” or equivalent
  language;
- a report can mix verification coverage and constructive coverage.

Deliverables:

- architecture decision fixing Mode V as foundational;
- claim vocabulary for `Verified`, `ConstructivelyProjected`,
  `Inconclusive`, `Unsupported` and `Experimental`;
- removal or qualification of unconditional “certified,” “100% mathematical
  fidelity” and “mathematically guaranteed” claims;
- dependency rule: vertical profiles depend on shared contracts, while the
  CanonFlow assurance kernel depends on no vertical or FsAssay;
- constructive feature flag/default state set to dormant.

Exit gates:

- no unsupported or empty assessment can produce `Pass`;
- verification works with constructive modelling disabled;
- claim scan and behavioral vectors pass.

Business result: customers understand exactly what CFF verifies today.

#### CM0 implementation record — 2026-07-28

Status: **Complete**

Implemented in CanonFlow:

- ADR `docs/adr/0002-verification-first-constructive-dormant.md` fixes Mode V
  as foundational and Mode C as dormant by default;
- `CanonFlow.Assurance.ClaimPolicy` owns the five-term public claim vocabulary
  and prevents constructive emission in the default mode;
- receipt verification rejects a `Pass` with no assessments, zero applicable
  rules, unevaluated applicable rules, incomplete health or non-conformant
  compliance;
- CLI, generated FsCheck comments and MCP output now describe scoped
  constraint-fidelity evidence rather than self-awarded certification,
  universal coverage or mathematical proof;
- SDK capabilities disclose the vocabulary, define `Verified` as bounded by
  admitted rules and evidence, and report constructive modelling as dormant
  with production emission disabled;
- `build-tools/test-cm0-boundaries.sh` mechanically enforces kernel dependency
  direction and prohibited public claims, and runs in evaluator CI.

Verification evidence:

- CM0 dependency and public-claim gate: passed;
- pinned .NET 10 evaluator image: compiled successfully;
- `CanonFlow.Assurance.Tests`: 18 passed, 0 failed;
- locked CanonFlow/ONDCFlow integration: 73 passed, 0 failed across ONDCFlow,
  assurance, XP, core-law and PostgreSQL integration suites;
- hardened evaluator container gates: passed, including four-verdict fixtures,
  deterministic receipts, offline verification, dormant capability disclosure
  and non-root/read-only execution;
- constructive mode remained dormant while the ordinary verification reducer
  independently produced a valid evidence-bounded `Pass`.

CM0 adds no constructive rule implementation and does not admit CM1
automatically.

### CM1 — Obligation and projection contract

Objective: define the shared pure contract without building a synthesizer.

RED witnesses:

- an empty obligation/gate list reaches a successful projection;
- a candidate derivation is serialized as exact;
- two materially different policies share a digest.

Deliverables:

- canonical obligation identifier and source digest;
- normalized predicate digest;
- projection state and derivation types;
- non-empty proof-gate references;
- structured assumption identifiers;
- versioned canonical obligation-manifest schema;
- explicit four-verdict and exit-code mapping.

Exit gates:

- arbitrary-byte parsing is total;
- canonical output is deterministic;
- empty gates produce `Inconclusive`;
- policy/source changes alter the protected digest;
- no prose participates in executable rule logic.

Business result: all verticals can describe obligations consistently without
being rewritten.

#### CM1 implementation record — 2026-07-28

Status: **Complete**

Implemented in CanonFlow:

- ADR `docs/adr/0003-obligation-manifest-contract.md` fixes the vertical-neutral
  obligation, projection, canonicalization and exit-code contract;
- canonical structured types now cover obligation, proof-gate, assumption,
  admission and unsupported-reason identifiers;
- every obligation binds an authoritative source digest, normalized predicate
  digest and a non-empty set of versioned proof-gate references with
  implementation digests;
- closed projection derivations distinguish `None`, `Candidate`, `Admitted` and
  `Unsupported`; candidates require structured assumptions and serialize as
  `CandidateRequiringApproval`, never exact;
- an admitted projection evaluates to `Pass` only when every required gate is
  present and passes; empty or incomplete gate evidence is `Inconclusive`;
- `CanonFlowObligationManifest` schema `1.0` has a non-empty obligation array,
  canonical ordering and a protected SHA-256 digest over policy, sources,
  predicates, gates and projection derivations;
- strict arbitrary-byte parsing rejects invalid UTF-8, malformed JSON,
  duplicate/missing/unknown fields, invalid identifiers or digests, protected
  digest mismatch and non-canonical bytes;
- the four verdict exit mapping is centralized as `0/1/2/3`, with `64`
  reserved for invalid invocation;
- SDK capabilities advertise the manifest type and schema while constructive
  production emission remains disabled;
- `build-tools/test-cm1-contract.sh` protects the published JSON Schema's
  non-empty and projection-state invariants in CI.

Verification evidence:

- CM0 dependency/claim and CM1 schema gates: passed;
- canonical golden vector and round-trip tests: passed;
- arbitrary-byte parser totality property: passed;
- empty manifest, empty gates, incomplete gates and candidate false-pass
  witnesses: rejected or `Inconclusive` as required;
- policy and source mutations changed the protected digest;
- pinned .NET 10 evaluator image: compiled successfully;
- hardened evaluator container gates: passed;
- locked CanonFlow/ONDCFlow integration: 81 passed, 0 failed across ONDCFlow,
  assurance, XP, core-law and PostgreSQL integration suites.

CM1 adds no synthesizer, no regulated vertical rule and no production
constructive projection.

### CM2 — First constructive experiment

Objective: prove one undeniable row-local slice in a laboratory profile.

Admitted candidate:

```sql
CHECK (email IS NOT NULL OR phone IS NOT NULL)
```

RED witnesses:

- both fields absent is accepted;
- one valid state cannot be represented;
- `decode (encode x)` changes a domain value;
- an opaque parser node is treated as exact.

Deliverables:

- row-level Canon IR with referenced-column set;
- exact recognizer for the required-OR pattern;
- `Contact` DU with `EmailOnly`, `PhoneOnly` and `Both`;
- boundary DTO;
- total `encode` and `decode`;
- valid and invalid FsCheck generators;
- four-row PostgreSQL truth table;
- positive, negative and hostile mutation corpus;
- experimental obligation manifest and fidelity report.

Exit gates:

- every truth-table row agrees with PostgreSQL;
- `decode (encode x) = Ok x`;
- parser uncertainty is `Inconclusive`, never exact;
- generated output is deterministic;
- the complete integrated gate suite passes twice from a clean clone.

Business result: CFF demonstrates the capability without changing its primary
business.

#### CM2 implementation record — 2026-07-28

Status: **Complete**

Implemented in CanonFlow:

- `Canon.Core.RowConstraint` carries the row predicate, exact referenced-column
  set and an explicit opaque-node flag;
- the SQL parser now preserves structural `IS NULL` and `IS NOT NULL` nodes and
  exposes `parseRowConstraint`; all unparsed syntax remains opaque;
- the experimental PostgreSQL laboratory profile recognizes only a two-column
  `IS NOT NULL OR IS NOT NULL` row predicate with a matching reference set;
- opaque parser input returns `Inconclusive`, while structurally different
  known predicates are `Unsupported`;
- the `Contact` DU contains `EmailOnly`, `PhoneOnly` and `Both`, backed by a
  non-null text wrapper; empty and whitespace strings remain valid because the
  PostgreSQL obligation constrains nullability, not content;
- the boundary `ContactDto` has total `encode` and `decode` mappings;
- valid and invalid FsCheck generators cover all three admitted states,
  empty/whitespace values, the excluded absent state and hostile CLR-null
  options;
- deterministic F# emission is protected by a golden SHA-256 vector and the
  committed generated artifact is compiled by the law suite;
- a four-row Testcontainers PostgreSQL oracle compares database insertion
  acceptance with `Contact.decode`;
- positive, negative and hostile parser/mutation vectors are committed in
  `examples/required-contact-lab-corpus.json`;
- the source fragment, generated model, canonical obligation manifest and
  bounded experimental fidelity report are committed under `examples/`;
- SDK capabilities advertise `required-contact-postgres-v1-lab` as
  experimental while constructive production emission remains false;
- CI runs the complete locked CanonFlow/ONDCFlow integration suite twice with a
  clean build between runs.

Verification evidence:

- required-OR row IR reported exactly `email` and `phone`;
- all positive forms were recognized, known negative mutations were
  unsupported and opaque/hostile forms were `Inconclusive`;
- `decode (encode x) = Ok x` passed 200 generated cases;
- every invalid generator sample was rejected;
- all four presence combinations agreed with live PostgreSQL;
- generated source, obligation manifest and fidelity report matched committed
  artifacts byte-for-byte;
- CM0 dependency/claim and CM1 schema gates remained green;
- the pinned .NET 10 evaluator image compiled successfully;
- hardened evaluator container gates passed with production construction still
  disabled;
- the locked integrated suite passed twice after an intervening clean: 89 tests
  per run, 178 passing test executions total;
- CI is configured to repeat the same twice-clean gate from a fresh checkout.

CM2 remains a row-local PostgreSQL laboratory profile. It adds no ONDC, GST,
EDI or EPCIS semantics, awards no regulatory authority and does not activate
constructive production emission.

### CM3 — FsAssay obligation preservation

Objective: make FsAssay the structural preservation gate, not the semantic
oracle.

RED witnesses:

- remove a required DU case;
- add a wildcard that hides a new case;
- expose a constructor that permits bypass;
- remove or change `encode`/`decode`;
- edit generated code without changing the manifest;
- suppress a required finding without an audit record.

Deliverables:

- obligation-aware FsAssay profile or separately admitted plugin;
- manifest-to-TAST symbol resolution;
- required type/case/payload and mapping checks;
- source/generated/manifest digest checks;
- wildcard and suppression reporting for obligated types;
- positive and negative behavioral specimens for every new blocking rule;
- production-admission record for the obligation rules;
- replacement or honest renaming of CanonFlow's lightweight lexical build gate.

Exit gates:

- every hostile mutation triggers the expected admitted finding;
- comments and identifier aliases do not change detection;
- compiler failure or missing TAST yields `Inconclusive`;
- FsAssay never claims the generated mapping agrees with the business oracle;
- plugin load failure yields `ToolFailure`.

Business result: generated models remain trustworthy as teams and AI agents
modify downstream code.

#### CM3 implementation record — 2026-07-28

Status: **Complete**

Implemented across CanonFlow and FsAssay:

- `FsAssay.CanonFlow.Plugin` is a separately versioned and admitted
  `FSharp.Analyzers.SDK` plugin; it does not add CanonFlow business meaning to
  FsAssay's general rule catalogue;
- the required-contact profile binds its CanonFlow obligation-manifest entry to
  compiler-resolved F# entities, union cases, payload types, the private
  `ContactText` representation and the typed signatures of `encode` and
  `decode`;
- `CFF-OBL001` through `CFF-OBL005` are blocking findings for evidence-binding
  drift, type/case/payload or constructor weakening, mapping loss, wildcard
  introduction and unaudited suppression;
- `CFF-OBL006` is non-blocking missing typed-tree evidence and therefore
  contributes `Inconclusive`, never `Pass`;
- source, canonical obligation manifest, generated F#, suppression audit and
  the preservation profile are SHA-256 bound; generated edits cannot inherit
  the prior admission silently;
- suppression approvals, when present, must name the exact rule and generated
  artifact digest, contain a reason and approver, and be unexpired;
- the generated laboratory decoder now enumerates every result pair rather
  than hiding future cases behind a wildcard;
- the former `build-tools/fsassay.fsx` scanner is now honestly named
  `canonflow-lexical-guard.fsx`, with `CFLEX` findings. It remains a lightweight
  build guard and is no longer represented as FsAssay;
- FsAssay's plugin loader now treats an explicitly requested missing assembly
  as a load failure instead of silently loading zero analyzers;
- the production-admission record pins the profile and toolchain while stating
  `semanticOracle: false`, `businessOracleAgreement: false` and regulatory
  authority `none`;
- evaluator CI checks out FsAssay and runs
  `build-tools/test-cm3-obligation.sh` before the cumulative CanonFlow/ONDCFlow
  integration gate.

Verification evidence:

- the complete FsAssay suite passed: 50 tests, 0 failed;
- the admitted positive model and a comment/local-alias variant produced no
  CanonFlow obligation finding;
- removing a required case, exposing the wrapper representation, removing a
  mapping, changing generated mapping code, editing generated code without
  profile/manifest admission, adding a wildcard and adding an unaudited
  suppression each produced its expected admitted finding;
- compiler failure produced `Inconclusive`/exit 2;
- an explicitly requested missing plugin produced `ToolFailure`/exit 3;
- the preservation profile and admission record mechanically reject any claim
  that the plugin proves agreement with PostgreSQL or another business oracle.

CM3 preserves only the admitted generated structure and evidence bindings. It
does not activate constructive production emission and does not add ONDC, GST,
EDI or EPCIS authority.

### CM4 — Evidence and receipt integration

Objective: combine verification and constructive evidence without conflating
them.

RED witnesses:

- a signed receipt with failed constructive gates is promoted;
- missing constructive evidence is reported as verified constructive success;
- removing an earlier gate improves the verdict.

Deliverables:

- separate verification and constructive assessment components;
- canonical evidence references and digests;
- cumulative gate aggregation;
- explicit missing-evidence reporting;
- seal/verdict orthogonality;
- SDK fields exposing projection state, derivation and source digest.

Exit gates:

- sealing a `Fail` or `Inconclusive` result never changes its verdict;
- no missing-evidence path returns exit zero;
- removing required evidence cannot improve promotability;
- receipt verification succeeds offline.

Business result: constructive capability becomes auditable and usable in
enterprise governance.

#### CM4 implementation record — 2026-07-28

Status: **Complete**

Implemented in CanonFlow:

- receipt schema `1.1` keeps ordinary verification assessments and constructive
  assessments in separate canonical arrays; neither is presented as the other;
- constructive assessments bind the obligation, projection state, structured
  derivation, authoritative source digest and exact obligation-manifest digest;
- every required proof gate records its version, implementation digest, verdict
  and canonical evidence references with artifact digests and provenance;
- missing or evidence-free required gates are listed explicitly and reduce the
  constructive verdict to `Inconclusive`; they can never produce exit zero;
- repeated observations of one gate are cumulative: evidence is unioned and the
  worst observed verdict is retained;
- promotability requires an admitted projection, `Pass`, a non-empty required
  gate set, complete evaluated-gate coverage and no missing gate identifiers;
- receipt verification independently recomputes gate counts, missing-evidence
  state, cumulative constructive verdict, top-level verdict coherence and
  canonical signed bytes;
- signing is orthogonal to assessment: correctly sealed `Fail` and
  `Inconclusive` receipts retain their verdicts, while a valid signature cannot
  legalize a dishonest `Pass`;
- the evaluator profile `required-contact-constructive-v1` validates that the
  obligation manifest, evidence bundle and every referenced artifact are
  declared, root-contained and digest-bound before assessment;
- JSON, Markdown and HTML outputs expose constructive evidence gaps separately
  from verification findings;
- SDK capabilities expose the projection state, derivation/admission,
  obligation identifier, source digest field and receipt profile;
- the released `CanonFlowEvidenceReceipt` 1.0 CLR record shape remains intact
  for binary compatibility with existing ONDCFlow/profile assemblies; receipt
  `1.1` uses a separate internal model rather than mutating that public F#
  constructor.

RED and integration evidence:

- a signed receipt containing a failed constructive gate cannot be promoted;
- an absent constructive bundle produces four explicit missing gate IDs,
  `Inconclusive` and exit 2;
- removing a failed required observation changes `Fail` only to
  non-promotable `Inconclusive`, never to promotable success;
- duplicate Pass/Fail observations retain `Fail` and both evidence references;
- signed constructive Pass, Fail and missing-evidence receipts all verify
  offline with the pinned public key;
- the shipped non-root, read-only, network-disabled container exercises all
  three constructive paths and the prebuilt ONDCFlow profile.

Verification evidence:

- full CanonFlow build: 0 warnings and 0 errors;
- cumulative CanonFlow suite: 77 passed, 0 failed across assurance, XP, core
  laws and integration tests;
- cumulative FsAssay suite: 50 passed, 0 failed, including every admitted CM3
  hostile preservation specimen;
- CM0 dependency/claim and CM1 obligation-schema gates remained green;
- the canonical manifest -> FsAssay preservation profile -> admission digest
  chain was rebound and verified after removal of a non-canonical trailing
  newline;
- hardened evaluator container gates passed, including deterministic legacy
  receipts, all four verification verdict paths, constructive Pass/Fail/missing
  paths, offline verification and ONDC binary compatibility.

CM4 does not activate production constructive emission, award regulatory
authority or admit a vertical pilot. CM5 remains demand-pulled.

### CM5 — One pulled vertical pilot

Objective: apply the capability to exactly one real regulated flow.

Selection is based on demand, not preference:

| Vertical | Candidate examples |
|---|---|
| ONDCFlow | admitted lifecycle state, identifier consistency or signed-message state |
| GSTFlow | document status with conditionally required fields |
| EDIFlow | transaction-set/segment alternatives |
| EPCIS | event type with required payload relationship |

Rules:

- choose one vertical and one obligation;
- do not generalize from schema shape alone;
- do not start a second vertical during the pilot;
- preserve all existing vertical tests and receipts.

Exit gates:

- a design partner can identify the avoided defect or reduced integration work;
- the authoritative source and expected outcomes are reviewed;
- the generated model prevents at least one observed invalid state;
- verification remains available without consuming the generated model;
- upgrade/drift behavior is demonstrated on one source change.

Business result: evidence of willingness to adopt, not merely technical novelty.

### CM6 — Second-pattern decision gate

Objective: decide whether constructive modelling should expand or return to
dormancy.

Expand only when:

- CM5 produced measurable customer value;
- false-positive and false-negative review found no blocking defect;
- support and upgrade effort decreased;
- another vertical or customer requests a related pattern;
- accumulated gates remain affordable.

Candidate second patterns:

- closed `IN` set to DU;
- discriminator plus conditionally required payload;
- non-empty collection;
- ordered interval, only with explicit normalization and time semantics.

Otherwise:

- freeze the experimental profile;
- retain manifests and evidence;
- continue Mode V;
- record the stop decision as a successful Lean outcome.

Business result: capital follows validated demand rather than roadmap momentum.

### CM7 — Productization

Objective: package proven constructive capability without changing CFF's core
identity.

Possible products:

- verification SDK and CI gate;
- maintained regulated profile and source-update feed;
- constructive model package for admitted patterns;
- obligation drift and consumer-impact report;
- enterprise policy/suppression governance;
- private trace and mutation-corpus evaluation;
- sealed evidence receipts.

Exit gates:

- package and image provenance are published;
- upgrades expose source, model and consumer drift;
- all claims identify the admitted pattern and evidence boundary;
- official certification language is used only with written authority.

Business result: shared infrastructure supports multiple regulated verticals
while revenue remains grounded in verification, updates and evidence.

## 11. Business measurements

Do not optimize for number of generated types. Measure:

- time to first verified transaction/document/event;
- invalid states prevented before runtime;
- integration defects escaping to staging or production;
- source-upgrade effort and compiler-located consumers;
- false-positive, false-negative and inconclusive rates;
- support incidents attributable to rule misunderstanding;
- CI/runtime overhead;
- design-partner adoption and renewal;
- percentage of receipts independently verified;
- number of vertical-specific components reused without semantic coupling.

## 12. Stop conditions

Constructive work returns to dormancy when:

- no design partner or observed defect justifies the next pattern;
- authoritative source meaning is ambiguous;
- exactness requires guessed business semantics;
- the oracle cannot be run;
- mutation tests expose an unresolved false pass;
- cumulative gates become unaffordable relative to customer value;
- a vertical would need to be rewritten around generated abstractions;
- constructive work delays a higher-value verification requirement.

No stop condition weakens Mode V.

## 13. Definition of success

CFF succeeds even if CM6 decides not to expand.

The strategy is successful when:

1. verification remains the permanent product and trust boundary;
2. constructive modelling can remain dormant at zero operational cost;
3. one admitted pattern can be activated without rewriting a vertical;
4. FsAssay can detect structural weakening without pretending to prove business
   semantics;
5. oracle evidence can establish bounded agreement;
6. receipts distinguish verification, construction, evidence gaps and authority;
7. future verticals reuse infrastructure while retaining ownership of their
   regulatory meaning.

## 14. Exact next move

CM0 through CM2 are complete.

Pause at the admission boundary. Review the CM2 laboratory evidence and decide
explicitly whether maintaining this generated model justifies starting **CM3
FsAssay obligation preservation**. CM3 is not admitted automatically, and
constructive production emission remains disabled.
