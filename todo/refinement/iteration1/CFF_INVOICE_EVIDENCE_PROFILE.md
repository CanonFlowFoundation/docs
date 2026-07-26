# CFF_INVOICE_EVIDENCE_PROFILE.md — cff.invoice-evidence/1 (Draft)

> **One deterministic ZIP that lets a stranger's machine re-derive the
> verdict — and see exactly what the bundle cannot establish.**

- **Status:** `DESIGNED` — profile draft; normative only when frozen by
  work packet `CFF-0001` (Pipeline §11, train item 1)
- **Snapshot:** source review 2026-07-17 · integration pass 2026-07-18
- **Authority:** subordinate to `CANONFLOW_BASE.md` §18/§19; executes under
  `CFF_PIPELINE.md`; layout divergences from Base §18 are ADR candidates
  (§10 below), never silent amendments
- **Profile ID:** `cff.invoice-evidence/1`

Sections marked **▲** are integration deltas against the doc set.

---

## 0 · DECISION

Embrace the container; ship the smallest rigorous core. Four parts belong
in CFF v1 — deterministic container, canonical facts, evaluation context,
verdicts with traceability. **Avro and signing remain optional, separately
versioned conformance profiles** until cross-runtime byte/semantic
equivalence and trust semantics are Crucible-proven. This is Base §18's
storage-evolution law and the Pipeline §10 freeze, applied to one profile.

---

## 1 · CONTAINER LAYOUT

```text
invoice-2024-001.cff              # one deterministic ZIP, never a directory
├── manifest.json                 # content inventory and digests (§2)
├── facts/
│   └── invoice.json              # normative canonical facts
├── sources/
│   └── index.json                # legal sources, stable citation IDs (§4)
├── evaluation/
│   └── context.json              # engine, pack and execution context (§3)
├── verdicts/
│   └── verdict.json              # per-rule outcomes and explanations (§5)
├── evidence/
│   ├── trace.json                # source → fact → rule → outcome trace
│   └── acquisition.json          # how input was obtained/transformed
├── proof/
│   ├── proof-manifest.json       # normative machine-readable test evidence
│   └── report.html               # optional derived human rendering
└── attachments/
    └── original-invoice.pdf      # optional sensitive source evidence
```

▲ **Base §18 reconciliation.** The Base's normative layout lists
`manifest.json · sources/ · facts/ · verdicts/ · evidence/ · attachments/ ·
proof-manifest.json · signatures/`. This profile diverges twice, and both
divergences are proposed amendments, not accomplished facts:

```text
+ evaluation/context.json    NEW directory — the Base layout has no home
                             for execution context, and §3 shows a digest
                             alone cannot carry it.  ADR candidate.
~ proof/ directory           proof-manifest.json moves under proof/ with an
                             optional derived rendering beside it.  The
                             HTML is DERIVED ONLY — machine-readable JSON
                             is normative (Base §4: structured meaning;
                             presentation at the boundary).  ADR candidate.
  signatures/                absent from v1 payload by design; reserved
                             path for the deferred profile (§8.2) and
                             excluded from manifest entries (§2).
```

Disposition of the originally proposed six files: `manifest.json` — keep,
with the acyclicity fix (§2) · `canonical-invoice.avro` — defer; canonical
JSON first, Avro becomes a projection (§8.1) · `rule-pack-digest.json` —
expand into `evaluation/context.json` (§3) · `verdict.json` — keep and
expand (§5) · `proof-manifest.html` — derived only; JSON normative ·
`ca-signature.pem` — replace and defer: "CA" is ambiguous (Chartered
Accountant vs Certifying Authority), a PEM certificate is not a signature,
and **a private key is never packaged** (§8.2).

---

## 2 · MANIFEST AND THE ACYCLIC DIGEST MODEL

"Hashes of all files" is circular: the manifest cannot contain its own
final hash, and signatures create a second cycle. The model:

```text
manifest entries = all payload entries
                   EXCLUDING manifest.json
                   EXCLUDING signatures/**

payloadDigest    = SHA-256(exact canonical bytes of manifest.json)

signature        = signature over a canonical scope object
                   containing payloadDigest              (§8.2)
```

Each entry:

```json
{
  "path": "facts/invoice.json",
  "role": "canonical-facts",
  "mediaType": "application/json",
  "byteLength": 4821,
  "sha256": "..."
}
```

Multiple signatures attach without changing payload identity. ▲ This slots
exactly into the §46.1 type-state ladder: `VerifiedEntries` checks entry
digests against the manifest; `TrustAssessedBundle` checks signatures over
`payloadDigest`; neither step can be confused with the other because they
verify different objects. ▲ **Redaction is explicit, never silent:** an
omitted or redacted attachment appears in the manifest with
`"redacted": true` and a reason code, so the bundle states what it does not
contain (Base §6 discipline applied to the container itself; manifesto
commitment 8).

---

## 3 · EVALUATION CONTEXT — `evaluation/context.json`

A digest alone cannot reproduce a decision. The context makes the
determinism tuple explicit:

```json
{
  "engine":  { "id": "GSTFlow", "version": "0.1.0", "buildSha256": "..." },
  "rulePack": { "id": "gst.in.invoice", "version": "2024.04", "sha256": "..." },
  "parameterPackSha256": "...",
  "interpretationSnapshotSha256": "...",
  "jurisdiction": "IN",
  "effectiveAt": "2024-04-01",
  "executionProfile": "dotnet-decimal",
  "canonicalizationProfile": "cff-json-decimal-string/1"
}
```

This kills the dangerous claim that "same rule-pack digest" ⇒ "same
verdict." ▲ In the DYNAMIC annex's terms: the kernel's collapse law
`[a]p ↔ ⟨a⟩p` is asserted over the **whole tuple**
`(input, engine, packs, context)` — `context.json` is that tuple
serialized, which is what makes cross-machine replay (train item 12) a
well-defined test rather than a hope. ▲ Per Base §10, the *verdict record*
additionally carries `evaluatedAt` (wall-clock provenance); it lives in
`verdicts/verdict.json`, not here, because context is what determines the
result and the timestamp deliberately is not.

---

## 4 · LEGAL SOURCE REGISTRY — `sources/index.json`

Each source records: stable internal ID (e.g.
`gstref://cgst-act/2017/s170`) · issuing authority · document title and
type · publication and effective dates · supersession status · section,
paragraph and page anchors · **digest of the exact document consulted** ·
official URL or bundled evidence path.

Verdicts cite source IDs, never fragile inline web links. ▲ The URI
schemes `gstref://` (statutory sources) and `fact://` (canonical fact
paths, §5) are profile-defined vocabularies: register both in the LAT
glossary and the profile schema, and treat an unregistered scheme in a
bundle as a validation failure — stable citation only works if the ID
space itself is governed.

---

## 5 · VERDICT ENVELOPE — `verdicts/verdict.json`

Never reduce an invoice to one Pass/Fail. Per-rule outcomes:

```json
{
  "overall": "Unknown",
  "evaluatedAt": "2026-07-17T10:42:11Z",
  "outcomes": [
    {
      "ruleId": "GST.IN.ROUNDING.S170",
      "outcome": "Pass",
      "reasonCode": "ROUNDING_CONFORMS",
      "factRefs": ["fact://invoice/totals/tax"],
      "sourceRefs": ["gstref://cgst-act/2017/s170"],
      "traceRef": "evidence/trace.json#/traces/12"
    }
  ],
  "unsupportedFacts": [],
  "unknowns": [
    {
      "code": "SUPPLY_EVIDENCE_NOT_PROVIDED",
      "messageKey": "unknown.supply_evidence_not_provided",
      "params": {}
    }
  ]
}
```

Laws:

```text
outcome vocabulary = the FULL Base §6 algebra:
    Pass | Warning | Unknown | NeedsEvidence
    | RequiresProfessionalReview | Fail
overall            = the §6 explicit severity/aggregation function,
                     never DU declaration order
NO probabilistic confidence score in statutory outcomes  (Base §22)
```

▲ **Correction to the source draft:** the original example carried a prose
`"message"` inside the canonical envelope. Base §4 forbids localized prose
in core results — canonical content is `MessageKey + TypedParameters`;
rendering happens at the presentation boundary. The example above is the
corrected form. A bundle whose envelope contains display prose fails
profile validation, because locale-dependent bytes would corrupt canonical
agreement across hosts.

---

## 6 · EVIDENCE VERSUS PROOF — DIFFERENT CLAIMS, DIFFERENT FILES

```text
evidence/trace.json          why THIS invoice received ITS verdict
                             (source → fact → rule → outcome, per outcome)
evidence/acquisition.json    how the input was obtained or transformed
                             (raw → parsed → normalized provenance, §46.1)
proof/proof-manifest.json    testing of the ENGINE and RULE PACK:
                             property IDs, FsCheck seeds, run counts,
                             shrink results, corpus digest, source commit,
                             compiler/toolchain versions, target runtime
```

The proof manifest does **not** prove the particular invoice is genuine —
and the trace does not prove the engine is well-tested. ▲ Modal reading
(DYNAMIC §2): `trace.json` documents one **diamond witness** — the actual
path this evaluation took; `proof-manifest.json` reports **box-discharge
attempts** — sampled universals over the engine and pack, with seeds so
the sampling is replayable. Conflating them is claiming a box from a
witness, the exact overclaim the modal taxonomy exists to name. Bundle
validation checks that every `traceRef` in the envelope resolves
(referential box; same discharge class as `lat check`).

---

## 7 · CANONICALIZATION — `cff-json-decimal-string/1`

Monetary decimals are **strings with a strict grammar** in canonical JSON.
RFC 8785 (JCS) canonicalizes numbers with IEEE-754 semantics —
insufficient for `System.Decimal`-grade monetary fidelity — so CFF defines
its own canonicalization profile rather than adopting JCS for money. This
is Base §5 (`JSON wire value = canonical decimal string`) given a pinned
profile name, referenced from `evaluation/context.json`, and versioned in
the §43.8 manifest fields (`canonicalization_version`). ▲ The strict
grammar (sign, integer, fraction, scale bounds, no exponent, no leading
zeros) is itself a `CFF-0001` deliverable with hostile fixtures — the
grammar, not the serializer, is what reviewers approve.

---

## 8 · DEFERRED PROFILES

### 8.1 · Avro projection — `projections/`

Appropriate later as an optional high-volume **projection**, never the
authority:

```text
projections/canonical-invoice.avsc     exact writer schema (mandatory —
                                       binary Avro is uninterpretable
                                       without it)
projections/canonical-invoice.avro
```

Conditions: monetary fields use `logicalType: decimal` with explicit
precision and scale · `float`/`double` never · .NET, Fable/JS, and every
supported reader proven to round-trip identical values · canonical JSON
remains authoritative until that proof exists. **Byte-determinism
caveat:** Avro object-container files embed a randomly generated sync
marker, so their bytes are not inherently deterministic — a canonical Avro
profile must additionally fix schema bytes, codec, block layout, and
sync-marker derivation before "same input ⇒ same bytes" can be claimed.
▲ Which is why it stays behind the Base §18 gate ("Avro enabled after
decimal/union round-trip proof") and outside the Pipeline §10 freeze.

### 8.2 · Signature profile — `signatures/`

Never a single ambiguous `ca-signature.pem`. Future shape:

```text
signatures/reviewer-001/
├── scope.json                  what is signed, by whom, for what purpose
├── signature.p7s               detached CMS/PKCS#7 signature
├── certificate-chain.pem       chain, not a signature
├── timestamp.tsr               optional trusted timestamp
└── validation-evidence.json    OCSP/CRL/trust evaluation result
```

`scope.json` states: `payloadDigest` · signer role
(`chartered-accountant`, `rule-pack-publisher`, …) · signature intent
(`prepared` | `reviewed` | `approved-for-specified-scope`) · covered
period and jurisdiction · signature-policy identifier.

▲ **Role mapping onto Base §19** — the profile's roles are the wire form
of the Base's trust separation, and must stay bijective with it:

```text
scope.json role/intent            Base §19 role
chartered-accountant / reviewed   author or review approval (DSC workflow)
independent reviewer / reviewed   review approval
rule-pack-publisher / approved    release signature (Foundation key)
(no scope.json)                   transport integrity = SHA-256 digests only
```

Detached CMS aligns with Indian CCA interoperability guidance; trusted
timestamps establish *when* a signature existed. And the meaning stays
bounded: **a signature attests that this signer attested to this exact
payload for this declared purpose** — never that the commercial
transaction occurred, that every relevant document was included, or that a
government authority accepted the conclusion. (Base §19: signature ≠ legal
correctness; §18: digest ≠ signature ≠ authenticity.)

---

## 9 · THE v1 CORE (normative list)

```text
1. Deterministic ZIP safety rules
2. Canonical decimal-string JSON (cff-json-decimal-string/1)
3. Reproducible manifest + acyclic payloadDigest model
4. Evaluation context
5. Per-rule verdicts with legal citations (full §6 algebra, MessageKey only)
6. Fact → source → rule traceability (resolvable traceRefs)
7. Machine-readable proof manifest (seeds, digests, toolchain)
8. Explicit omissions, redactions, and Unknown outcomes
```

Avro and signatures follow as separately versioned optional conformance
profiles **after** the Crucible demonstrates cross-runtime byte and
semantic equivalence.

---

## 10 · ▲ PIPELINE BINDING AND ADR CANDIDATES

Train mapping (Pipeline §11):

```text
Item 1  CFF_V1_SPEC freeze        ← this document is its primary input;
                                    §1 layout + §7 grammar + §2 digest
                                    model are the spec's contested cores
Item 3  canonical manifest schema ← §2 entry schema + redaction fields
Item 4  manifest parser           ← §2 exclusion rules as fixtures
Item 7  digest generation/order   ← acyclic model; signatures excluded
Item 8  round-trip + replay       ← §3 context tuple defines "same"
Item 14 signature milestone       ← §8.2 wholesale, as the R3 spec seed
```

ADR candidates (Base amendments — through §29 process, never silent):

1. Base §18 layout — add `evaluation/`; permit `proof/` directory with
   `proof-manifest.json` normative and renderings derived.
2. Base §18 vocabulary — register `gstref://` and `fact://` ID schemes;
   unregistered schemes fail validation.
3. Base §4/§6 — profile validation rule: canonical envelopes carry
   `messageKey + params`, never prose; full outcome algebra mandatory.
4. Base §43.8 — `canonicalizationProfile` and `executionProfile` names
   join the CFF manifest field list explicitly.
5. DYNAMIC annex §7 — discharge vocabulary gains `trace-resolution`
   (referential box over traceRefs/sourceRefs/factRefs).

---

## 11 · FALSIFIER SKETCH (into CFF-0001 acceptance)

```text
manifest lists manifest.json or signatures/**        → invalid (cycle)
entry digest mismatch                                → invalid at VerifiedEntries
envelope contains prose message                      → profile-invalid
outcome outside the §6 algebra                       → profile-invalid
confidence score present                             → profile-invalid
traceRef/sourceRef/factRef fails to resolve          → referential failure
redacted attachment absent from manifest             → silent-omission failure
same (input, context) on second machine ≠ digest     → collapse incident (§46.5)
IEEE-754-normalized money accepted                   → canonicalization failure
```

> The bundle's promise is small and exact: *these facts, this context,
> this verdict, this trace, these gaps.* Everything larger than that is a
> claim the container refuses to make on your behalf.
