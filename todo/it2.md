# Fresh deep scrutiny

Reviewed current heads:

* ONDCFlow [`2c2453c`](https://github.com/CanonFlowFoundation/Ondcflow/tree/2c2453ce483fd3606f1f12067027c78d8f91875d)
* CanonFlow [`bee7481`](https://github.com/CanonFlowFoundation/CanonFlow/tree/bee7481b124257e7a063689b8c72f4e993c60a47)

## Bottom line

This is genuine progress, not merely cosmetic. Several previous P0 findings were addressed. But the recovery is incomplete:

[
\Delta_{\text{architecture}}>0
]

[
\operatorname{Releaseable}=0
]

The current state is:

[
\boxed{
\text{Good constitutional response}
\land
\text{partial implementation}
\land
\text{still non-reproducible}
}
]

## What was genuinely fixed

| Previous defect                                        | Current state                              |
| ------------------------------------------------------ | ------------------------------------------ |
| Duplicate Npgsql versions                              | Fixed                                      |
| FSharp.Core downgrade conflict                         | Largely fixed                              |
| No package lock files                                  | Fixed across many projects                 |
| Fake certification workflow                            | Deleted—correct decision                   |
| ONDC accepted raw source-lock digest                   | Replaced by private `VerifiedSourceLock`   |
| Source-lock argument completely ignored by type system | Assessor now requires `VerifiedSourceLock` |
| No M7/M8 tests                                         | Five new golden-vector tests added         |
| No order IR                                            | `OndcOrder` and `OndcQuote` added          |
| Only two core rules                                    | Now three core rules plus one retail rule  |
| No cross-repository build procedure                    | Integration scripts added                  |

Deleting the simulated certification workflow was particularly important. See the removal commit [`bee7481`](https://github.com/CanonFlowFoundation/CanonFlow/commit/bee7481b124257e7a063689b8c72f4e993c60a47).

---

# Critical findings

## 1. Both repositories still fail the clean build contract

### CanonFlow

A targeted `dotnet restore --locked-mode` for `CanonFlow.Assurance.Tests` now succeeds. That is a real improvement.

Compilation still fails because [`Directory.Build.targets`](https://github.com/CanonFlowFoundation/CanonFlow/blob/bee7481b124257e7a063689b8c72f4e993c60a47/Directory.Build.targets) contains:

```xml
build-tools\fsassay.fsx
```

On Linux, the resulting path is literally:

```text
CanonFlow/build-tools\fsassay.fsx
```

and cannot be found.

Therefore:

[
\operatorname{LockedRestore}_{Canon}=1
]

[
\operatorname{Build}_{Canon/Linux}=0
]

The latest CanonFlow workflows independently remain red:

* [Laws failure](https://github.com/CanonFlowFoundation/CanonFlow/actions/runs/30268899766)
* [Drift failure](https://github.com/CanonFlowFoundation/CanonFlow/actions/runs/30268899883)
* [Playground deployment failure](https://github.com/CanonFlowFoundation/CanonFlow/actions/runs/30268899788)

### ONDCFlow

Locked restore fails because `nuget.config` requires `./local-feed`, but the directory is absent.

The new integration script is directionally correct, but:

* It is not executed by CI.
* It assumes sibling directories named exactly `CanonFlow` and `ONDCFlow`.
* CanonFlow cannot currently pack without correcting its Linux path.
* ONDCFlow has no GitHub Actions workflows.

So the integration script is currently an instruction, not verified integration.

---

## 2. The package-lock contract is internally inconsistent

CanonFlow changed `CanonFlow.Assurance` dependencies while retaining version:

```text
0.1.0-alpha
```

ONDCFlow’s lock file still records the older package content hash and the older dependency:

```text
FSharp.Core 9.0.200
```

CanonFlow now uses:

```text
FSharp.Core 9.0.300
```

I packed the current Assurance project in a temporary diagnostic copy after fixing only the Linux separator. The new package SHA-512 differs from ONDCFlow’s committed `contentHash`.

Formally:

[
\operatorname{Version}(P_{\text{old}})
======================================

\operatorname{Version}(P_{\text{new}})
]

but:

[
\operatorname{Hash}(P_{\text{old}})
\ne
\operatorname{Hash}(P_{\text{new}})
]

This defeats immutable locked restore.

Fix:

* Bump Assurance to a new version, such as `0.1.1-alpha`.
* Pack it.
* Update ONDCFlow’s package reference.
* Regenerate ONDCFlow lock files from that exact package.
* Never alter package contents without changing the version.

---

# ONDCFlow findings

## 3. Source-lock verification is still circular

[`AdmittedSource.fs`](https://github.com/CanonFlowFoundation/Ondcflow/blob/2c2453ce483fd3606f1f12067027c78d8f91875d/src/ONDCFlow.Core/AdmittedSource.fs) constructs a lock, calculates its digest at runtime, and then verifies the same lock against the digest it just calculated:

[
d=\operatorname{Hash}(L)
]

[
\operatorname{Verify}(L,d)
]

This proves internal consistency, not admission by an independent trust anchor.

Worse:

* Source-document digest is 32 zero bytes.
* Rule-pack digest is 32 zero bytes.
* No actual ONDC document is bundled.
* No externally reviewed expected digest is committed.
* `EffectiveFrom=None`.
* Reviewer is a self-declared string.

Therefore:

[
\operatorname{CryptographicallyConsistent}(L)=1
]

but:

[
\operatorname{AuthoritativelyAdmitted}(L)=0
]

## 4. Source-lock canonicalization omits protected fields

[`SourceLock.canonicalizeLock`](https://github.com/CanonFlowFoundation/Ondcflow/blob/2c2453ce483fd3606f1f12067027c78d8f91875d/src/ONDCFlow.Core/SourceLock.fs) omits:

* `EffectiveFrom`
* `ConflictDecisions`

Therefore two materially different locks can produce the same digest:

[
L_1.\operatorname{ConflictDecisions}
\ne
L_2.\operatorname{ConflictDecisions}
]

while:

[
\operatorname{Hash}(L_1)
========================

\operatorname{Hash}(L_2)
]

It also builds JSON using `sprintf` without proper string escaping. This is not safe canonical JSON.

## 5. The verified source lock still does not affect the receipt

[`Assessor.evaluateBundle`](https://github.com/CanonFlowFoundation/Ondcflow/blob/2c2453ce483fd3606f1f12067027c78d8f91875d/src/ONDCFlow.Core/Assessor.fs) accepts `VerifiedSourceLock`, but never reads it.

The receipt contains no:

* Source-lock digest.
* Protocol-version binding from the lock.
* Rule-pack digest.
* Source-document digest.
* Source evidence reference.

Thus:

[
\operatorname{Receipt}
\perp
\operatorname{VerifiedSourceLock}
]

Changing from one valid source lock to another can produce an identical receipt.

The type boundary improved. The evidence binding did not.

---

## 6. Schema, parser and golden vectors still describe different protocols

The committed [`evidence-bundle.schema.json`](https://github.com/CanonFlowFoundation/Ondcflow/blob/2c2453ce483fd3606f1f12067027c78d8f91875d/schemas/evidence-bundle.schema.json) requires:

```json
{
  "source_lock": {},
  "traces": [
    {
      "action": "...",
      "payload": {}
    }
  ]
}
```

The parser and golden vectors use:

```json
{
  "traces": [
    {
      "context": {},
      "message": {}
    }
  ]
}
```

The golden vectors omit `source_lock` completely.

Therefore the test named “Valid Order Formation Bundle” is invalid under the repository’s own schema.

Required law remains broken:

[
\operatorname{SchemaValid}(b)
\Rightarrow
\operatorname{ParserAccepts}(b)
]

---

## 7. The lifecycle rule still accepts invalid sequences

[`Lifecycle.validateSequence`](https://github.com/CanonFlowFoundation/Ondcflow/blob/2c2453ce483fd3606f1f12067027c78d8f91875d/src/ONDCFlow.Core/Lifecycle.fs) still checks only loose relative membership.

It accepts:

```text
[confirm]
[on_confirm]
[search, search]
[confirm, on_confirm]
```

because it does not consume the matched expected action and does not require the complete sequence.

A concrete false-pass counterexample is:

```text
one confirm trace
+ valid transaction GUID
+ order ID
+ quote
```

All current rules then pass:

[
R_{\text{transaction}}=1
]

[
R_{\text{lifecycle}}=1
]

[
R_{\text{order}}=1
]

[
R_{\text{retail-guid}}=1
]

Therefore:

[
\operatorname{Verdict}=\operatorname{Pass}
]

even though no search, select or init occurred.

The new “valid full lifecycle” test proves that one good sequence passes. It does not prove that invalid sequences fail.

---

## 8. “Pass” currently means only four weak rules

Current implemented rules are:

1. Transaction ID consistency.
2. Loose lifecycle ordering.
3. Confirm order ID and quote presence.
4. Retail transaction ID parses as GUID.

Not checked:

* Message-ID grammar—the “valid” vector uses `m1`, `m2`, etc.
* Context version.
* Country/city.
* Subscriber IDs.
* Timestamp validity.
* TTL.
* Quote consistency across `on_select`, `on_init`, `confirm`.
* Order-ID consistency.
* Required intent/catalog/billing shapes.
* Schema validity.
* Source-lock presence.
* Signatures.
* Idempotency.

So the name “Valid Order Formation Bundle Passes Assessment” is too strong. It means only:

[
\text{passes four experimental predicates}
]

The receipt should record the exact rule-pack digest and coverage denominator.

---

## 9. Receipt axes remain collapsed

ONDCFlow sets:

```fsharp
Compliance = verdictStr
```

This can produce:

```text
Compliance = "Pass"
```

rather than:

```text
Compliance = "Conformant"
```

The constitution explicitly separates:

[
\text{Health}
\times
\text{Compliance}
\times
\text{Verdict}
]

The implementation still collapses those domains into strings.

It also claims component and engine version `1.0.0` while the package is alpha.

---

# CanonFlow findings

## 10. The dangerous false-pass path remains

The fake certification workflow is gone, but the underlying evaluator is unchanged:

* [`Canon.Cli`](https://github.com/CanonFlowFoundation/CanonFlow/blob/bee7481b124257e7a063689b8c72f4e993c60a47/src/Canon.Cli/Program.fs) writes `"stub"` to `assessment.cff` and exits `0`.
* [`FsAssayRunner`](https://github.com/CanonFlowFoundation/CanonFlow/blob/bee7481b124257e7a063689b8c72f4e993c60a47/src/CanonFlow.Evaluator/FsAssayRunner.fs) remains simulated.
* [`VerdictView`](https://github.com/CanonFlowFoundation/CanonFlow/blob/bee7481b124257e7a063689b8c72f4e993c60a47/src/CanonFlow.Reports/VerdictView.fs) hard-codes `Complete`, `Conformant` and exit code `0`.

This remains the highest implementation risk:

[
\operatorname{StubEvaluation}
\rightarrow
\operatorname{ExitCode}(0)
]

Delete or disable `evaluate` until it is wired. A `ToolFailure` is safer than synthetic success.

## 11. Assurance sealing remains incomplete

Still missing:

* Receipt signing operation.
* Full `.cff` parsing and verification.
* Recanonicalization during verification.
* Receipt digest comparison.
* Seal-state types instead of raw strings.
* Exhaustive tamper tests.
* Native/WASM equivalence tests.

The pure SHA implementation is useful, but it does not establish receipt verification.

## 12. M9–M11 remain demonstrations

* PostgreSQL “differential” assessment still checks connection and timezone only.
* WASM still uses a regex DDL parser and does not generate CanonFlow receipts.
* The viewer still displays unverified uploaded JSON and uses `innerHTML` with uploaded fields.

These milestones should remain explicitly experimental.

---

# Documentation truth

ONDCFlow’s [`README`](https://github.com/CanonFlowFoundation/Ondcflow/blob/2c2453ce483fd3606f1f12067027c78d8f91875d/README.md) still says:

* Tests green `9/9`, although new tests were added.
* Slice 1 complete.
* Verified integration.
* Release ready.

That directly conflicts with the formal requirements, which correctly describe the MVP terminus as preview and keep 48 rules outside the implemented denominator.

CanonFlow’s `LESSONS_LEARNED.md` still says M0–M12 implementation concluded and refers to the now-deleted certification workflow.

The constitutional document is more honest than the README and milestone documentation.

---

# Updated scorecard

| Area                       | Previous |  Current |
| -------------------------- | -------: | -------: |
| Architecture direction     |     7/10 | **8/10** |
| Package discipline         |     2/10 | **4/10** |
| CanonFlow assurance kernel |     4/10 | **4/10** |
| Canon evaluator/CLI        |     1/10 | **1/10** |
| ONDC source-lock design    |     2/10 | **4/10** |
| ONDC source-lock trust     |     1/10 | **1/10** |
| ONDC rule implementation   |     2/10 | **3/10** |
| Tests                      |     2/10 | **4/10** |
| Reproducible build         |     0/10 | **1/10** |
| Claim honesty              |     1/10 | **4/10** |

The fake certification removal raises claim honesty materially. README overclaims prevent it from going higher.

# Rumsfeld matrix

| Category         | Current state                                                                                                                             |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| Known knowns     | Build hook fails on Linux; local feed absent; package hash changed without version change; evaluator stubs remain; schema/parser mismatch |
| Known unknowns   | Real ONDC source documents, authoritative hierarchy, exact identifier grammar, intended rule-pack contents                                |
| Unknown knowns   | Assumptions hidden in GUID parsing, zero digests, hard-coded protocol/version strings and runtime-generated trust anchors                 |
| Unknown unknowns | Behaviour against real network traces, malicious bundles, cross-platform receipts and external auditors                                   |

# Exact next move

Do only this slice:

[
\boxed{
\text{Green Linux Build}
\rightarrow
\text{Versioned Assurance Package}
\rightarrow
\text{External Source-Lock Anchor}
\rightarrow
\text{Schema/Parser Agreement}
\rightarrow
\text{Exact Lifecycle}
\rightarrow
\text{Four Honest Rules}
\rightarrow
\text{Bound Receipt}
}
]

Do not add another milestone until this counterexample fails:

```text
confirm-only trace + ID + quote
```

And until this command succeeds in CI from clean clones:

```bash
build-integration.sh
```

My updated assessment: you responded correctly to the review, especially by deleting false certification and introducing locked dependencies. But the implementation moved only halfway through the recovery constitution. The next victory is not M9 or another rule—it is making one exact vertical slice impossible to falsely pass.
