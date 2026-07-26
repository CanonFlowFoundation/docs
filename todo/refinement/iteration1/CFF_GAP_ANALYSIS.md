# CFF_GAP_ANALYSIS.md — What the Doc Set Still Leaves Unspecified for CFF v1

> *Everyone wrote "deterministic ZIP." Nobody yet wrote which bytes make it
> deterministic.*

- **Status:** `DESIGNED` — audit + proposed law text; primary input to work
  packet `CFF-0001` (Pipeline train item 1) alongside
  `CFF_INVOICE_EVIDENCE_PROFILE.md`
- **Audited:** Base v2 §18/§43.8/§46.1/§46.5 · invoice-evidence profile ·
  Pipeline train items 1–14 · Gate 5 exit criteria
- **Snapshot:** 2026-07-18

---

## 0 · METHOD AND WHAT IS **NOT** MISSING

Audit question: could an engineer freeze `CFF_V1_SPEC.md` tomorrow from the
existing documents alone, and could a hostile reviewer break the result?

Confirmed covered (no action): logical layout with `evaluation/` and
`proof/` · acyclic digest model · evaluation-context tuple · envelope purity
(messageKey-only) · explicit redaction · `gstref://`/`fact://` registration ·
evidence-vs-proof modal split · Avro/signature deferral with conditions ·
L0–L3 import levels · channel-crossing matrix · type-state ladder
(`UntrustedArchive → ReplayableCff`) · manifest field list · atomic
finalization (§46.5 generally) · storage evolution gates.

What follows are the holes. Ranked: **F** = blocks the spec freeze (the spec
cannot be written without this decision) · **P** = required before Gate 5
exit / `PROVEN` · **D** = defer, but name it now so deferral is a decision.

---

## 1 · GAP REGISTER

| # | Gap | Rank | One-line consequence if ignored |
|---|---|---|---|
| G1 | Canonical identity undefined: payloadDigest vs container-byte digest | **F** | §43.8/§43.11 say "CFF digest" ambiguously; two implementations will disagree about what a CFF *is* |
| G2 | Byte-level deterministic ZIP profile (timestamps, ordering, compression, flags) | **F** | "same input ⇒ same bytes" is unfalsifiable because "same bytes" was never constructed |
| G3 | Full canonical JSON profile — decimals were pinned, everything else was not | **F** | key order, escapes, timestamps, null-vs-absent each silently fork the digest |
| G4 | Entry path grammar | **F** | case collisions, unicode tricks, and 300-char paths pass "path safety" because safety was never a grammar |
| G5 | Media type, extension, and magic identification | **F** | nothing on disk says "this is a CFF" without opening it; sniffers and OSes guess |
| G6 | Parser-differential law (central directory vs local headers) | **F** | the classic signed-container bypass: verify what the directory says, extract what the header says |
| G7 | Bundle subject scope: one evaluation vs batch | **F** | batch preflight (§35.3) has no container story; the first professional user hits it |
| G8 | Failed-evaluation bundles | **P** | a normalization failure produces no evidence artifact, contradicting §46.1's promise |
| G9 | Golden reference vectors with pinned digests | **P** | cross-runtime proof (train item 8) has no fixed point to agree on |
| G10 | Resource-budget schema with concrete defaults | **P** | "size/count limits" is a requirement with no numbers; every reader invents its own zip-bomb posture |
| G11 | Typed import-rejection taxonomy per level | **P** | L0–L3 failures collapse into strings; §46.2 error families stop at the CFF border |
| G12 | Writer byte-reproducibility as a distinct test | **P** | replay tests the verdict; nothing tests that the *writer* is deterministic |
| G13 | Import dedup by payloadDigest | **P** | same bundle imported twice = duplicate workspace records (§27 dedup never applied to CFF) |
| G14 | Per-entry criticality (must-understand) flag | **P** | "unknown optional material is preserved" (§43.8) is undecidable without knowing which entries are optional |
| G15 | Privacy classification mechanics per entry | **P** | §18 requires "privacy classification" with no field, no vocabulary, no crossing interaction |
| G16 | External by-reference attachments (digest without bytes) | **D** | large PDFs force a choice the spec hasn't made; today's only tool is redaction, which means something else |
| G17 | Cross-bundle references (`cffref://`) | **D** | reconciliation evidence (§21) spans documents; no way to cite another bundle |
| G18 | Batch index profile | **D** | follows G7's decision |
| G19 | Counter-signing / signature ordering | **D** | v1 should say "parallel over payloadDigest only" out loud |
| G20 | Encrypted-bundle profile | **D** | §27 optional encryption never met §18; fine — but say so |

---

## 2 · G1 — THE IDENTITY LAW (the load-bearing ambiguity)

§43.8 puts a digest in the manifest; §43.11 forbids moving "the CFF digest";
the invoice profile defines `payloadDigest`. Which one names the bundle?
Proposed law:

```text
CanonicalIdentity(CFF) = payloadDigest
                       = SHA-256(exact canonical bytes of manifest.json)

ContainerDigest(CFF)   = SHA-256(archive bytes)
                       = a TRANSPORT check, never the identity
```

Rationale: `payloadDigest` is a function of content + canonicalization
profile only. `ContainerDigest` additionally depends on the ZIP encoder —
two conforming writers could differ at the container layer during v1 without
changing what was evaluated or signed. Signatures, replay claims, registry
records, dedup, and §43.11 immutability all bind to `payloadDigest`.
Byte-identical containers (G2) remain a conformance goal — writers must
achieve it — but identity must not *depend* on it, or a zlib version bump
becomes a semantic event. Two digests, two jobs, named apart — the §18
"digest is not signature" discipline applied one level down.

---

## 3 · G2 — DETERMINISTIC ZIP PROFILE `cff-zip/1`

The nondeterminism sources in ZIP, each pinned:

```text
entry order          = byte-lexicographic by path, in both local records
                       and central directory; mimetype entry first (G5)
compression          = STORE (method 0) for every entry in v1
                       (deflate output varies by encoder/version/level —
                        canonical JSON is small; buy determinism with bytes)
timestamps           = fixed constant 1980-01-01 00:00:00 DOS time, every
                       entry (wall-clock provenance lives in the verdict
                       record per §10/§18, never in container metadata)
extra fields         = none (no UT/unix/NTFS extras)
external attributes  = 0 · no directory entries · no symlinks
filename encoding    = UTF-8, EFS bit set; names constrained by G4 grammar
data descriptors     = not used (sizes/CRC in local header)
archive comment      = empty · per-entry comments = empty
zip64                = forbidden in v1 (bounded by G10 budgets); a bundle
                       needing zip64 is over budget by definition
encryption           = ZIP-level encryption forbidden (G20 is a separate
                       profile if ever)
```

Under this profile, `same logical content ⇒ same container bytes` becomes
an executable property (G12), not an aspiration.

---

## 4 · G3 — CANONICAL JSON PROFILE `cff-json/1`

`cff-json-decimal-string/1` pinned monetary decimals; the rest of the JSON
surface was left to serializer defaults. The full profile:

```text
encoding             = UTF-8, no BOM; content is NOT unicode-normalized
                       (bytes are preserved as authored; normalization is a
                        transformation and transformations report fidelity)
object keys          = unique (duplicate key ⇒ parse rejection, never
                       last-wins) · sorted by UTF-8 byte order
whitespace           = none (no spaces, no newlines) · file ends without
                       trailing newline
strings              = minimal escaping: only " \ and control chars; no
                       \uXXXX for printable characters
numbers              = integers only as JSON numbers (i64 range); every
                       decimal quantity = string under decimal-string/1;
                       floats forbidden EVERYWHERE in canonical content,
                       not only in monetary fields
timestamps           = RFC 3339 UTC, 'Z' suffix, exactly milliseconds
                       ("2026-07-18T10:42:11.000Z") — one width, one zone
null vs absent       = null is FORBIDDEN in canonical content; optionality
                       = field absence (two representations of "nothing"
                       is a digest fork waiting to happen)
empty collections    = [] and {} permitted and distinct from absence;
                       a profile schema states which is meaningful where
```

The digest of canonical content is well-defined only after this profile
exists. Every claim in G1 stands on it.

---

## 5 · G4 — PATH GRAMMAR

"Path safety" becomes a grammar:

```text
path      = segment *( "/" segment )        ; forward slash only
segment   = 1*63( %x61-7A / %x30-39 / "-" / "." / "_" )   ; lowercase
            ; no segment of "." or ".."; no leading "-"
max path  = 180 bytes · max depth = 6
forbidden = uppercase (case-collision surfaces on case-folding
            filesystems) · unicode (homoglyph/normalization ambiguity in
            an identity-bearing string) · absolute paths · backslash ·
            empty segments
uniqueness= exact byte comparison; ANY duplicate path in local records or
            central directory ⇒ L0 rejection (feeds G6)
```

Lowercase-ASCII-only is deliberately austere: paths participate in digests
and signatures, so every ambiguity a filesystem or normalizer could
introduce is excluded at the grammar rather than handled downstream.

---

## 6 · G5 — IDENTIFICATION: MIME, EXTENSION, MAGIC

The EPUB/ODF precedent applied:

```text
extension   = .cff
media type  = application/vnd.canonflow.cff+zip   (register the vendor tree)
magic       = first archive entry is exactly "mimetype": STORED,
              uncompressed, containing the media-type string — so bytes
              30–~70 of the file identify it without ZIP parsing
manifest    = declares the same media type; mismatch ⇒ L1 rejection
```

The `mimetype` entry is excluded from manifest entries (like
`manifest.json` and `signatures/**`) since it precedes parsing; its content
is fixed by the format version, so nothing is lost from the digest.

---

## 7 · G6 — THE PARSER-DIFFERENTIAL LAW

The classic attack on signed archives: the central directory and the local
file headers describe different content; the verifier walks one, the
extractor walks the other; the signature covers what was verified while the
victim reads what was extracted. Law:

```text
∀ entry : central-directory record ≡ local header
          (path, sizes, CRC, method, flags, offsets consistent)
overlapping entry data ranges                    ⇒ L0 rejection
gaps / trailing data outside declared structures ⇒ L0 rejection
bytes before the mimetype entry                  ⇒ L0 rejection
two readers (directory-driven and header-driven) MUST extract identical
bytes for every entry — this is a product-state agreement test (CDC §13
shape) and a mandatory hostile fixture family
```

This belongs beside the digest model in the spec's security section, not in
a test appendix: it is the reason `VerifiedEntries` can be trusted at all.

---

## 8 · G7 + G8 — SUBJECT SCOPE AND FAILED EVALUATIONS

**Scope law (G7):**

```text
one CFF = one evaluation of one canonical subject
          (one document, or one declared document set evaluated as a unit,
           e.g. invoice + its e-way bill in a single verify call)
batch preflight ⇒ N bundles, one per subject
batch index    ⇒ deferred profile (G18): an index bundle whose facts are
                 payloadDigest references to member bundles — never a
                 mega-bundle that re-embeds them
```

One subject keeps identity, signing scope, redaction, and privacy
classification decidable per bundle.

**Failure bundles (G8):** §46.1 promises evidence for every promotion
attempt, but the profile only describes successful evaluations. Law: an
evaluation that fails before a verdict (parse rejection, normalization
failure, pack incompatibility) MAY still emit a conforming bundle with
`verdicts/verdict.json` overall = the typed rejection, `evidence/
acquisition.json` populated, `facts/` possibly absent, and the absence
declared in the manifest. A crash that produces nothing is a diagnostic
path; a *rejection* is a result, and results get containers.

---

## 9 · G9–G15 — PROOF AND MECHANICS (pre-Gate-5 set)

**G9 Golden vectors.** Ship in `/schemas/cff/vectors/`: the minimal valid
bundle ("smallest CFF") with its payloadDigest and container digest pinned
in the spec text itself · one full invoice-evidence bundle · one failure
bundle (G8) · one redaction bundle. Cross-runtime agreement (train item 8)
then has fixed points; a reader that computes any other digest is wrong by
inspection.

**G10 Budget schema.** Concrete v1 defaults, declared in the spec and
overridable only downward by consumers:

```text
max entries 512 · max total uncompressed 256 MiB · max single entry 64 MiB
max manifest 4 MiB · max path 180 B · max depth 6 · compression ratio cap
n/a (STORE) · read strategy = central-directory-first, bounded memory
```

**G11 Rejection taxonomy.** Typed reason codes per level (`L0_DUP_PATH`,
`L0_HEADER_MISMATCH`, `L1_UNKNOWN_MEDIA_TYPE`, `L2_MISSING_CAPABILITY`,
`L3_UNKNOWN_SIGNER`, …) mapped onto §46.2's error families; every code has
a hostile fixture (falsifier symmetry).

**G12 Writer reproducibility.** `write(content); write(content)` on one
machine and on a second machine ⇒ identical container bytes under
`cff-zip/1` + `cff-json/1`. Distinct from verdict replay: this tests the
*writer*, and it is what makes G2 falsifiable.

**G13 Import dedup.** Workspace import keys on payloadDigest; re-import is
a no-op with an "already present" result, mirroring §27 restore dedup.

**G14 Criticality flag.** Manifest entries gain `"critical": true|false`.
A reader within the same major version MUST reject on an unknown critical
entry and MUST preserve unknown non-critical entries byte-exactly — this
turns §43.8's minor-version rule into a per-entry decision procedure.

**G15 Privacy class.** Manifest entries gain
`"privacy": "public" | "business" | "personal"`; the bundle's effective
class = max over entries; channel-crossing and the §24 privacy receipt read
this field; redaction (`redacted: true`) records *removal*, privacy records
*sensitivity* — two fields, two questions.

---

## 10 · G16–G20 — DEFERRED, BY NAME

```text
G16 external attachments   entry with digest + byteLength + "external":
                           true and no bytes; verification = fetch-and-
                           check by the CONSUMER, never the verdict path
                           (offline law §24). Distinct from redaction:
                           external = "not carried", redacted = "withheld".
                           Wake: first real corpus with >64 MiB sources.
G17 cffref://              cross-bundle citation by payloadDigest + path
                           fragment. Wake: reconciliation profile (§21)
                           activation.
G18 batch index profile    per G7. Wake: professional batch mode ships.
G19 counter-signing        v1 law stated now: signatures are PARALLEL,
                           each over payloadDigest via its own scope.json;
                           no signature covers another signature. Chained
                           attestation is a v2 discussion.
G20 encrypted bundles      §27's optional encryption never met §18. v1
                           law: encryption wraps the container (file-level,
                           user-carried); the CFF inside is a normal CFF.
                           No ZIP-native encryption ever (G2).
```

---

## 11 · FALSIFIER ADDITIONS (into CFF-0001 acceptance, extending the profile's sketch)

```text
duplicate path (central or local)                → L0 reject
central/local header disagreement                → L0 reject
overlapping or shadowed entry data               → L0 reject
bytes before mimetype entry / wrong first entry  → L0 reject
deflate-compressed entry                         → L0 reject (v1 STORE law)
nonzero or varying entry timestamps              → conformance fail (writer)
uppercase, unicode, "..", 181-byte path          → L0 reject
duplicate JSON key in canonical content          → L1 reject
JSON null in canonical content                   → L1 reject
JSON float anywhere in canonical content         → L1 reject
timestamp without ms or without Z                → L1 reject
unknown critical entry, same major               → L2 reject
unknown non-critical entry survives re-emission byte-exactly → must pass
golden vector digest mismatch                    → implementation rejected
same content, two machines, different container bytes → writer incident
```

---

## 12 · TRAIN AND ADR BINDING

```text
Train item 1 (spec freeze)   ← G1–G7 are the freeze's contested cores;
                               the spec is not freezable until each has a
                               decided sentence
Train item 3 (schemas)       ← G14 critical + G15 privacy manifest fields
Train item 4 (parser)        ← G6 differential law + G10 budgets + G11 codes
Train item 7 (digests)       ← G1 identity split
Train item 8 (replay)        ← G9 vectors + G12 writer reproducibility
Train item 13 (malicious)    ← §11 fixture families
Gate 5 exit                  ← unchanged in wording, now falsifiable in fact

ADR candidates: identity = payloadDigest (G1) · cff-zip/1 STORE decision
(G2) · null-forbidden / absence-only optionality (G3) · single-subject
scope (G7) · failure bundles (G8) · critical + privacy manifest fields
(G14/G15) · media-type registration (G5)
```

---

> The doc set built the trust architecture of CFF — digests, signatures,
> channels, type-states — and left the *bytes* to folklore. Every gap above
> is the same finding at a different layer: a determinism claim whose
> equality was never constructed. Fix the equalities and the claims become
> tests; that is the whole method, applied to its own container.
