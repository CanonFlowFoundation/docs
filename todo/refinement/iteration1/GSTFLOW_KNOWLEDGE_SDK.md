# GSTFLOW_KNOWLEDGE_SDK.md — The K in Flow(GST): Sources, Lineage, and the Source-to-Rule Protocol

> *The weakest links are not F#, Fable, Avalonia, decimals, or packaging.
> They are authority selection, amendment tracking, effective dates,
> jurisdiction, fact completeness, interpretation review, and evidence
> custody.*

- **Status:** `DESIGNED` — architecture baseline; not an implementation claim
- **Snapshot:** source 2026-07-15 · cherry-pick pass 2026-07-18
- **Authority:** subordinate to `CANONFLOW_BASE_v2.md`; this is a **Flow(GST)
  domain document** — per §2 ownership, the generic machinery here
  (source-artifact schema, lockfile pattern, extraction grades) stays
  GSTFlow-owned until a second flow uses it unchanged
- **Not legal advice.** Engineering and research plan only.

Sections marked **▲** are cherry-pick corrections or doc-set bindings.

---

## 0 · THE ONE-SENTENCE MODEL

GSTFlow is not a large collection of tax `if` statements. It is a replayable
chain:

```text
Official bytes
  → identified instrument and legal lineage
  → anchored extraction
  → reviewed interpretation
  → typed proposition / Candidate Rule IR
  → independently derived examples and falsifiers
  → Crucible evidence
  → approved, signed rule pack
  → deterministic GSTFlow verdict with citations
```

This is Base §12's authoring pipeline extended **upstream** — before "Source
material" there is source *acquisition, identity, and lineage*, and this
document governs that missing prefix.

---

## 1 · ▲ RECONCILIATION CORRECTIONS (applied on arrival)

Four collisions with the existing doc set, each fixed before adoption:

```text
1. gstref:// GRAMMAR SUPERSESSION.  The invoice profile and Base v2 §18
   registered gstref:// with short-form examples
   (gstref://cgst-act/2017/s170).  This document defines the richer
   normative grammar:

     gstref://<jurisdiction>/<authority-profile>/<instrument-type>
             /<identity>@<version>#<anchor>

   The long form carries jurisdiction, version pinning, and anchors — all
   things the temporal model (§9) requires.  The long form is NORMATIVE;
   earlier short-form examples are superseded.  ADR candidate against
   Base v2 §18.

2. PROFILE NAMING.  CFF-SOURCE/1 etc. violate the established dotted style.
   Renamed on arrival: cff.source/1 · cff.interpretation/1 · cff.case/1 ·
   cff.rulepack/1 · cff.verdict/1.  Further: cff.verdict/1 is a profile
   FAMILY and cff.invoice-evidence/1 is its first concrete member — one
   subject kind, same layout laws.  No duplicate verdict profile is
   created.

3. STATUS VOCABULARY.  The source's coverage ledger used NOT_SUPPORTED as a
   capability status.  §31 has no such status.  Corrected: capability
   status ∈ {STUBBED, DESIGNED, DORMANT, …} per §31; NotSupported is an
   OUTCOME/EVIDENCE term (§6, §8.7) about a rule family at verdict time.
   A ledger row says `DESIGNED`; a verdict says `NotSupported`.

4. ConflictingAuthority.  The conflict protocol emits it as if it were an
   outcome constructor.  §6's algebra is closed; corrected:
   ConflictingAuthority is a LegalIssue CLASS carried inside
   RequiresProfessionalReview(LegalIssue) — no seventh DU case.
```

---

## 2 · AUTHORITY LADDER — WHAT EACH SOURCE CAN PROVE

"Official" is not enough. An official website hosts convenience
compilations, meeting recommendations, and manuals alongside binding
notifications — different artifacts with different legal effect.

| Rank | Artifact | Permitted use | Main trap |
|---:|---|---|---|
| A0 | Constitution and constitutional amendments | legislative competence, GST institutional structure | using a summary instead of enacted text/judgment |
| A1 | Act, amendment Act, commencement provision/notification (Gazette) | primary statutory proposition within jurisdiction and effective interval | reading an amendment without commencement or later amendments |
| A2 | Rules, rate/exemption/RCM notifications, orders, corrigenda under statutory power | delegated-law proposition, subject to enabling Act and dates | missing principal instrument, supersession, proviso, retrospective effect |
| A3 | Binding judgment/order of competent court/tribunal | interpretive effect classified by forum, jurisdiction, date, finality, issue | treating headnotes, interim orders, fact-specific holdings as universal |
| A4 | CBIC circular/instruction under identified power | administrative interpretation within lawful scope | treating a circular as overriding Act/rules/judgment |
| B1 | GST Council recommendation, minutes, fitment, press release | legislative history, policy intent, change radar | a recommendation is not an operative rate/rule |
| B2 | Portal/GSTN/NIC advisory, manual, schema, error list | operational behavior for a named version/date | portal behavior does not amend statute; manuals go stale |
| C1 | ICAI IDTC and reviewed professional commentary | education, issue discovery, candidate interpretations | secondary interpretation, never the Gold oracle |
| D | Vendor blog, social media, AI answer | discovery lead only | link rot, marketing, hallucination |

### 2.1 Binding rules (verbatim keep — the ≠ discipline)

```text
GST Council recommendation ≠ operative law
Portal validation           ≠ statutory prohibition
ICAI explanation            ≠ primary authority
PDF filename                ≠ document identity
Digital signature           ≠ legal correctness
Current engine output       ≠ test oracle
AI confidence               ≠ evidence
```

▲ This ladder is the **domain-K instantiation of Base §3's abstract oracle
hierarchy**: Authority = A0–A2 (and A3 within its binding scope) · reviewed
interpretation record = §10 here · worked example / hand calculation /
invariant / prior output rank below exactly as §3 orders them. One
hierarchy, now with named rungs for GST. Where a Council recommendation is
later implemented, link the two records but preserve them separately —
*Mohit Minerals* (SC 2022) is required reading on the character of Council
recommendations, and a project summary must not replace the judgment.

### 2.2 Conflict protocol

```text
1. Stop rule publication; never silently choose the convenient text.
2. Confirm identity, version, issue/publication/effective/retrospective
   dates, jurisdiction, subject.
3. Reconstruct amendment/supersession/corrigendum lineage.
4. Check the enabling provision and relevant judgments.
5. Record both readings in an interpretation issue.
6. Obtain independent statutory review.
7. Emit Unknown or RequiresProfessionalReview(ConflictingAuthority)
   until resolved.                                    ▲ (§1 correction 4)
```

---

## 3 · KNOWLEDGE MAP AND ROLE DEPTH

Eighteen knowledge domains (constitutional structure · source hierarchy ·
supply · jurisdiction/tax type · time and value · rates/classification ·
registration/identity · documents/e-invoice · ITC · returns/ledgers · e-way
bill/movement · refunds/exports/imports · administration/dispute ·
accounting/reconciliation · exact computation · evidence engineering ·
security/privacy · product communication), each with a "minimum
understanding / why needed / evidence of readiness" triple in the source
document. No single person masters every column; the **project** must, via
named custodians (§29).

The load-bearing governance column is role depth:

| Role | Deep in | Must not approve alone |
|---|---|---|
| Statutory custodian | authority, amendments, effective dates, interpretation | own authored interpretation |
| Technical custodian | domain model, exact computation, host agreement | legal correctness of an interpretation |
| Corpus custodian | licensing, privacy, source identity, anchors, Gold quality | rule semantics without statutory review |
| Security custodian | hostile files, extraction, signing, keys, revocation | release or legal interpretation alone |
| Release custodian | reproducible build, proof manifest, channels | substantive rule meaning |
| Channel developer | intake, explanations, offline UX, failure states | conversion of portal behavior into law |

▲ Readiness evidence per domain ("classify ten mixed documents without
promoting a lower source") is exactly the §34.3 invariant-binding pattern
applied to *people*: every claimed competence names its discharging test.

**Learning stages** (each with an exit gate, no runtime code before Stage 2):

```text
S0 classify before calculate    exit: populate a source record without
                                calling a press release "the rule"
S1 core GST map by dependency   exit: one-page concept graph, every edge
   (person → supply → POS →     backed by a source family
    time → value → rate →
    documents → ITC → returns)
S2 ONE narrow vertical E2E      exit: excluded families return
                                NotSupported/NeedsEvidence, never guesses
S3 rates/classification/docs    exit: a rate result carries entry,
                                condition, interval, evidence — never "18%"
S4 ITC and reconciliation       exit: unknown facts never auto-Pass
S5 movement/returns/refunds/    exit: no family SUPPORTED before its own
   cross-regime families        full gate set (Base Gate 3 repeated)
```

---

## 4 · SOURCE MAP AND THE PUBLISHER/AUTHORITY SPLIT

Seed indexes: **CBIC GST** (statutory/administrative index — beware
consolidated convenience copies), **GST Council** (policy intent — never
activate a recommendation before locating the implementing instrument),
**ICAI IDTC** (index/link by default; record copyright; do not redistribute
publications merely because they are downloadable), **GST Portal**
(operational truth for a named version/date). Must-add families: Gazette of
India (the authoritative bytes) · India Code · State/UT gazettes and
commercial-tax departments · Supreme Court · High Courts/eCourts · AAR/AAAR
· GSTN · NIC e-way-bill developer portal · Customs/ICEGATE · DGFT · RBI
(only where a rule requires a governed rate) · PIB (discovery only).

**▲ The structural law — never one undifferentiated "official PDF"
bucket:**

```text
SourceRecord = publisher ⊕ authority_profile      (two fields, always)
```

A CBIC-hosted press release and a Gazette notification are both official;
they justify different claims. A portal manual can be the best evidence of
a JSON workflow while being incapable of changing a statutory entitlement.
This is Base §3's provenance discipline given a schema field.

---

## 5 · THE CORPUS IS A SOURCE SDK, NOT A PDF DUMP

"Complete dump" means a complete **manifest of scope and provenance** —
never crawling every downloadable file into Git.

Collection modes:

```text
INDEX_ONLY            identity + metadata + license, no copied bytes
                      (ICAI material; unstable portals; restricted docs)
LOCKED_SNAPSHOT       original bytes, content-addressed, + digest
                      (Gazette/CBIC artifacts a reviewed rule needs)
EXTRACTED_REFERENCE   snapshot + page/block extraction + anchors + report
                      (every source cited by an interpretation or oracle)
RELEASE_EMBEDDED      minimal licensed excerpt inside a CFF
                      (only when redistribution is allowed AND necessary;
                       default is digest + locator)
```

Repository shape (▲ ADR candidate: add `/knowledge` to Base §33):

```text
knowledge/
  registry/    authorities · jurisdictions · instruments · source-index
               · sources.lock.json
  schemas/     source-artifact · legal-lineage · extraction
               · interpretation · citation
  policies/    authority · acquisition · copyright · retention/privacy
  extracts/    <artifact-id>/ pages · blocks · tables · report
  interpretations/  cases/{candidate,reviewed,gold,falsifiers}
  fixtures/    hostile-documents · extraction-regression
  generated/   timelines · coverage · link-health
objects/sha256/2c/9e/2c9edc….pdf     (outside Git / governed store)
```

Raw bytes are **immutable**: a changed file at the same URL creates a new
artifact version plus a `same_locator_changed_bytes` event — §46.4's
append-only ledger applied to legal sources. Acquisition boundaries: respect
robots/terms/copyright/rate limits · never bypass CAPTCHA or auth · record
redirects and final URL (identity is never inferred from a URL) · taxpayer
documents live in a private corpus, never in the public store or any
training set · offline import permitted with recorded custody.

---

## 6 · THE `gstref` REFERENCE SDK

▲ Normative URI grammar (supersedes earlier short forms — §1 correction 1):

```text
gstref://<jurisdiction>/<authority-profile>/<instrument-type>/<identity>@<version>#<anchor>

gstref://in/cbic/circular/233-27-2024-gst@2024-09-10#p2-para2.3
gstref://in/central/gazette/gsr-705-e@2023-09-29#notification-49-2023-ct
gstref://in/sci/judgment/2020-23083@2022-05-19#para59
```

The URI resolves **through the lockfile** — never "whatever is current"
during a reproducible build. Conceptual commands (contracts for future
automation, §43.12 style): `init · discover · add · fetch --locked ·
inspect · verify · extract · ocr · diff · lineage · anchor · cite ·
validate-interpretation · coverage · link-health --no-mutate-lock ·
export-cff`. Typed exit codes (0 verified · 2 schema/identity · 3 digest
mismatch · 4 unsafe document · 5 extraction below fidelity · 6 lineage
incomplete · 7 review/license gate missing · 8 offline-with-locked-object ·
9 unresolved conflict) — ▲ the §46.2 error families as a CLI surface.

`SourceArtifact` minimum (schema `canonflow.source-artifact/1`): identity
(instrument number, file number, dates, cited legal power) · publisher ⊕
authority_profile (§4) · acquisition (requested/final URL, retrieved_at,
method) · content (media type, bytes, sha256, pages, encrypted, embedded
files, document signature) · legal_lineage (cites/amends/supersedes/
superseded_by) · rights (owner, collection mode, redistribution) · quality
(identity status, extraction grade, interpretation status).

`sources.lock.json` pins artifact → sha256 → object path → gstref URI.
▲ **Release binding (ADR candidate):** the source snapshot digest joins the
§43.1 release-set manifest beside `trust_snapshot_digest` — a release that
cannot name the exact legal bytes it was reviewed against is not
reproducible in the sense this project means it. Nightly discovery may
propose lock updates; it may never rewrite the lock used by `stable` or an
existing proof manifest (§43's discovery-job law, applied to law itself).

---

## 7 · SAFE PDF ACQUISITION AND EXTRACTION

PDF is an untrusted container. Extraction runs outside the deterministic
kernel and cannot turn text into statutory truth without review.

```text
Discover locator → fetch in network sandbox → hash and identify bytes
→ static safety inspection → text/layout extraction → OCR only where
required → anchors + quality report → HUMAN identity review
→ interpretation and cases
```

▲ This is Base §46.1's boundary-promotion ladder instantiated for legal
sources: `UntrustedBytes → BoundedBytes (limits) → ParsedWire (extraction)
→ ConstrainedInput (anchored blocks) → VerifiedArtifact (identity review) →
ReplayableInput (locked + cited)`. Same law, new boundary. Controls:
allowlisted hosts · redirect/MIME/magic/size checks · SHA-256 before
parsing · parser process with no network/credentials/home access ·
CPU/time/memory/page/pixel/object/decompression limits · detect encryption,
JavaScript/actions, embedded files, malformed xref tables · embedded files
only via separate quarantine · **preserve raw bytes, never "repair" in
place** · deterministic extraction with tool/version recorded · OCR only
for image-only regions, with confidence, language, model version, and page
image digest preserved.

Extraction fidelity grades (▲ = §34.1 classified fidelity for text):

```text
A_TEXT_VERIFIED              can support interpretation (with review)
A_TEXT_WITH_LAYOUT_WARNINGS  after targeted visual check
B_OCR_REVIEWED               with explicit OCR provenance
C_CANDIDATE                  discovery and test ideation ONLY
D_UNUSABLE                   no
```

Anchor preference order: instrument number + paragraph/rule/section >
printed page + paragraph > PDF page + normalized block fingerprint >
generated line number (convenience pointer, never a durable legal
citation). Every block stores pdf_page, printed_page, heading,
paragraph_label, normalized-text digest, bounding box.

**AI boundary:** AI may propose headings, citations, relations, conditions,
examples, boundary values, contradictions. It may not silently correct OCR,
invent missing provisos or table cells, declare a source authoritative,
decide which interpretation is law, promote an extraction to Gold, or
publish/sign a pack. All AI output is `Guessed` until an accountable
reviewer confirms it against preserved evidence — Base §3/§15 verbatim, at
the source layer.

---

## 8 · WORKED SPECIMEN (the shape of a real record)

CBIC Circular 233/27/2024-GST, locked: official locator · instrument +
file number · date 2024-09-10 · 480,068 bytes · sha256 `2c9edc…b1867` ·
PDF 1.7, 2 pages, unencrypted, no embedded files, no JS · **no embedded PDF
signature — which by itself says nothing about legal publication or issuer
identity** · extractor poppler 24.02.0 pinned.

The candidate extraction of paragraph 2.3 yields detected conditions
(named Customs notifications used at import · later payment of IGST + cess
· interest paid · Bill of Entry reassessed) and a detected conclusion (the
described refund treated as not contrary to rule 96(10) *for that stated
situation*) — all at `CANDIDATE_INTERPRETATION`, `rule_eligible: false`.

Before any code: the reviewer must read 2.3 with 1/2.1/2.2, s.168(1), rule
96(10) for the period, Notification 16/2020-CT, both Customs notifications,
amendments, retrospective effect, later judicial treatment, and the
taxpayer facts. **This is exactly why "PDF text → F# rule" is unsafe** —
the specimen exists to make the gap between extraction and law physically
visible in the repo.

---

## 9 · LEGAL LINEAGE AND THE TEMPORAL MODEL

Never collapse into `date`:

```text
issued_on · published_on · assented_on · commenced_on · effective_from
· effective_until · deemed_effective_from · retrieved_at · reviewed_at
· pack_published_at · transaction_time / tax_period
```

▲ This extends Base §10's five-date list; ADR candidate to adopt the fuller
vocabulary there. Instrument relations: `ENACTS AMENDS SUBSTITUTES INSERTS
OMITS RESCINDS SUPERSEDES CORRECTS COMMENCES EXTENDS CLARIFIES CITES
IMPLEMENTS INTERPRETS STAYS OVERRULES` — and `CLARIFIES` is a recorded
*claim by the issuing artifact*, not proof a court will treat it as
clarificatory or retrospective.

The applicability predicate:

```text
Applicable(rule, case) =
    jurisdiction_matches ∧ subject_matches
  ∧ effective_interval_contains(case.legal_time)
  ∧ commencement_satisfied
  ∧ ¬superseded_for_that_interval
  ∧ required_facts_observed_or_declared
```

Overlapping versions or a missing effective date = **blocking lineage
defect, never "use latest."**

▲ **Proof-annex binding:** interval containment, disjointness, and
overlap/cycle detection over epoch days are precisely the LIQUID annex's
decidable fragment — `VERIFY-INTERVAL` obligations over the lineage graph,
dischargeable at Gate 6 strength with no Class D interpreter. ▲ **CDC
binding:** the promotion machine `Discovered → Locked → Extracted →
Reviewed → Interpreted → CandidateRule → ApprovedPack | Rejected →
Superseded` is a transition system; its governance laws are executable cage
formulas — `¬Reviewed → [U*] ¬ApprovedPack` and `¬StatutoryReview →
[extract;interpret]* ¬RuleEligible` — CDL law candidates the moment the
engine wakes. The statuses of source, extraction, interpretation, rule, and
pack are **separate**: an authoritative Gazette file can have a bad
extraction; a perfect extraction a disputed interpretation; a signed pack a
later supersession.

**Oracle rule (verbatim keep):** expected outputs come from a traced
authority/reviewed interpretation and an independent calculation. Never run
GSTFlow and save its output as Gold.

---

## 10 · CFF KNOWLEDGE PROFILES (▲ renamed, §1 correction 2)

| Profile | Purpose | Prohibited content |
|---|---|---|
| `cff.source/1` | portable source snapshot/index | interpretations presented as source text |
| `cff.interpretation/1` | reviewable legal analysis package | executable verdict code; unreviewed AI promoted to fact |
| `cff.case/1` | reproducible case/corpus | uncontrolled taxpayer PII |
| `cff.rulepack/1` | executable bounded rules | arbitrary F#/DLL/script; unstated network dependency |
| `cff.verdict/1` (family) | replayable evaluation — `cff.invoice-evidence/1` is its first member | claim of filing/adjudication success without acknowledgement |

If bytes cannot lawfully be redistributed, omit the payload and carry the
official locator + identity metadata + digest of the reviewed copy + a
`payload_omitted` reason — ▲ which is precisely the gap-analysis G16
external-attachment mechanism plus the G15 privacy field, arriving from the
legal side with the same shape. One manifest law serves both.

**Separation law (the profile system's spine):**

```text
source bytes    ≠ extracted text
extracted text  ≠ interpretation
interpretation  ≠ executable rule
signature       ≠ authority
verdict         ≠ filing or adjudication
```

Each layer has its own digest and reviewer state. CFF signs the exact
bundle; it never collapses the layers.

---

## 11 · TESTING THE KNOWLEDGE LAYER

**Source-SDK tests:** every locked artifact resolves to its recorded
SHA-256 · same-URL byte drift is detected and quarantined · HTML error
pages renamed `.pdf`, truncation, zero-byte files fail · dangerous
actions/attachments fail safely within resource limits · extraction is
deterministic for a pinned toolchain · every anchor resolves to the same
artifact/page/block fingerprint · OCR-derived material is visibly labeled
and cannot silently replace native text · lineage has no unexplained
overlap, cycle, or missing commencement · schemas reject unknown
authority/status promotion · link-health reports drift **without mutating
stable locks**.

**Statutory case design — per proposition:**

```text
one ordinary positive · one ordinary negative · every boundary value/date
· one missing-fact · one conflicting-evidence · one exception/proviso
· one wrong-jurisdiction · one superseded-version replay
· one mutation a naive implementation would accept
```

▲ That last item is Base §14.3's mutation discipline authored at
*interpretation time* — the falsifier is designed by the person who read
the law, before any implementation exists to flatter.

**Cross-host evidence** compares outcome kind, exact decimal strings,
evidence paths, effective rule IDs, unknowns, and canonical digest — never
only a Boolean. **Portal/API contract tests** (Pact-style, offline stateful
mocks, sanitized fixtures by schema version, separately authorized sandbox
smoke) prove the *adapter*, never the external service or the law — mocks
never become statutory oracles.

---

## 12 · COVERAGE LEDGER (▲ status-corrected)

| Capability family | Sources | Interp. | Gold/fals. | Engine | Agreement | Custodian | §31 status |
|---|---:|---:|---:|---:|---:|---:|---|
| GSTIN syntax/check character | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | `STUBBED` |
| Ordinary B2B goods invoice arithmetic | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | `STUBBED` |
| Intra/inter-State classification | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | `STUBBED` |
| Place of supply | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | `DESIGNED` |
| Rates/exemptions/RCM | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | `DESIGNED` |
| ITC preflight | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | `DESIGNED` |
| E-invoice schema/QR preflight | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | `DESIGNED` |
| E-way bill preflight | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | `DESIGNED` |
| Returns/reconciliation | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | `DESIGNED` |
| Refund/export/SEZ | ☐ | ☐ | ☐ | ☐ | ☐ | ☐ | `DESIGNED` |

Template values, to be reconciled with repository evidence before
publication. **A checked source column never automatically checks a rule
column** — the columns are the §10 promotion states made visible.

---

## 13 · CUSTODY, WATCHERS, AND THE URGENT PATH

Approval matrix highlights: index-only record = corpus custodian alone ·
locking an artifact a rule uses = corpus + statutory · interpretation
approval = independent statutory reviewer, **author cannot self-approve** ·
signed pack = statutory + technical + release + security gate. Watchers
(CBIC indexes, Gazette patterns, Council releases, portal/NIC advisories,
ICAI index, court decisions, dead links, same-URL drift) open review
proposals and **change nothing** — not packs, not Gold, not stable locks.

Urgent midnight-circular protocol: `DISCOVERED` never jumps to active →
locate the operative instrument and Gazette identity → reconstruct
effective date → interpretation + impact diff → red tests +
affected-period replay → normal approval/sign/release gates → users see
snapshot, effective time, pack version, known gaps. **Speed comes from
prepared machinery, not skipped review.**

---

## 14 · ▲ EXECUTION BINDING — ONE PROGRAM, NOT TWO

The source's 90-day plan is sound but must not become a second gate
program beside Base §36. Bound as the K-side elaboration of existing
gates, day counts demoted to guidance (Pipeline law: exit evidence over
time estimates):

```text
Days 1–7   custodians, ladder, five schemas, gstref convention,
           seed indexes, specimen ingestion, policies
           = Gate 0/2 K-side work; exit: a reviewer reproduces hashes
             and anchors from a clean environment
Days 8–21  the B2B goods-invoice vertical end to end
           = Gate 3 EXACTLY (do not run a parallel vertical); exit:
             changed amount/State/date/missing fact/rule version each
             produce the independently expected outcome
Days 22–45 gstref automation + hostile PDF corpus + lineage validation
           + AI candidates behind review + CI fails on unpinned source
           = Gate 2 hardening; exit: no unreviewed extraction or mutable
             URL can enter Gold or a pack
Days 46–60 operational contracts (portal/NIC mocks, versioned schemas)
           = Gate 4 adapter work; exit: UI distinguishes statutory vs
             operational vs uncertain findings offline
Days 61–90 Studio source/anchor/interpretation workflow, lineage
           visualization, cff.source/interpretation/case exports,
           second statutory reviewer onboarded
           = Gate 7 preparation; exit: an author can propose a bounded
             rule and cannot self-approve, sign, or widen capability
```

**Definition of done** (v1): authority profiles documented ∧ source
identity/schema/lock machine-validated ∧ raw bytes immutable and safely
parsed ∧ every legal assertion anchored ∧ extraction and interpretation
separately reviewed ∧ amendments/effectivity/jurisdiction modeled ∧ one
vertical with independent Gold and falsifiers ∧ .NET/Fable exact agreement
∧ CFF replays snapshot + facts + pack + verdict ∧ licenses/privacy/
unsupported scope visible ∧ **no AI, portal behavior, convenience copy, or
current engine output treated as authority**.

---

## 15 · IMPORT FILTER AND DELTAS

§46.7: DomainNeutral — **split**: the ladder ranks, GST source map, and
learning stages are K-owned; the source-artifact schema, lockfile pattern,
extraction grades, and separation law are generic *candidates* that remain
GSTFlow-owned until EDIFlow uses them unchanged (§2) · Enforceable ✓
(schemas, lock resolution, grade gates, CI rules) · Falsifiable ✓ (§11's
fixture families; drift detection) · NonDuplicative ✓ after §1's four
corrections · ReducesFailureOrAmbiguity ✓ (publisher/authority split;
date vocabulary; grade gates) · RespectsOwnership ✓ (nothing here touches
the verdict path; extraction runs outside the kernel).

ADR candidates: gstref long-form grammar supersedes §18 short forms ·
`source_snapshot_digest` joins §43.1 release sets · `/knowledge` joins §33
· §10 date vocabulary extension · `cff.source/interpretation/case/rulepack
/verdict` profile family registration (with invoice-evidence as first
verdict member) · lineage `VERIFY-INTERVAL` obligations join the Gate 6
proof set.

---

> The correct ambition is not "put all GST knowledge into the engine." It
> is: make every supported conclusion traceable to the exact sources,
> interpretation, facts, dates, jurisdiction, code, tests, approvals, and
> bytes that produced it — and make every unsupported conclusion fail
> honestly. That is the CFF advantage: a changing body of law, policy,
> administrative interpretation, and portal behavior becomes portable,
> content-addressed, reviewable evidence — without pretending those layers
> are the same thing.
