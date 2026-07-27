# CanonFlow Evaluator — .NET 10 Implementation Specification

## CanonFlow × FsAssay × ONDCFlow

**Status:** Implementation-ready MVP specification

**Runtime:** .NET 10 only

**License intent:** Apache-2.0

**Primary interface:** Offline CLI in a hardened OCI container

**Primary artifact:** Canonical CanonFlow Evidence Receipt (`assessment.cff`)

**Database:** None in the base evaluator

**Network:** Forbidden during normal assessment
**ONDC status:** Profile implementation blocked until source-lock admission

---

## 0. Executive Decision

The evaluator shall deliver:

```text
One .NET 10 executable
+ one immutable OCI image
+ one evaluation manifest
+ one honest four-state verdict
+ one canonical evidence receipt
+ one offline receipt verifier
```

The evaluator shall not initially deliver:

```text
an ONDC application
a PostgreSQL server in the base image
a Node.js or Python runtime
a browser/WASM evaluator
a PDF renderer
a self-awarded compliance certificate
a Gold/Silver/Bronze scoring system
```

The external promise is:

> Docker is the only host prerequisite. The evaluator does not require a local
> .NET SDK, F# tooling, Node.js, Python, PostgreSQL, or network access.

“Zero setup” must not be used literally because Docker itself is a prerequisite.

---

## 1. Normative Language

The terms **MUST**, **MUST NOT**, **SHOULD**, **SHOULD NOT**, and **MAY** are
normative.

The implementation is acceptable only when every MVP **MUST** has executable
evidence in CI.

Documentation, comments, screenshots, and previously observed local test output
are not substitutes for executable evidence.

---

## 2. Non-Negotiable Laws

### 2.1 Runtime law

\[
\forall p \in \mathit{ProductionProjects},\quad
\operatorname{TargetFramework}(p)=\texttt{net10.0}
\]

The repository MUST contain an exact .NET 10 SDK pin in `global.json`.

Initial pin:

```json
{
  "sdk": {
    "version": "10.0.110",
    "rollForward": "disable",
    "allowPrerelease": false
  }
}
```

The pin MAY be advanced through a separately reviewed dependency change.

The build MUST reject:

- a production project targeting `net8.0` or `net9.0`;
- an evaluator image based on a .NET 8 or .NET 9 image;
- silent major-version roll-forward;
- preview SDKs in a release build.

### 2.2 Absence-of-evidence law

\[
\neg \operatorname{Evidence}(P)
\not\Rightarrow
\operatorname{Conformant}(P)
\]

No missing, skipped, crashed, timed-out, unsupported, or unexecuted assessor may
be represented as Pass.

### 2.3 Empty-assessment law

\[
\operatorname{aggregate}(\varnothing)=\mathrm{Inconclusive}
\]

An empty applicable rule set, missing rule pack, or missing assessment plan
MUST NOT produce Pass.

### 2.4 Verdict precedence law

Let:

\[
V=
\{
\mathrm{Pass},
\mathrm{Inconclusive},
\mathrm{Fail},
\mathrm{ToolFailure}
\}
\]

with severity:

\[
\operatorname{rank}(\mathrm{Pass})=0,\quad
\operatorname{rank}(\mathrm{Inconclusive})=1,\quad
\operatorname{rank}(\mathrm{Fail})=2,\quad
\operatorname{rank}(\mathrm{ToolFailure})=3
\]

Then:

\[
a\sqcup b=
\arg\max_{x\in\{a,b\}}\operatorname{rank}(x)
\]

The implementation MUST use an explicit rank function. It MUST NOT depend on F#
discriminated-union declaration order.

All component results MUST remain in the receipt even when a more severe
aggregate verdict dominates them.

### 2.5 Sealing orthogonality law

\[
\operatorname{Seal} \perp \operatorname{Verdict}
\]

Pass, Fail, Inconclusive, and ToolFailure assessments MAY all be sealed.

A seal proves receipt integrity and signer identity. It does not prove that the
assessment passed.

### 2.6 Determinism law

Given identical:

- input bytes;
- manifest;
- evaluation instant;
- toolchain;
- profile and rule-pack digests;
- source-lock digest;
- resource budget;

the canonical receipt payload MUST be byte-identical:

\[
\operatorname{evaluate}(I,C,T,P,B)
=
\operatorname{evaluate}(I,C,T,P,B)
\]

No ambient time, locale, hostname, random identifier, file enumeration order,
network response, or absolute host path may influence the canonical payload.

### 2.7 Ownership law

```text
CanonFlow.Assurance
    owns generic verdict, evidence, assessment, canonical hashing,
    replay identity, receipt construction and receipt verification.

FsAssay
    owns F# engineering policy, static findings and release admission.

CanonFlow.Profile.ONDC
    owns ONDC parsing, typed traces, lifecycle evaluation, ONDC source
    mappings and the admitted ONDC rule pack.

CanonFlow.Evaluator
    orchestrates components without redefining their semantics.
```

Dependency direction:

\[
\texttt{CanonFlow.Assurance}
\leftarrow
\texttt{CanonFlow.Reports}
\leftarrow
\texttt{CanonFlow.Cli}
\]

\[
\texttt{CanonFlow.Assurance}
\leftarrow
\texttt{CanonFlow.Profile.ONDC}
\]

`CanonFlow.Assurance` MUST NOT reference FsAssay, ONDCFlow, reporting, Docker,
CLI, PostgreSQL, or XP concepts.

---

## 3. MVP Scope

### 3.1 Included

1. CanonFlow schema-fidelity assessment.
2. FsAssay static F# assessment through the real runner.
3. Typed component aggregation.
4. JSON, SARIF, HTML, and Markdown report views.
5. Canonical `.cff` receipt construction.
6. Optional Ed25519 receipt sealing.
7. Offline receipt verification.
8. Hardened .NET 10 OCI image.
9. Air-gap distribution bundle.
10. Self-test and deterministic replay commands.

### 3.2 Deferred

1. PostgreSQL differential testing, except as a separate development profile.
2. ONDC protocol evaluation until the source-lock gate passes.
3. WASM and TypeScript SDK.
4. PDF generation.
5. Hosted verification service.
6. Badges, regulatory claims, and certification levels.
7. Native AOT until reflection, crypto, FCS, and report generation are proven
   compatible.

---

## 4. Repository and Project Layout

The preferred layout is:

```text
CanonFlow/
├── global.json
├── Directory.Build.props
├── Directory.Packages.props
├── NuGet.config
├── packages.lock.json
├── CanonFlow.slnx
│
├── src/
│   ├── Canon.Core/                       Existing semantic compiler
│   ├── CanonFlow.Assurance/
│   │   ├── NonEmpty.fs
│   │   ├── Verdict.fs
│   │   ├── Evidence.fs
│   │   ├── Assessment.fs
│   │   ├── EvaluationContext.fs
│   │   ├── Digest.fs
│   │   ├── CanonicalReceiptJson.fs
│   │   ├── Receipt.fs
│   │   ├── Seal.fs
│   │   └── Verification.fs
│   │
│   ├── CanonFlow.Reports/
│   │   ├── JsonReport.fs
│   │   ├── SarifReport.fs
│   │   ├── MarkdownReport.fs
│   │   └── HtmlReport.fs
│   │
│   ├── CanonFlow.Evaluator/
│   │   ├── Manifest.fs
│   │   ├── Budget.fs
│   │   ├── Component.fs
│   │   ├── CanonFlowComponent.fs
│   │   ├── FsAssayComponent.fs
│   │   ├── OndcComponent.fs
│   │   ├── Aggregate.fs
│   │   └── Pipeline.fs
│   │
│   ├── CanonFlow.Profile.ONDC/
│   │   ├── SourceLock.fs
│   │   ├── EvidenceBundle.fs
│   │   ├── OndcIR.fs
│   │   ├── Trace.fs
│   │   ├── Evaluator.fs
│   │   └── RulePack.fs
│   │
│   └── Canon.Cli/
│       ├── EvaluateCommand.fs
│       ├── ReceiptCommand.fs
│       ├── SelfTestCommand.fs
│       ├── VersionCommand.fs
│       └── Program.fs
│
├── tests/
│   ├── CanonFlow.Assurance.Tests/
│   ├── CanonFlow.Reports.Tests/
│   ├── CanonFlow.Evaluator.Tests/
│   ├── CanonFlow.Profile.ONDC.Tests/
│   ├── Determinism.Tests/
│   ├── Container.Tests/
│   └── Fixtures/
│       ├── passing/
│       ├── failing/
│       ├── inconclusive/
│       ├── tool-failure/
│       └── fsassay-negative-sentinel/
│
├── profiles/
│   ├── schema-fidelity-v1/
│   ├── fsassay-production-v1/
│   └── ondc-retail-locked-v1/            Absent until source-lock admission
│
├── eng/
│   ├── evaluator/
│   │   ├── Dockerfile
│   │   ├── compose.differential.yml
│   │   └── README.md
│   ├── airgap/
│   └── scripts/
│
└── docs/
    ├── evaluator/
    ├── receipt-schema/
    └── threat-model/
```

The implementation MUST NOT introduce `CanonFlow.Sdk.Core`. Generic assurance
types belong in `CanonFlow.Assurance`.

Docker definitions MUST NOT be published as a NuGet library.

---

## 5. Core F# Contracts

### 5.1 Non-empty evidence

```fsharp
type NonEmpty<'T> = private NonEmpty of head: 'T * tail: 'T list

module NonEmpty =
    val create : head: 'T -> tail: 'T list -> NonEmpty<'T>
    val ofList : 'T list -> Result<NonEmpty<'T>, EmptySequence>
    val toList : NonEmpty<'T> -> 'T list
```

### 5.2 Evidence health

```fsharp
type EvidenceGap =
    { Code: string
      Description: string
      RequiredBy: string
      ExpectedLocation: string option }

type ToolError =
    { ToolId: string
      ErrorCode: string
      Message: string
      ExitCode: int option
      DiagnosticDigest: Digest option }

type EvidenceHealth =
    | Complete
    | Partial of NonEmpty<EvidenceGap>
    | Broken of ToolError
```

### 5.3 Compliance

```fsharp
type Finding =
    { RuleId: string
      Severity: Severity
      Message: string
      EvidenceRefs: EvidenceRef list
      Remediation: string option }

type Compliance =
    | Conformant
    | NonConformant of NonEmpty<Finding>
    | NotEstablished
```

### 5.4 Public verdict

```fsharp
type Verdict =
    | Pass
    | Fail
    | Inconclusive
    | ToolFailure
```

The detailed findings, gaps, and errors belong in the assessment record rather
than inside the four-state verdict value.

### 5.5 Component assessment

```fsharp
type ComponentAssessment =
    { ComponentId: string
      ComponentVersion: string
      Health: EvidenceHealth
      Compliance: Compliance
      ApplicableRules: int
      EvaluatedRules: int
      Evidence: EvidenceRef list
      Duration: TimeSpan option }
```

`Duration` is operational metadata and MUST NOT enter the canonical assessment
digest.

### 5.6 Verdict derivation

```fsharp
let verdictOf assessment =
    match assessment.Health, assessment.Compliance with
    | Broken _, _ -> ToolFailure
    | _, NonConformant _ -> Fail
    | Complete, Conformant when assessment.ApplicableRules > 0 -> Pass
    | _ -> Inconclusive
```

The implementation MUST make illegal states difficult or impossible:

- `NonConformant` requires non-empty findings.
- `Partial` requires non-empty named gaps.
- `Broken` requires structured tool-error evidence.
- Pass requires at least one applicable and evaluated rule.

---

## 6. Evaluation Context and Time

```fsharp
type TimeProvenance =
    | Declared
    | SignedCapture of Digest
    | TrustedTimestamp of authority: string

type EvaluationInstant = private EvaluationInstant of DateTimeOffset

type EvaluationContext =
    { Instant: EvaluationInstant
      TimeProvenance: TimeProvenance
      Locale: string
      NetworkPolicy: NetworkPolicy
      Budget: EvaluationBudget }
```

Rules:

1. `CanonFlow.Assurance` MUST NOT call `DateTime.UtcNow`,
   `DateTimeOffset.UtcNow`, `TimeProvider.System`, or equivalent ambient clocks.
2. The CLI MAY obtain a time only at the boundary and MUST record its provenance.
3. Replay accepts the original declared instant.
4. Crypto and certificate-validation adapters MUST accept the evaluation instant
   explicitly.
5. Operational log timestamps MUST NOT enter the canonical receipt payload.

Required test:

```text
Run the same assessment under system clocks separated by ten years.
With the same declared EvaluationContext, assessment.cff must be byte-identical.
```

---

## 7. Evaluation Manifest

The CLI MUST accept one versioned manifest:

```json
{
  "schemaVersion": "1.0",
  "subject": {
    "root": "/input",
    "schema": "db/init.sql",
    "sourceDirectories": ["src"]
  },
  "assessors": [
    {
      "id": "canonflow-schema",
      "profileId": "schema-fidelity-v1",
      "required": true
    },
    {
      "id": "fsassay",
      "profileId": "fsassay-production-v1",
      "required": true
    }
  ],
  "evaluationContext": {
    "instant": "2026-07-27T10:30:00Z",
    "timeProvenance": "Declared",
    "network": "Forbidden",
    "locale": "invariant"
  },
  "budget": {
    "maxFiles": 10000,
    "maxInputBytes": 104857600,
    "maxJsonDepth": 128,
    "componentTimeoutSeconds": 120,
    "totalTimeoutSeconds": 300
  },
  "output": {
    "formats": ["cff", "json", "sarif", "html", "markdown"],
    "seal": {
      "mode": "Optional",
      "keyPath": "/keys/evaluator.key"
    }
  }
}
```

Rules:

1. All paths MUST resolve beneath `/input`.
2. Symlink traversal outside `/input` MUST be rejected.
3. Unknown manifest fields MUST be rejected unless schema evolution explicitly
   permits them.
4. Profile aliases such as `production` MUST NOT silently resolve to mutable
   content. The receipt records the exact profile digest.
5. Environment variables MAY configure operational concerns, but MUST NOT
   silently change assessment semantics.

---

## 8. Component Interface

```fsharp
type ComponentRequest =
    { Context: EvaluationContext
      Subject: SubjectManifest
      Profile: ProfileSnapshot
      OutputDirectory: ValidatedPath }

type EvaluatorComponent =
    { Id: string
      Version: string
      Assess:
          ComponentRequest ->
          Async<ComponentAssessment> }
```

The orchestrator MUST:

1. resolve and hash the manifest;
2. resolve exact profile snapshots;
3. hash all admitted subject artifacts;
4. execute required components within budgets;
5. classify process exits without losing diagnostics;
6. aggregate component verdicts;
7. construct the canonical receipt;
8. optionally seal the receipt;
9. derive human and machine report views;
10. return the canonical process exit code.

The base evaluator MUST NOT compile or execute the assessed project.

FsAssay static analysis may parse project files and F# syntax. It MUST NOT run
arbitrary build targets from the assessed repository.

---

## 9. CLI Contract

### 9.1 Commands

```bash
canonflow evaluate \
  --manifest /input/canonflow-evaluation.json \
  --output /output
```

```bash
canonflow receipt verify \
  --receipt /input/assessment.cff \
  --public-key /input/evaluator.pub \
  --offline
```

```bash
canonflow replay \
  --receipt /input/assessment.cff \
  --input /input \
  --output /output/replay
```

```bash
canonflow self-test --output /output/self-test.json
```

```bash
canonflow version --json
```

### 9.2 Exit codes

| Exit | Meaning |
|---:|---|
| `0` | Pass |
| `1` | Fail |
| `2` | Inconclusive |
| `3` | ToolFailure |
| `64` | Invalid command, manifest, profile, or invocation |

Every subcommand MUST have an explicit exit-code table.

`receipt verify` has:

| Exit | Meaning |
|---:|---|
| `0` | Receipt digest and signature valid |
| `1` | Receipt invalid or tampered |
| `3` | Verifier/tool failure |
| `64` | Invalid invocation |

`receipt verify` MUST NOT return Inconclusive.

---

## 10. FsAssay Integration

### 10.1 Required integration

FsAssay MUST be invoked through its real runner or programmatic API.

The evaluator MUST NOT represent a substring-scanning bootstrap script as
FsAssay.

```fsharp
type FsAssayInvocation =
    { InputRoots: ValidatedPath list
      ProfileSnapshot: ProfileSnapshot
      SarifOutput: ValidatedPath
      JsonOutput: ValidatedPath
      Timeout: TimeSpan }
```

### 10.2 Required sentinel

CI MUST contain a deliberately violating F# fixture:

```fsharp
module Sentinel

let mutable forbiddenState = 0
```

The sentinel gate passes only when:

1. FsAssay is proven to have executed in that CI run;
2. the expected rule ID is emitted;
3. the expected file and source range are emitted;
4. the runner exits with the expected non-zero code;
5. removing or bypassing the runner makes CI fail.

`Assert.True(true)` is forbidden as an execution sentinel.

### 10.3 Policy integrity

The receipt MUST include:

- FsAssay version;
- profile ID and digest;
- rule count;
- rules executed;
- rules skipped with reasons;
- runner exit code;
- SARIF digest;
- JSON-result digest.

Agent-authored code MUST NOT be allowed to weaken the admitted FsAssay profile
inside the same work item.

Ignore directives MUST be typed, scoped, reviewable, expiring where applicable,
and included in the evidence receipt. A free-form line containing
`FsAssay-Ignore` is not acceptable.

---

## 11. ONDCFlow Integration

### 11.1 Hard source-lock gate

No ONDC protocol rule implementation may be admitted until the repository
contains:

```text
source.lock.json
├── profile identity
├── admitted source documents
├── exact versions and effective dates
├── source hierarchy
├── conflict decisions
├── source-document digests
├── reviewer identity
└── source-lock digest/signature
```

If the source lock is absent, invalid, conflicting, or incomplete:

```text
Health      = Partial
Compliance  = NotEstablished
Verdict     = Inconclusive
```

### 11.2 Bounded MVP profile

The first admitted profile MUST be one pinned retail order-formation slice:

```text
/search
→ /on_search
→ /select
→ /on_select
→ /init
→ /on_init
→ /confirm
→ /on_confirm
```

The MVP MUST NOT claim universal ONDC compliance.

### 11.3 Evidence-bundle input

ONDC assessment consumes captured evidence, not only source code:

```text
ondc-evidence/
├── manifest.json
├── messages/
├── transport/
│   ├── headers.json
│   └── observations.json
├── replay/
│   └── observations.json
├── registry/
│   └── signed-snapshot.json
├── source.lock.json
└── profile.pack.json
```

Static F# source, when supplied, is assessed separately by FsAssay.

The following cannot be established from source scanning alone:

- runtime idempotency;
- cross-message ordering;
- quote consistency;
- nonce replay behaviour;
- registry status;
- runtime digital signatures;
- settlement observations.

Missing required runtime evidence yields Inconclusive unless an independent
violation is already proven.

### 11.4 Rule-pack safety

Signed rule packs contain data, not executable F# or scripts.

Identifier grammars MUST use bounded constructors and private smart
constructors. Arbitrary backtracking regular expressions are forbidden.

All evaluators MUST enforce:

- maximum input length;
- maximum grammar depth;
- maximum state count;
- deterministic evaluation budget;
- explicit timeout classification.

### 11.5 Claims boundary

Allowed:

```text
CanonFlow Conformance Assessment
CanonFlow Evidence Receipt
Assessed against profile <id>@<digest>
```

Forbidden without written authority:

```text
ONDC Certified
ONDC Approved
Official ONDC Compliance Certificate
CanonFlow Foundation guarantees compliance
```

---

## 12. Canonical Receipt

### 12.1 Files

```text
report/
├── assessment.cff
├── VERDICT.json
├── REPORT.html
├── EVIDENCE.md
├── LOSS.md
├── findings.sarif
└── sbom.spdx.json
```

`assessment.cff` is authoritative. Other files are derived views.

### 12.2 Receipt payload

```json
{
  "schemaVersion": "1.0",
  "receiptType": "CanonFlowEvidenceReceipt",
  "assessmentId": "sha256:...",
  "subject": {
    "manifestDigest": "sha256:...",
    "artifacts": [
      {
        "path": "db/init.sql",
        "digest": "sha256:..."
      }
    ]
  },
  "evaluationContext": {
    "instant": "2026-07-27T10:30:00Z",
    "timeProvenance": "Declared",
    "network": "Forbidden",
    "locale": "invariant"
  },
  "toolchain": {
    "dotnetSdk": "10.0.110",
    "dotnetRuntime": "10.0.10",
    "imageDigest": "sha256:...",
    "canonflowVersion": "0.1.0",
    "fsassayVersion": "0.1.0",
    "ondcProfile": null
  },
  "policy": {
    "profileDigests": [],
    "rulePackDigests": [],
    "sourceLockDigest": null
  },
  "assessment": {
    "health": "Complete",
    "compliance": "NonConformant",
    "verdict": "Fail",
    "exitCode": 1
  },
  "components": [],
  "seal": {
    "status": "Signed",
    "algorithm": "Ed25519",
    "keyId": "local:evaluator-1",
    "signature": "base64:..."
  }
}
```

### 12.3 Canonicalization

The implementation MUST:

1. reject duplicate JSON property names;
2. use RFC 8785/JCS property ordering by unsigned UTF-16 code units;
3. use ordinal, culture-independent comparison;
4. implement canonical number serialization or prohibit numbers from the signed
   schema;
5. reject invalid Unicode scalar sequences;
6. hash UTF-8 bytes of the canonical payload;
7. use lowercase SHA-256 hex with a normative `sha256:` prefix;
8. exclude the signature field from the signed payload;
9. include an explicit schema version;
10. verify all official and project-owned canonicalization vectors.

### 12.4 Digest immutability

Digest, key, nonce, and signature types wrapping byte arrays MUST make defensive
copies on construction and extraction.

Mutation tests MUST prove that modifying caller-owned buffers cannot modify
constructed domain values.

### 12.5 Tamper enumeration

Tests MUST:

1. parse an emitted receipt into a DOM;
2. enumerate every JSON Pointer in the emitted DOM;
3. mutate each value independently;
4. verify that each signed-field mutation invalidates the receipt;
5. prove that pointer enumeration is derived from the serialized receipt rather
   than a hand-maintained field list.

---

## 13. Sealing and Keys

1. Receipt signing keys MUST NOT be baked into the image.
2. Keys are mounted read-only at runtime.
3. ONDC protocol keys and receipt-signing keys MUST be distinct types, paths,
   key IDs, and operational roles.
4. Absence of a signing key affects seal status, not assessment truth.
5. An unsigned receipt MUST say `Unsigned`; it MUST NOT pretend to be
   tamper-evident.
6. Private keys MUST never appear in stdout, stderr, logs, HTML, SARIF, receipt,
   crash dumps, or Docker layers.
7. Verification MUST work offline with an explicitly supplied public key.

---

## 14. Docker Implementation

### 14.1 Build inputs

The image is assembled from versioned packages or project outputs produced by
their owning repositories.

The Docker build MUST NOT assume that three independently cloned repositories
exist under one unversioned build context.

Release input:

```text
evaluator-build/
├── CanonFlow source or published evaluator project
├── .nuget/offline/
│   ├── CanonFlow.Assurance.<version>.nupkg
│   ├── FSharpAssay.Runner.<version>.nupkg
│   └── CanonFlow.Profile.ONDC.<version>.nupkg   optional
├── packages.lock.json
├── NuGet.config
├── profiles/
└── Dockerfile
```

### 14.2 Dockerfile

```dockerfile
# syntax=docker/dockerfile:1

ARG SDK_IMAGE=mcr.microsoft.com/dotnet/sdk:10.0.110@sha256:<sdk-digest>
ARG RUNTIME_IMAGE=mcr.microsoft.com/dotnet/runtime:10.0.10-noble-chiseled@sha256:<runtime-digest>

FROM ${SDK_IMAGE} AS build
WORKDIR /src

COPY global.json ./
COPY Directory.Build.props ./
COPY Directory.Packages.props ./
COPY NuGet.config ./
COPY .nuget/offline/ ./.nuget/offline/
COPY src/ ./src/

RUN dotnet restore src/Canon.Cli/Canon.Cli.fsproj \
    --locked-mode

RUN dotnet publish src/Canon.Cli/Canon.Cli.fsproj \
    -c Release \
    --no-restore \
    -o /out \
    /p:ContinuousIntegrationBuild=true \
    /p:DebugType=None

FROM ${RUNTIME_IMAGE} AS runtime
WORKDIR /app

COPY --from=build /out/ ./
COPY profiles/ /profiles/

ENV DOTNET_EnableDiagnostics=0
ENV COMPlus_EnableDiagnostics=0

USER $APP_UID

ENTRYPOINT ["dotnet", "Canon.Cli.dll"]
```

The final image MUST contain no shell, SDK, compiler, package manager, Node.js,
Python, PostgreSQL, git, curl, or private signing key.

### 14.3 Hardened invocation

```bash
mkdir -p report

docker run --rm \
  --network none \
  --read-only \
  --cap-drop ALL \
  --security-opt no-new-privileges \
  --pids-limit 256 \
  --memory 2g \
  --cpus 2 \
  --tmpfs /tmp:rw,noexec,nosuid,size=128m \
  --mount type=bind,src="$PWD",dst=/input,readonly \
  --mount type=bind,src="$PWD/report",dst=/output \
  ghcr.io/canonflowfoundation/canonflow-evaluator:0.1.0@sha256:<image-digest> \
  evaluate \
  --manifest /input/canonflow-evaluation.json \
  --output /output
```

Inputs MUST be read-only. Outputs MUST use a separate writable mount.

### 14.4 Image provenance

Every release MUST publish:

- immutable OCI digest;
- SPDX or CycloneDX SBOM;
- vulnerability-scan result;
- signed image provenance;
- source commit;
- exact package lock;
- exact .NET SDK and runtime versions;
- supported platform list;
- checksum file for the air-gap bundle.

MVP platform:

```text
linux/amd64
```

`linux/arm64` is admitted only after it passes the same golden vectors and
byte-identical receipt tests.

---

## 15. Air-Gap Distribution

Release bundle:

```text
canonflow-evaluator-airgap-<version>/
├── images/
│   └── canonflow-evaluator.tar
├── profiles/
├── public-keys/
├── schemas/
│   ├── evaluation-manifest.schema.json
│   └── receipt.schema.json
├── examples/
├── sbom/
├── checksums.sha256
├── provenance.json
└── README.md
```

Usage:

```bash
sha256sum --check checksums.sha256
docker load --input images/canonflow-evaluator.tar
docker run --network none ...
```

The bundle MUST NOT contain private receipt-signing keys.

Registry evidence required by an ONDC profile must be supplied as a signed
snapshot. Missing or stale registry evidence is Inconclusive according to the
admitted profile policy.

---

## 16. Differential PostgreSQL Profile

PostgreSQL is not part of the base evaluator.

Differential testing is implemented as a separate Compose profile:

```text
evaluator container
        │
        └── isolated network
                │
                ▼
        PostgreSQL 16 sidecar
```

Rules:

1. The base image remains unchanged.
2. The database exists only for the duration of the differential assessment.
3. The exact PostgreSQL image digest is recorded.
4. Generated test data, seed, collation, timezone, extensions, and SQL settings
   are recorded.
5. Database startup failure is ToolFailure.
6. A mismatch between database behaviour and generated validation is Fail.
7. Unsupported SQL semantics are Inconclusive, not Pass.
8. The sidecar is unavailable to the public network.

This profile is deferred until the base evaluator is complete.

---

## 17. Reports

### 17.1 Machine report

`VERDICT.json` is a derived, unsigned convenience view. It MUST contain:

- aggregate health;
- aggregate compliance;
- four-state verdict;
- exit code;
- component results;
- findings;
- named missing evidence;
- tool failures;
- subject digests;
- receipt digest.

### 17.2 SARIF

SARIF is used for source-addressable schema and F# findings.

Findings without a meaningful source region MAY appear in JSON and HTML without
fabricating a file and line number.

### 17.3 HTML

`REPORT.html` MUST:

- be a single offline-readable file;
- load no remote fonts, scripts, styles, images, or analytics;
- show verdict and evidence health separately;
- show every skipped or unsupported check;
- link findings to evidence references;
- identify itself as a derived view;
- show receipt and image digests;
- avoid certification language.

### 17.4 LOSS.md

`LOSS.md` MUST name:

- unsupported constraints;
- evidence not supplied;
- assessors not run;
- approximations;
- manual obligations;
- source conflicts;
- profile limitations;
- rules outside the admitted scope.

Coverage percentage MUST NOT override the verdict.

---

## 18. Test Strategy

### 18.1 Unit tests

- smart constructors and illegal-state prevention;
- verdict table;
- explicit exit mappings;
- manifest path validation;
- profile resolution;
- digest immutability;
- canonical JSON vectors;
- seal construction and verification;
- duplicate-property rejection;
- resource-budget classification.

### 18.2 Exhaustive algebra tests

Because the verdict set has four elements, CI MUST exhaustively evaluate:

```text
4 × 4 join pairs
4 × 4 × 4 associativity triples
all empty and singleton aggregates
all EvidenceHealth × Compliance combinations
```

FsCheck MAY supplement these vectors but MUST NOT replace finite exhaustive
enumeration.

Required laws:

\[
a\sqcup a=a
\]

\[
a\sqcup b=b\sqcup a
\]

\[
(a\sqcup b)\sqcup c=a\sqcup(b\sqcup c)
\]

\[
a\sqcup\mathrm{Pass}=a
\]

### 18.3 Determinism tests

Run identical fixtures under:

- `LANG=C`;
- `LANG=tr_TR.UTF-8`;
- different host timezones;
- system clocks separated by ten years;
- shuffled file enumeration;
- shuffled JSON object construction order;
- different absolute workspace paths.

The canonical receipt bytes and digest MUST remain identical.

### 18.4 Four golden fixture classes

```text
passing/          → exit 0
failing/          → exit 1
inconclusive/     → exit 2
tool-failure/     → exit 3
```

Every release must demonstrate all four.

### 18.5 Container tests

CI MUST prove:

- network access is unavailable;
- input is read-only;
- process runs as non-root;
- root filesystem is read-only;
- no shell or package manager exists;
- resource budgets terminate hostile inputs;
- no private key is present in any layer;
- `self-test` works in the release image;
- two container runs produce identical canonical receipts.

### 18.6 FsAssay tests

- positive clean fixture;
- negative sentinel fixture;
- scanner crash;
- timeout;
- malformed profile;
- profile digest mismatch;
- protected-policy mutation;
- ignore-directive audit.

### 18.7 ONDC tests

Blocked until source-lock admission.

Once admitted:

- positive and negative golden message traces;
- missing transport headers;
- cross-message ordering;
- replay/idempotency observations;
- quote mutation;
- explicit evaluation time;
- signature vectors;
- registry snapshot missing/stale/valid;
- rule-pack tampering;
- source-lock tampering;
- bounded grammar attack inputs.

---

## 19. CI Pipeline

Required pipeline:

```text
G1  Source and license hygiene
G2  Exact .NET 10 toolchain verification
G3  Locked restore
G4  Release build
G5  Unit + exhaustive algebra tests
G6  Real FsAssay gate + negative sentinel
G7  Determinism and tamper tests
G8  OCI build
G9  Container hardening tests
G10 SBOM + vulnerability scan
G11 Image signing/provenance
G12 Air-gap bundle assembly and verification
```

No release artifact may be published unless all required gates Pass.

A tool crash in a gate is ToolFailure and blocks release.

### 19.1 Required .NET assertions

CI MUST record:

```bash
dotnet --version
dotnet --info
```

and assert:

```text
SDK     = 10.0.110
TFM     = net10.0
Runtime major = 10
```

The values are advanced only by an explicit toolchain-update change.

### 19.2 Solution completeness

CI MUST build and test the complete solution:

```bash
dotnet restore CanonFlow.slnx --locked-mode
dotnet build CanonFlow.slnx -c Release --no-restore
dotnet test CanonFlow.slnx -c Release --no-build
```

Every substantive test source file MUST appear in its `.fsproj`.

Placeholder tests such as `Assert.True(true)` MUST fail repository policy.

---

## 20. Security Threat Model

The evaluator processes potentially hostile files.

Threats:

| Threat | Required control |
|---|---|
| Path traversal | Validated root-relative paths |
| Symlink escape | Resolve and reject outside-root targets |
| Input bomb | File, byte, depth, time and memory budgets |
| ReDoS | Bounded grammar/non-backtracking policy |
| Malicious MSBuild | Never build assessed projects |
| Network exfiltration | `--network none` |
| Source mutation | Read-only input mount |
| Container breakout | Non-root, no capabilities, read-only root |
| Policy weakening | Pinned profile digest and protected changes |
| Tool substitution | Image/package digests and provenance |
| Receipt tampering | Canonical digest and optional Ed25519 seal |
| Secret leakage | No embedded keys; output canary tests |
| Ambient nondeterminism | Explicit context and deterministic ordering |

The threat model MUST be versioned and included in release review.

---

## 21. Implementation Milestones

Milestones are dependency gates, not calendar promises.

### M0 — Repository truth

Deliver:

- full canonical Apache-2.0 license;
- clean repository without committed dependency directories;
- all current CI workflows green on .NET 10;
- all substantive tests included;
- removal of placeholder tests;
- removal of mock cryptographic outputs;
- one documented ownership ADR.

Acceptance:

```text
CanonFlow.slnx builds and tests on a clean .NET 10 machine.
No current workflow is red.
```

### M1 — Generic assurance kernel

Deliver:

- `NonEmpty`;
- evidence health;
- compliance;
- four-state verdict;
- explicit reducer;
- immutable digest;
- evaluation context;
- exhaustive algebra tests.

Acceptance:

```text
All illegal-state, empty-assessment and algebra tests pass.
No I/O or ambient clock exists in CanonFlow.Assurance.
```

### M2 — Receipt kernel

Deliver:

- canonical receipt schema;
- JCS canonicalization;
- SHA-256 digest;
- optional Ed25519 sealing;
- offline verifier;
- tamper enumeration;
- replay identity.

Acceptance:

```text
Every signed-field mutation is detected.
Two identical assessments produce byte-identical receipts.
```

### M3 — Evaluator orchestration

Deliver:

- manifest parser;
- validated paths;
- budgets;
- component execution;
- exit-code classification;
- aggregate assessment;
- CLI commands;
- four golden fixture classes.

Acceptance:

```text
Pass, Fail, Inconclusive and ToolFailure fixtures return 0, 1, 2 and 3.
```

### M4 — FsAssay integration

Deliver:

- real FsAssay runner;
- profile snapshot and digest;
- JSON/SARIF ingestion;
- protected policy;
- negative execution sentinel.

Acceptance:

```text
CI proves FsAssay executed and detected the deliberate violation.
Tool failure cannot be reported as zero findings or Pass.
```

### M5 — Reports

Deliver:

- VERDICT.json;
- findings.sarif;
- EVIDENCE.md;
- LOSS.md;
- offline REPORT.html.

Acceptance:

```text
All views are derived from assessment.cff and contain no remote dependencies.
```

### M6 — Hardened OCI and air-gap bundle

Deliver:

- pinned .NET 10 build and runtime images;
- non-root chiseled runtime;
- SBOM;
- image signature/provenance;
- hardened run command;
- air-gap archive.

Acceptance:

```text
The evaluator runs with network disabled and input mounted read-only.
The air-gap bundle verifies and runs on a clean Docker host.
```

### Experimental M7 — ONDC source-lock

Deliver:

- admitted source hierarchy;
- sealed source lock;
- one bounded retail profile;
- evidence-bundle schema;
- protocol golden vectors.

Acceptance:

```text
Independent review admits the source lock.
Until then, ONDC assessment remains disabled or Inconclusive.
```

### Experimental M8 — ONDC order-formation assessor

Deliver only after M7:

- typed ONDC IR;
- trace parser;
- lifecycle model through `/on_confirm`;
- evidence-based rules;
- deterministic ONDC component receipt.

Acceptance:

```text
No rule exceeds the admitted source-lock scope.
Missing runtime evidence is never reported as conformance.
```

### Experimental Deferred milestones

```text
Experimental M9   PostgreSQL differential sidecar
Experimental M10  WASM after native/WASM conformance and real hashing
Experimental M11  Hosted report viewer
Experimental M12  External certification workflow, only with authorized governance
```

---

## 22. Release Artifacts

For version `0.1.0`, publish:

```text
NuGet
├── CanonFlow.Assurance.0.1.0.nupkg
├── CanonFlow.Reports.0.1.0.nupkg
└── CanonFlow.Cli.0.1.0.nupkg

OCI
└── ghcr.io/canonflowfoundation/canonflow-evaluator:0.1.0

Air-gap
└── canonflow-evaluator-airgap-0.1.0.tar.gz

Schemas
├── evaluation-manifest.schema.json
└── canonflow-evidence-receipt-v1.schema.json

Evidence
├── release-assessment.cff
├── sbom.spdx.json
├── provenance.json
└── checksums.sha256
```

The release must assess itself with the release candidate image.

The resulting self-assessment receipt is published with the release.

---

## 23. Definition of Done

The MVP is Done only when an evaluator can perform:

```bash
docker load --input canonflow-evaluator.tar

docker run --rm \
  --network none \
  --read-only \
  --cap-drop ALL \
  --security-opt no-new-privileges \
  --mount type=bind,src="$PWD/sample",dst=/input,readonly \
  --mount type=bind,src="$PWD/report",dst=/output \
  canonflow-evaluator:0.1.0 \
  evaluate \
  --manifest /input/canonflow-evaluation.json \
  --output /output
```

and receive:

```text
assessment.cff
VERDICT.json
REPORT.html
EVIDENCE.md
LOSS.md
findings.sarif
sbom.spdx.json
```

The evaluator must then verify the receipt offline:

```bash
docker run --rm \
  --network none \
  --read-only \
  --mount type=bind,src="$PWD/report",dst=/input,readonly \
  canonflow-evaluator:0.1.0 \
  receipt verify \
  --receipt /input/assessment.cff \
  --public-key /input/evaluator.pub \
  --offline
```

Final acceptance equation:

\[
\begin{aligned}
\mathit{MVPReady} \iff\;&
\mathit{Net10Only}
\land \mathit{CleanBuild}
\land \mathit{RealFsAssay}\\
&\land \mathit{FourVerdicts}
\land \mathit{DeterministicReceipt}
\land \mathit{OfflineVerify}\\
&\land \mathit{HardenedContainer}
\land \mathit{NoFalseCertification}
\end{aligned}
\]

---

## 24. The One Rule

```text
CanonFlow assesses semantic fidelity.
FsAssay assesses F# engineering policy.
ONDCFlow assesses admitted protocol evidence.
The evaluator orchestrates them.
The receipt records what happened.
The seal protects the receipt.
None of these facts, alone, grants regulatory certification.
```

\[
\boxed{
\text{One .NET 10 evaluator}
+
\text{one hardened image}
+
\text{one honest receipt}
}
\]

\[
\blacksquare
\]
