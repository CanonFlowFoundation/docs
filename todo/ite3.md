## X-ray verdict

At the pinned heads below, this is a credible experimental assurance stack—not yet a trustworthy ONDC product.

* **ONDCFlow:** strongest component; a real bounded evaluator.
* **CanonFlow:** valuable assurance kernel and receipt engine, but currently permits a proven false `Pass`.
* **FsAssay:** original and promising, but its own behavioural suite is red.
* **M9–M12:** correctly classified as pending. Some prototype files exist, but none of those milestones is discharged.
* **Business:** the original claim “nobody checks ONDC integrations” is no longer defensible. ONDC now has an official Workbench offering schema validation, scenarios and reports.

My overall assessment:

[
\text{Readiness}=\min(
\text{Kernel},
\text{Analyzer},
\text{Protocol},
\text{Authority},
\text{Market proof})
]

| Dimension                             | X-ray score |
| ------------------------------------- | ----------: |
| CanonFlow assurance kernel            |         70% |
| FsAssay dependable implemented subset |         35% |
| ONDCFlow bounded ten-rule profile     |         60% |
| Reproducible release chain            |         40% |
| Authoritative ONDC admission          |          5% |
| Demonstrated customer demand          |         10% |

These are readiness judgements, not code-coverage percentages.

## What I verified

| Repository | Exact head                                                                                                      | Runtime evidence                                                                                                                                                                                                                                                                                   | Verdict                        |
| ---------- | --------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------ |
| ONDCFlow   | [`f6d3a81`](https://github.com/CanonFlowFoundation/Ondcflow/commit/f6d3a8180e8e5c3f05c5ce6cd36be6663a782058)    | Locked restore/build passed; **19/19 tests passed**; [CI green](https://github.com/CanonFlowFoundation/ONDCFlow/actions/runs/30286250453)                                                                                                                                                          | Amber-green experimental slice |
| CanonFlow  | [`a24d6c2`](https://github.com/CanonFlowFoundation/CanonFlow/commit/a24d6c201d340ac44a994e89c60035662180a529)   | Build: zero warnings/errors. **47/49 tests passed locally**; two PostgreSQL tests could not run without Docker. Signed receipt generated and verified offline. Current [M0–M8 CI is red](https://github.com/CanonFlowFoundation/CanonFlow/actions/runs/30286582249).                               | Amber-red                      |
| FsAssay    | [`a7533e9`](https://github.com/CanonFlowFoundation/FSharpAssay/commit/a7533e9bb297d644db150a72ffa8adae4eb00082) | Analyzer/runner build clean. Behavioural suite: **29 passed, 8 failed, 1 errored**. [Core execution workflow red](https://github.com/CanonFlowFoundation/FSharpAssay/actions/runs/30286351195); [audit workflow red](https://github.com/CanonFlowFoundation/FSharpAssay/actions/runs/30286351261). | Red experimental analyzer      |

The CanonFlow → ONDCFlow path produced the correct honest result:

```text
Verdict: Inconclusive
Applicable rules: 10
Evaluated rules: 10
Reason:
- no independent ONDC authority admission
- no transport evidence
- no replay evidence
- no registry snapshot
```

The resulting `.cff` receipt’s digest and Ed25519 seal verified offline. That part is genuine engineering, not scaffolding.

## Critical blockers

### P0 — CanonFlow can issue a false `Pass`

[`FsAssayRunner.fs`](https://github.com/CanonFlowFoundation/CanonFlow/blob/a24d6c201d340ac44a994e89c60035662180a529/src/CanonFlow.Evaluator/FsAssayRunner.fs#L83-L87) selects the first `.fs` artifact:

```fsharp
manifest.Subject.Artifacts
|> List.tryFind (fun artifact -> artifact.EndsWith(".fs", ...))
```

I constructed this manifest:

```json
"artifacts": ["Clean.fs", "Bad.fs"]
```

`Bad.fs` contained `Option.get`. CanonFlow scanned only `Clean.fs` and returned:

```text
Verdict: Pass
FsAssay findings: 0
Exit: 0
```

This alone blocks certification, badges and an M0–M8 completion claim.

Required law:

[
\text{ScannedFiles}=\text{DeclaredApplicableFiles}
]

and:

[
\exists f\in A:\operatorname{CriticalFinding}(f)
\Longrightarrow
\operatorname{Verdict}(A)\neq Pass
]

### P0 — ONDC rule-pack/code binding is stale

The [admitted rule pack](https://github.com/CanonFlowFoundation/Ondcflow/blob/f6d3a8180e8e5c3f05c5ce6cd36be6663a782058/admission/retail-order-formation-preview-v2.rulepack.json) records:

```text
Rules.fs = sha256:1e8a276...
```

The current [`Rules.fs`](https://github.com/CanonFlowFoundation/Ondcflow/blob/f6d3a8180e8e5c3f05c5ce6cd36be6663a782058/src/ONDCFlow.Core/Rules.fs) is:

```text
sha256:deee873...
```

The outer rule-pack digest is correctly anchored, but the runtime does not recalculate the component digests. Therefore, the receipt binds a manifest containing stale code hashes—not necessarily the executable rules being assessed.

Required gate:

[
\forall (p,h)\in\text{RulePack.Components}:
SHA256(\operatorname{bytes}(p))=h
]

### P0 — Some ONDC “evidence” is self-declared

[`IR.fs`](https://github.com/CanonFlowFoundation/Ondcflow/blob/f6d3a8180e8e5c3f05c5ce6cd36be6663a782058/src/ONDCFlow.Core/IR.fs#L60-L80) represents transport and registry verification as booleans:

```fsharp
SignatureVerified: bool
Duplicate: bool
Idempotent: bool
```

A caller can submit:

```json
"signature_verified": true
```

No raw signature, signed HTTP header, registry document, public key or replay execution is required. These rules currently validate assertions about evidence, not the evidence itself.

Either rename them `DeclaredObservation`, forcing `Inconclusive`, or introduce trusted observers that produce unforgeable evidence types.

### P0 — FsAssay is not presently a release gate

FsAssay declares 91 identifiers:

| Status      | Count |
| ----------- | ----: |
| Implemented |    35 |
| Dummy       |    22 |
| Prototype   |    34 |

That distinction is honestly encoded in [`Domain.fs`](https://github.com/CanonFlowFoundation/FSharpAssay/blob/a7533e9bb297d644db150a72ffa8adae4eb00082/FsAssay.Analyzers/Domain.fs#L222-L240), but not reflected in the [README](https://github.com/CanonFlowFoundation/FSharpAssay/blob/a7533e9bb297d644db150a72ffa8adae4eb00082/README.md), which claims “core guarantees” and zero false positives.

Other problems:

* 9 of 38 behavioural tests currently fail or error.
* `.fsassayrc` loads `severities` and `targetGrade`, but neither affects the verdict.
* The depth rule is literally an empty implementation.
* CanonFlow reports FsAssay as `1/1 applicable rules`, hiding the actual catalogue denominator.
* FsAssay exit `2 = Inconclusive` is converted by CanonFlow into broken/tool failure.
* The README says MIT; the repository root licence is Apache-2.0.

FsAssay should publish an “admitted rule pack” containing only independently tested rules. Prototype rules must never block production.

## M9–M12 truth

| Milestone                      | What exists                                                                                                                                                                                                                                              | Honest completion |
| ------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------: |
| **M9 PostgreSQL differential** | [`PgsqlAssessor.fs`](https://github.com/CanonFlowFoundation/CanonFlow/blob/a24d6c201d340ac44a994e89c60035662180a529/src/CanonFlow.Profile.Pgsql/PgsqlAssessor.fs) checks connection and timezone                                                         |             5–10% |
| **M10 WASM parity**            | A DDL playground with a “Regex dummy”; current Fable workflow is [red](https://github.com/CanonFlowFoundation/CanonFlow/actions/runs/30286582416)                                                                                                        |            10–15% |
| **M11 Viewer**                 | Vite preview using an older receipt shape; no cryptographic verification; renders receipt strings through `innerHTML`, creating an XSS boundary                                                                                                          |               10% |
| **M12 Certification**          | Governance prose, no authority relationship. The simulated workflow was deliberately [removed](https://github.com/CanonFlowFoundation/CanonFlow/commit/bee7481b124257e7a063689b8c72f4e993c60a47), while the governance document still says “operational” |              0–5% |

Do not build these next merely because they are numbered.

For the offline ONDC MVP:

* M9 is unrelated.
* M10 is optional.
* M11 should be a local static viewer only, after receipt verification is sound.
* M12 is not primarily software work. It requires an external authority, governance keys, liability boundaries and written permission.

## How much work remains

Assumption: one experienced F# engineer using agents, stable scope, with real ONDC traces available.

| Work package                                                                     |                              Estimate |
| -------------------------------------------------------------------------------- | ------------------------------------: |
| Close false-pass, FsAssay failures, rule-pack binding and all CI                 |                   15–25 engineer-days |
| Bind ten rules to official sources and upgrade/freeze version explicitly         |                            10–20 days |
| Replace self-declared signature/replay/registry booleans with evidence observers |                            10–20 days |
| Pilot packaging, documentation and three real golden transactions                |                            10–15 days |
| Remaining 38 rules, with positive/negative vectors                               |                            40–80 days |
| M9 proper differential engine                                                    |                            10–15 days |
| M10 native/WASM receipt equivalence                                              |                            15–25 days |
| M11 safe offline receipt viewer                                                  |                            10–20 days |
| M12 engineering after authority exists                                           | 5–10 days; authority timeline unknown |

Therefore:

* **Trustworthy bounded paid pilot:** approximately 35–60 engineer-days.
* **Broad 48-rule pre-certification product:** approximately 75–140 days.
* **Full M9–M12 platform:** roughly 110–190 days, plus an unbounded external governance timeline.

## Business reality

The official ONDC material now lists **Retail B2C 1.2.5 as latest**, while ONDCFlow is pinned to an internally derived 1.2.0 preview. ONDC also operates an official **Workbench** with schema validation, scenario simulation, debugging and reporting. [Official ONDC developer resources](https://github.com/ONDC-Official)

That means this proposition will lose:

> “We are an ONDC validator because nobody else checks integrations.”

The defensible proposition is:

> “An Apache-2.0, offline, air-gapped ONDC regression and evidence sidecar that produces reproducible, independently verifiable receipts for CI and audits.”

Your differentiation must be:

* Offline and air-gapped operation.
* Customer data never uploaded.
* Deterministic replay.
* Version-pinned source and rule packs.
* Signed evidence receipts.
* Code-quality plus protocol-trace assessment.
* Historical drift comparison.
* Ability to complement the official Workbench, not claim to replace it.

Your first customers are TSP and network-participant engineering/QA teams—not individual sellers and not auditors issuing official certificates.

## Next business gate

Before implementing M9–M11:

1. Interview ten ONDC/TSP engineering leads.
2. Obtain three anonymised real `search → on_confirm` trace bundles.
3. Run them through both ONDC Workbench and ONDCFlow.
4. Document defects uniquely caught by ONDCFlow.
5. Secure two design partners, with at least one paid pilot.
6. Require one partner to run it repeatedly in CI.

Continue only if:

[
\begin{aligned}
&\text{Real high-value defects uniquely found} \ge 3\
&\land\ \text{Implemented-rule false-positive rate}<5%\
&\land\ \text{Replays are byte-identical}\
&\land\ \text{At least one team pays}
\end{aligned}
]

## Rumsfeld matrix

|                | Known                                                                                                                 | Unknown                                                                                                         |
| -------------- | --------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------- |
| **Recognised** | Real receipt algebra, four verdicts, offline verification, ten bounded rules, current red tests                       | Official source admission, real-world false-positive rate, customer willingness to pay                          |
| **Hidden**     | Prototype/dummy FsAssay rules presented as uniform capability; stale hashes; M12 document claiming operational status | Adversarial traces, specification drift, liability expectations, how much the official Workbench already solves |

## My confidence

* Confidence in this repository assessment: **95%**.
* Confidence that you and I can produce the bounded trustworthy MVP: **75%**.
* Confidence that the current code is ready for a paid pilot: **35–45%**.
* Confidence in current product-market fit: **20–30%**, because no customer evidence is present and an official Workbench exists.
* Confidence in official certification today: **below 10%**, because authority cannot be manufactured in code.

The F# and mathematical architecture are not the primary risk anymore. The risks are evidence authenticity, release discipline, official-source binding and proving that somebody will pay for the offline reproducibility advantage.

## Implementation completion update — 2026-07-28

This update supersedes the technical-blocker status above. It does not rewrite
the original X-ray, which remains the audit record for the three pinned heads.

### P0 disposition

| Finding | Disposition | Executable evidence |
| --- | --- | --- |
| CanonFlow false `Pass` | **Closed** | CanonFlow passes every declared F# artifact to FsAssay, compares the normalized scanned set with the declared applicable set, and returns `ToolFailure` on mismatch. The mixed `Clean.fs` + `Bad.fs` fixture returns `Fail`, reports `FSA-C02`, and records two declared/two scanned files. |
| ONDC rule-pack/code binding | **Closed** | `RulePackBinding` recalculates every component SHA-256, rejects missing/escaping/mismatched components, binds the runtime rule-pack digest to the admitted source lock, and has a stale-manifest negative test. |
| Self-declared ONDC evidence | **Contained, observer work remains** | Inputs are explicitly named `declared_transport`, `declared_replay`, and `declared_registry`. Positive declarations cannot establish conformance and therefore remain `Inconclusive`; explicit negative declarations can still prove `Fail`. Trusted transport, replay, and registry observers remain a future evidence-source boundary. |
| FsAssay as release gate | **Closed for a bounded subset** | The production admission contains exactly 21 implemented rules with positive behavioral specimens. Non-admitted catalog rules cannot create a blocking production verdict. The executable suite passes 39/39. Unsupported `targetGrade`/`severities` configuration and the false README guarantees were removed. |

### Bound versions

| Component | Version/boundary |
| --- | --- |
| FsAssay CLI | `1.0.4`; 21 admitted production rules |
| ONDCFlow Core/Profile | `0.1.6-alpha` |
| .NET SDK | exactly `10.0.301` |
| CanonFlow runtime | .NET `10.0.9` chiseled image, non-root UID `1654` |

### Verification ledger

The following gates passed from WSL2:

* FsAssay build: zero warnings/errors; behavioral/fault suite **39/39**.
* ONDCFlow locked restore/build: zero warnings/errors; tests **21/21**.
* CanonFlow locked restore/build: zero warnings/errors.
* CanonFlow tests: laws **26/26**, assurance **15/15**, XP **1/1**,
  PostgreSQL/Testcontainers integration **7/7**.
* Cross-repository locked integration: **passed**.
* Hardened container matrix: deterministic `Pass`, admitted-rule `Fail`,
  two-file false-pass sentinel `Fail`, ONDC `Inconclusive`, missing-tool
  `ToolFailure`, and self-test all **passed** with networking disabled,
  read-only root, all capabilities dropped, and no-new-privileges.
* Runtime filesystem gate: non-root, chiseled, and no shell/package manager or
  private key in the image.
* Windows 11 WSL Containers (`wslc 2.9.4.0`) independently built the same
  Dockerfile and ran `CanonFlow Evaluator 0.1.0-alpha (.NET 10.0.9)`.
* Air-gap release mechanics: SBOM generation, checksums, offline Cosign
  verification, image reload, and offline execution **passed with an explicitly
  local test key**. Example `bin/` and `obj/` output is excluded from the bundle.

The local key proves the release mechanism, not release authority. Production
signing still requires an externally governed key and release ceremony.

### Honest completion boundary

The actionable repository P0 blockers in this review are complete. The result is
a technically hardened, bounded, offline assurance MVP. It is suitable for
controlled engineering evaluation and design-partner pilots.

It is **not** an ONDC certificate, an authoritative ONDC admission, or evidence
of product-market fit. The following cannot be completed by repository changes
alone and remain explicit external gates:

1. Official admission of the pinned ONDC sources and rule pack.
2. Trusted signature, registry, and replay observer implementations and their
   operating keys/services.
3. Three anonymized real transaction traces and comparative Workbench results.
4. Measured field false-positive/false-negative rates.
5. Two design partners, one paid recurring pilot, and demonstrated CI use.
6. A production signing authority, liability boundary, and release ceremony.
7. M9 PostgreSQL differential, M10 native/WASM parity, M11 verified safe viewer,
   and M12 authority-backed certification; these remain separately scoped and
   must not be inferred from this P0 closure.

The permitted claim after this work is:

> CanonFlow + ONDCFlow + FsAssay is a reproducible, air-gap-capable experimental
> assurance stack with evidence-bounded verdicts and a tested 21-rule F# gate.

The prohibited claim remains:

> Official ONDC certification or production approval.

### Remote CI closure — 2026-07-28

Follow-up clean-checkout reproductions found and closed four CI-only defects:

* ONDCFlow's admitted `Rules.fs` digest was stale after line-ending
  normalization. The component, manifest, source-lock and golden-vector
  digests now agree, and the corrected packages are versioned `0.1.6-alpha`.
* CanonFlow now invokes non-executable repository shell scripts explicitly
  through Bash. Its standalone Wasm project pins both Fable and FSharp.Core,
  and a clean NuGet cache can regenerate and build the playground.
* The Sangam Credit schema is an intentional negative fixture. CI now requires
  strict mode to reject its unsupported constraints and verifies the proof
  report instead of treating the expected rejection as a product failure.
* FsAssay's audit distinguishes findings (`exit 1`) from tool failures, has the
  permission required to upload SARIF, disposes browser resources, and verifies
  the deployed site with the locked Playwright runtime.

Remote results at the final pushed heads:

| Repository/head | GitHub Actions result |
| --- | --- |
| CanonFlow `1d0ac7d` | **4/4 workflows passed**: Playground, Evaluator M0–M8, Drift, Laws |
| FSharpAssay `4da15b2` | **4/4 workflows passed**: CI, Architectural Audit/SARIF, Pages, combined deploy/live verification |
| ONDCFlow `d8bf362` | **Locked Gates passed** |

The full FsAssay self-audit remains intentionally visible: it scanned 23 files
and reported 425 existing findings. That is architectural debt evidence, not a
clean bill of health. The independently executable production-rule regression
suite remains **39/39 passed**, and only its 21 positively exercised rules are
admitted to blocking production verdicts.
