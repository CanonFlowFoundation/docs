# LAT.md — CFF Human Knowledge Graph and Optional Agent Context Profile

> **Write the important knowledge once, keep it readable by people, and let
> tools or agents consume it without becoming its authority.**

- **Status:** `DESIGNED` — architecture and governance profile, not an implementation claim
- **Snapshot:** 2026-07-16 · integration pass 2026-07-18
- **Authority:** subordinate to `CANONFLOW_BASE.md`, accepted ADRs, reviewed constitutions, locked CFF dependency policy; `CFF_PIPELINE.md` governs how its gates execute
- **Core-stack rule:** the CFF technology selection remains sealed
- **Primary audience:** humans reviewing architecture, laws, decisions, test intent, change meaning
- **Secondary audience:** optional coding agents and other tools
- **Compiler / LLM / Headroom requirement:** none · none · none

Sections marked **▲** are integration deltas binding this profile into the
doc set (Base · Pipeline · LIQUID · DYNAMIC annexes). Everything else is the
reviewed profile, compressed without normative loss.

---

## 0 · CORRECTED VERDICT

The earlier draft coupled three independent concerns: knowledge
documentation, compiler-assisted F# development, LLM context compression.
That coupling was unnecessary.

```text
lat.md   = human-readable repository knowledge graph
         ∧ optionally machine-navigable
         ∧ independent of compiler services
         ∧ independent of generative LLMs

Headroom = optional transformation layer for context sent to an LLM
         ∧ irrelevant to a human-only workflow
         ∧ not part of lat.md correctness

FCS/FSAC = separate optional F# developer tooling
         ∧ required by neither
```

Forbidden mental model: `lat.md → FCS → FSAC → MCP → LLM`. Valid model:

```text
                       ┌→ human reader/reviewer
repository → lat.md/ ──┤
                       └→ optional tools or agent
                                  ↓
                    optional Headroom only when
                    measured LLM context pressure exists
```

F# compiler assistance belongs in a separate `F_SHARP_TOOLING.md` profile if
CFF later specifies it.

### 0.1 · ▲ The two knowledge planes — LAT vs OKF demarcation

The Base already names a Markdown knowledge surface: the **OKF projection**
(§15.4) — *generated* portable bundles emitted from the Canonical Semantic
Model with pinned spec versions and fidelity reports. This profile adds a
second, different plane. They must never merge:

```text
lat.md/  = human-AUTHORED, reviewed prose ABOUT the system
           (explanations, rationale, workflows, decisions)
OKF      = machine-EMITTED projection OF canonical semantics
           (schemas, constraints, lineage; fidelity-classified)

lat node may LINK to an OKF artifact          ✓
lat node may restate OKF content wholesale    ✗ (drift; LAT-005)
OKF emitter may generate lat nodes            ✗ (generated prose is not
                                                  reviewed knowledge)
either plane claiming the other's role        ✗ (§34.2 drift surface)
```

Two knowledge planes with one boundary each is governable; one blurred plane
is the "second domain model" landmine (§5.1 glossary rule) at repository
scale. The capability-manifest diff (§30 Base) watches both.

### 0.2 · ▲ Where this sits in the CFF v1 freeze

Pipeline §10 freezes product surfaces. This profile adds none: the graph is
repository documentation (R0), and the tooling gates below are R1 packets.
Classification: Gates 0–1 and 4 = R0/R1 documentation packets; Gate 2–3
(CLI pin, F# corpus) = one R1 **tooling packet** under Pipeline §8.4 (pinned
version + checksum, no silent update, upgrade via separate packet); Gates 5–7
= dormant, individually gated. Nothing here touches the verdict path,
release packages, or the dependency lock of shipped artifacts (Gate 0 below
proves it).

---

## 1 · SCOPE, AUTHORITY, TERMS

**Governs:** plain Markdown knowledge files · wiki-style links · links from
knowledge to source · backlinks from source comments · human review of
semantic changes · optional lat CLI checks/navigation · optional local
semantic search · optional agent access via CLI or MCP · optional Headroom
evaluation for noncritical LLM context.

**Does not specify:** F# parsing/type checking · compiler APIs · editor
autocomplete · language servers · compilation · runtime verdict logic ·
domain-rule execution · Gold truth generation · signing/release authority ·
mandatory AI usage · model training.

**Terms:**

```text
NormativeSource = CANONFLOW_BASE | accepted ADR | reviewed constitution
                | approved domain source
KnowledgeGraph  = finite set of interconnected Markdown sections under lat.md/
KnowledgeNode   = one identified section expressing a bounded concept
CodeReference   = declared path or path+symbol link from a node
Backlink        = source comment referring to a node
Agent           = optional software using an LLM or other automation
CriticalContent = law, prohibition, stop condition, exact code, diagnostic,
                  evidence, approval, signature, digest, Gold expectation,
                  security rule
SoftContent     = repeated logs, duplicate results, explanatory prose whose
                  optional transformation cannot alter an authoritative decision
```

`⊕` means structured composition, never arithmetic addition.

---

## 2 · PRIME LAWS

**LAT-001 · Human readability is primary.**
`∀ node: Readable(node, ordinaryMarkdownReader)`. No proprietary viewer,
embedding model, LLM, compiler, MCP host, or remote service required.
*Falsifier:* essential content unrecoverable from checked-out Markdown alone.

**LAT-002 · LLM independence.**
`Remove(AllLLMs) ⇒ Read ∧ Edit ∧ Review ∧ Version` of the graph. An LLM may
help navigate or propose; it is never required.

**LAT-003 · Compiler independence.**
`Remove(FCS ∪ FSAC ∪ LanguageServers ∪ Compilers) ⇒ Read ∧ Edit ∧ Review`.

**LAT-004 · Tool removability.**
`Remove(latCLI ∪ MCP ∪ EmbeddingModel ∪ Headroom ∪ Agent) ⇒ workflow usable`.
Plain files are the durable asset; indexes, caches, vectors, expansions,
sessions, compressed context are disposable.

**LAT-005 · Projection is not authority.**
`KnowledgeGraph = ReviewedProjection(NormativeSources ⊕ Architecture ⊕ Code)`
and `KnowledgeGraph ≠ NormativeSources`. On conflict, the governing source
wins and the node is `STALE` until corrected.

**LAT-006 · Referential checks are bounded evidence.**
`latCheckPass ⇒ DeclaredReferentialChecksPassed` and
`latCheckPass ⇏ DomainTruth ∨ CodeCorrectness ∨ TestSufficiency ∨
FSharpTypeCorrectness ∨ ReleaseReadiness`.
▲ *Modal reading (DYNAMIC annex §2):* `lat check` discharges a **referential
box** over declared links only — a passing check is `[check]LinksResolve`,
never a claim about any other proposition. Tag it so in evidence.

**LAT-007 · Agent non-authority.**
`AgentOutput = Candidate ≠ AcceptedKnowledge ≠ AcceptedLaw`. Human review or
an explicitly authorized governance process accepts.

**LAT-008 · Headroom is optional and downstream.**
`HeadroomEnabled ⇒ LLMEnabled`; `Remove(Headroom) ⇒ latWorkflowUnchanged`.
It never transforms the stored graph or authoritative files.

**LAT-009 · Critical content remains exact.**
`∀ x ∈ CriticalContent: LossyCompress(x) = forbidden`. Exact selection,
stable ordering, byte-preserving deduplication, explicit field projection
allowed. A lossy summary may accompany critical content, never replace it.

**LAT-010 · Claimed savings require CFF evidence.**
Adopt an optimization only with same corpus ∧ same task ∧ same model where
applicable ∧ same acceptance oracle ∧ no accuracy/scope regression ∧
measured material benefit. README benchmarks are a reason to investigate,
not to adopt.

---

## 3 · FACT-CHECKED `lat.md` ROLE

The external `lat.md` project: Markdown knowledge graph for a codebase —
`lat.md/` directory, sections, `[[wiki links]]`, links into source,
`// @lat:` / `# @lat:` backlinks, referential checking, exact/fuzzy lookup,
local semantic search, prompt/reference expansion, optional MCP server,
agent hooks. It is **human-readable first and agent-parseable by design** —
neither exclusively human nor exclusively AI.

**LLM facts:** plain read, `check`, `locate/section/refs`, and MCP require
no LLM; `search` uses a bundled local **embedding** model (WASM) — a
retrieval vector mapper, not a generative LLM; only the agent workflow may
use one.

**Compiler facts:** `latCore ∩ RequiredCompilerServices = ∅`. Documented
examples are TypeScript/Python with multi-language source links (Rust, Go,
C). F# is **not** a documented symbol-link language:

```text
FSharpFileBacklinkComment      = syntactically possible with // @lat:
FSharpSymbolResolutionByLat    = UNPROVEN until tested with pinned version
```

Use Markdown nodes and textual backlinks immediately; claim exact F# symbol
support only after a small pinned-version corpus proves it.

**CLI disposition:** `lat section` / `lat refs` — ADOPT after version pin;
`init` / `check` / `locate` / `search` / `expand` / `reindex` — PILOT
(check = referential evidence only; search = candidate, not truth; index =
never authority); `lat mcp` — DORMANT until the direct CLI workflow is
proven.

### 3.1 · ▲ The Gate B2 grounding split

Pipeline Gate B2 requires "cited files and existing symbols resolve at the
pinned base SHA" — previously a reviewer promise. This profile mechanizes
**half** of it:

```text
file/path/section grounding  → lat check on the packet's cited references
                               (referential box; runs in CI at base_sha)
F# symbol grounding          → OPEN; belongs to the future
                               F_SHARP_TOOLING.md profile (FCS resolver)
```

Honest status: B2 grounding = `lat check` (mechanized, paths) ⊕ reviewer
(symbols, until the tooling profile wakes). Never present the first half as
covering the second.

---

## 4 · CORRECT ARCHITECTURE

**Minimal human-first:** normative sources → reviewed explanation and links
→ `lat.md/*.md` ↔ human author/reviewer → Git diff + ordinary review.
`RuntimeDependencies(HumanKnowledgeWorkflow) = {filesystem, Markdown reader}`.

**Optional tooling:** `check/locate/section/refs` · optional local search
index · optional editor integration · optional MCP retrieval — none changes
stored meaning without an explicit reviewed file edit.

**Optional LLM:** task → exact relevant sections → linked code/evidence
under normal permissions → deterministic limits and deduplication → optional
Headroom on SoftContent only → LLM → candidate → human review and ordinary
verification. If the task edits F#, ordinary build/test commands validate —
exactly as a human would use them.

**Forbidden:** `lat.md → mandatory LLM` · `→ mandatory compiler service` ·
`→ mandatory Headroom` · `Headroom → rewrite stored knowledge` ·
`Agent → silently accept its own graph update` ·
`Embedding similarity → declare semantic truth`.

### 4.1 · ▲ Three-plane AgentContext composition

Three documents now emit "agent context." They are planes of one context,
with strict authority ordering — never three competing briefings:

```text
AgentContext(task)
  = PacketProjection(task)      ← AUTHORITY  (Pipeline §5.4: permissions,
                                  digests, allowed/forbidden effects,
                                  commands, stop conditions, approvals)
  ⊕ CanonicalProjection(task)   ← SEMANTICS  (Base §46.6: schemas, refined
                                  constraints, lineage grades, fidelity gaps)
  ⊕ LatSections(task)           ← EXPLANATION (this profile §8: reviewed
                                  narrative, rationale, known gaps)

Authority ∈ PacketProjection only.
LatSections enter as UntrustedData (§12.1) — they explain, never permit.
A lat node claiming a permission, path, or effect the packet denies is a
drift finding, not an authorization.
```

One packet validator, one canonical emitter, one graph — three folds of
context, one source of authority.

---

## 5 · RECOMMENDED CFF KNOWLEDGE SITEMAP

```text
lat.md/
  index.md · purpose-and-boundaries.md · architecture.md · authority.md
  canon.md · evidence-and-proof.md · fidelity.md
  workflows/ change-to-review.md · candidate-to-proof.md · release-boundaries.md
  decisions/ index.md
  tests/ high-level-specs.md
  glossary.md
```

| File | Contains | Must not become |
|---|---|---|
| `index.md` | entry routes for humans and tools | duplicate of every node |
| `purpose-and-boundaries.md` | goal, scope, explicit non-goals | evidence-free marketing |
| `architecture.md` | components, dependencies, information flow | source-code inventory |
| `authority.md` | precedence of Base, ADRs, sources, evidence | replacement for them |
| `canon.md` | Canon concepts and invariants explained | executable verdict logic |
| `evidence-and-proof.md` | tests ↔ Gold ↔ Crucible relationship | auto-generated truth |
| `fidelity.md` | exact/partial/unknown/unsupported meaning | universal-exactness claim |
| `workflows/*` | human-reviewable lifecycle narratives | hidden automation authority |
| `decisions/index.md` | links to accepted ADRs | copied ADR content that drifts |
| `tests/high-level-specs.md` | important behavioral examples, corner cases | self-confirming expectations |
| `glossary.md` | stable terms and aliases | a second domain model |

**Growth rule:** split a node only on multiple independent concepts, a
review unit too large, or retrieval frequently needing a subset. Structure
is earned through use, never built as advance taxonomy.

---

## 6 · KNOWLEDGE NODE CONTRACT

```text
KnowledgeNode = StableSectionIdentity × Purpose × Explanation
              × NormativeSourceRefs × RelatedNodeRefs × CodeRefs
              × EvidenceRefs × KnownGaps × ReviewState
```

**Source precedence:** conflict with a normative source ⇒ effective meaning
is the source, node is `STALE`, review required.

**Code-link meaning:** `CodeLinkState = VerifiedByPinnedLat |
TextReferenceOnly | Ambiguous | Missing | Stale | Unsupported`. An existing
path does not prove the code implements the node; a matching symbol name
does not prove behavioral agreement.

**Backlink meaning:** presence declares a relationship; it never proves
implementation correctness or coverage.

**Search meaning:** a hit is a candidate relevant node — never truth,
authority, or sufficiency. Humans and agents still inspect the node and its
governing links.

---

## 7 · HUMAN-FIRST WORKFLOW

**Reading:** question → index or search → exact section → governing-source
link when authority matters → linked code/evidence when implementation
matters.

**Changing behavior:** identify affected nodes → edit code and knowledge
together when meaning changes → inspect the semantic Markdown diff first →
code/evidence diff → `lat check` when available → normal verification →
human acceptance.

**Knowledge-only change allowed** ⇔ clarification without behavior change ∨
corrected drift ∨ new explanation of existing accepted meaning — and the
review states why code, tests, and sources need no change.

**Review order (aid, not authority ranking):** normative-source changes →
lat semantic changes → tests/expected evidence → implementation → generated
or mechanical changes.

### 7.1 · ▲ Pipeline interlocks

```text
Gate L → lat nodes.  Pipeline Gate L's durable facts (escaped defect,
failed assumption, new invariant, pitfall, deletion opportunity) land as
lat nodes through the §8.2 candidate→review flow.  This answers "where
does sanctioned project memory live" structurally: in the reviewed graph —
which is precisely why mempalace/free-form model memory stayed REJECTED
(Pipeline §3.3).  Model memory rots privately; graph nodes rot publicly,
under diff.

Capsule anchors.  Gate F2's required_read_refs may cite lat
StableSectionIdentity values (they are packet/Base/ADR-class anchors:
reviewed, versioned, in-repo).  The capsule binds the graph commit;
resume validation runs lat check over the cited anchors — a dangling
knowledge anchor is a RESUME_BLOCKED condition like any other digest
mismatch.

Packet references.  constitution_refs in the packet header may point at
lat workflow nodes for narrative; authority still resolves only through
the node's NormativeSourceRefs.  A packet citing prose instead of law is
a B2 finding.
```

---

## 8 · OPTIONAL AGENT WORKFLOW

Agent task → retrieve exact nodes → preserve source identities → state
missing/conflicting information → propose a bounded candidate → show
affected nodes → await normal review. The graph reduces repeated
exploration; it does not remove the need to inspect current code or
evidence.

**Agent update law:** `AgentGraphEdit → CandidateDiff → ReferentialChecks →
HumanSemanticReview → Accept | Revise | Reject`. Hooks may remind the agent
to update knowledge; they may not silently rewrite `CANONFLOW_BASE.md`,
accepted ADRs, Gold expectations, signed evidence, release records, or
security exceptions.

**MCP role:** transport for bounded retrieval. `MCP ≠ KnowledgeAuthority ≠
Compiler ≠ LLM`. Prove direct Markdown/CLI access first.

---

## 9 · HEADROOM FACT CHECK AND DECISION

Headroom compresses material an agent/LLM reads: routing, JSON reduction,
AST-aware code compression, model-based text compression, cache-prefix
alignment, reversible retrieval, proxy/MCP integrations, cross-agent
memory, session learning, output shaping. Evaluate mechanisms separately —
"use Headroom" is not one atomic decision.

**Compiler support needed?** No: `HeadroomRequiredCompilerServices = ∅`
(AST-oriented parsing ≠ the project's compiler).

**F# support?** Documented AST-aware list: Python, JS/TS, Go, Rust, Java,
C/C++, Perl. F# absent ⇒ `HeadroomASTSupport(FSharp) = UNPROVEN` and
`UseHeadroomCodeCompressor(FSharpExactCode) = forbidden`. Generic lossy
compression never touches exact code, types, signatures, diagnostics, or
patches (LAT-009).

**When it may matter:** LLM used ∧ material context pressure measured ∧
deterministic selection insufficient ∧ safe content class available ∧
benefit exceeds integration cost. Safe pilot inputs: large repeated JSON
tool results, duplicate search results, verbose nonauthoritative logs,
repeated explanatory prose. Always exact: lat source nodes used for a
decision, laws/prohibitions, F# source and patches, compiler diagnostics,
Gold results, evidence/citations/hashes/signatures, approvals, security
constraints.

**When it does not matter:** human-only workflow ∨ small selected context ∨
provider-native compaction sufficient ∨ no measured token/latency problem ∨
critical content dominates ⇒ do not add it.

**Cherry-pick disposition:** measurement ideas — ADAPT (CFF owns metrics
and oracle) · deterministic JSON reduction — PILOT (soft tool output only;
preserve IDs/errors/pagination) · reversible retrieval — PILOT (digest,
scope, TTL, retrieval tests) · cache-prefix alignment — PILOT after a
measured problem · generic prose compression — DORMANT (nonauthoritative
prose; held-out loss eval) · **F# AST compressor — REJECTED pending
documented support and proof** · transparent proxy/wrapping — DORMANT ·
cross-agent memory — DORMANT (isolation, provenance, expiry, deletion
proof) · hosted processing — DORMANT (privacy/retention/residency) ·
learning dry-run report — ADAPT (candidates only) · **auto-writes to
constitutions/ADRs/Gold/agent instructions — REJECTED** · **output effort
shaping during proof/release work — REJECTED**.

---

## 10 · PREFERRED CONTEXT STRATEGY WITHOUT HEADROOM

Before compression, do less work and select less content:

```text
1. exact section retrieval
2. bounded graph traversal
3. deterministic field selection
4. exact deduplication
5. disposable local index/search
6. provider-native caching/compaction when available
7. third-party lossy compression only for a proven residual problem
```

▲ This is Pipeline §8.3's context discipline made concrete: minimal context
is achieved by *selection*, not compression — simpler, human-auditable,
language-neutral, and sufficient for the first adoption.

---

## 11 · OPTIONAL COMPRESSION LAW

```text
ContentClass = CriticalExact | StructuredSoft | NarrativeSoft
             | DuplicateBulk | SecretRestricted | Unknown
Classify(Unknown) = CriticalExact                      (fail closed)

Routing: CriticalExact → preserve exact
         StructuredSoft → deterministic projection; optional reversible
         NarrativeSoft → optional reversible
         DuplicateBulk → exact dedup first
         SecretRestricted → data policy before any transform
```

Every compressed item carries a receipt: `OriginalDigest × Algorithm+Version
× ParametersDigest × RepositoryScope × CreatedAt × ExpiresAt ×
RetrievalHandle`, with `Retrieve(receipt) = original` and
`H(original) = receipt.OriginalDigest`. Failed retrieval or digest ⇒ the
summary is unusable for governed work. (Digest-vocabulary discipline per
Base §18: digest ≠ signature ≠ authenticity.)

---

## 12 · SECURITY AND PRIVACY

**Untrusted content:** source comments, node text, search results, imported
documents, tool output, compressed summaries ⇒ UntrustedData. It cannot
change permissions, approved roots, tool authority, review state, output
destination, or release status (Pipeline §8.5 applies verbatim to graph
content — a lat node containing instruction-like text is data or a security
fixture, never authority).

**Local-first defaults:** storage = repository files · search = local when
enabled · agent transport = local process · remote upload forbidden unless
separately approved.

**Data exclusions:** no secrets, credentials, taxpayer material, personal
data, unredacted production records, or signing material in lat nodes,
embedding indexes, Headroom memory, compression caches, agent transcripts,
or remote search — absent a specific approved data policy.

---

## 13 · TEST MATRIX

| Law | Satisfying case | Falsifier |
|---|---|---|
| human readability | ordinary Markdown viewer suffices | essential content needs LLM/tool |
| LLM independence | read/edit/review with all models absent | graph unusable without model |
| compiler independence | workflow with no FCS/FSAC/compiler | check/navigation needs compiler |
| removability | delete indexes/Headroom/MCP, files usable | meaning exists only in cache |
| authority | node links to source, yields on conflict | node silently overrides Base/ADR |
| referential check | broken wiki/code ref reported | passing check called domain proof |
| F# support honesty | unsupported link labelled unproven | textual backlink called verified |
| search honesty | hit shown as candidate with source | similarity presented as truth |
| agent review | edit stays candidate until reviewed | hook self-accepts |
| Headroom isolation | removal changes no graph behavior | lat depends on compression proxy |
| critical exactness | exact law/code/diagnostic reaches reviewer | lossy summary replaces it |
| retrieval | cached original matches receipt digest | wrong/missing original accepted |
| ▲ plane separation | lat links to OKF artifact | lat restates or generates OKF |
| ▲ context authority | permission honored only from packet | lat prose treated as permission |

**Boundary cases:** missing/empty graph · single node · broken wiki link ·
renamed file/heading · missing code path · ambiguous symbol text ·
F# `// @lat:` backlink · offline search · missing local index · **hostile
instruction inside Markdown** · very large node · cyclic references ·
duplicate content · Headroom unavailable · expired receipt · wrong
repository retrieval scope · ▲ dangling capsule knowledge-anchor · ▲ lat
node asserting a packet-denied permission.

---

## 14 · ADOPTION GATES

```text
Gate 0 · Sealed-stack proof — no product dependency changed; no verdict/
         runtime/release package requires lat.md; no LLM/compiler/Headroom
         required.  ▲ CI asserts shipped artifacts have no lat linkage.
Gate 1 · Human graph — §5 sitemap; a stranger follows index.md to purpose,
         boundaries, architecture, authority, evidence, workflows.
Gate 2 · Referential tooling — pin one lat version; run check/locate/
         section/refs on a hostile test graph; record exactly what each
         command proves.  ▲ One R1 tooling packet (Pipeline §8.4).
Gate 3 · F# compatibility experiment — file links, section links, code
         refs, // @lat: backlinks on a small F# corpus; publish the honest
         support matrix.  Never add compiler tooling to pass this gate.
Gate 4 · Human workflow proof — one real CFF change reviews the knowledge
         diff before the code diff; record whether understanding improved
         without harmful upkeep.
Gate 5 · Optional agent retrieval — exact CLI/MCP retrieval with a
         replaceable agent; correct nodes, fewer irrelevant reads, no
         authority expansion.  ▲ Wires the §4.1 three-plane composition.
Gate 6 · Optional local semantic search — recall, false positives, offline
         behavior, rebuild, disposable-index recovery; exact navigation
         survives search removal.
Gate 7 · Optional Headroom experiment — only if Gate 5 shows material
         pressure: A raw exploration · B exact retrieval · C = B + limits +
         dedup · D = C + Headroom on SoftContent.  Select D only if it
         materially beats C with no critical omissions, retrieval failures,
         wrong citations, or worse human acceptance.
```

---

## 15 · TRUTHFUL STATUS

| Capability | Status |
|---|---|
| corrected human-first architecture | `DESIGNED` |
| recommended sitemap · human workflow · deterministic context selection | `DESIGNED` |
| ▲ LAT/OKF demarcation · three-plane AgentContext · pipeline interlocks | `DESIGNED` |
| pinned lat CLI validation | `DORMANT` until Gate 2 |
| F# code-link/backlink compatibility | `DORMANT` — unproven until Gate 3 |
| local semantic search · MCP · LLM-assisted workflow | `DORMANT`, optional |
| compiler-service integration inside this profile | `REJECTED` — separate profile |
| Headroom JSON/retrieval pilot · proxy/wrap | `DORMANT` |
| Headroom F# AST compression | `REJECTED` pending documented support + proof |
| Headroom auto-write to authority/instruction files | `REJECTED` |
| lat.md or Headroom in verdict/runtime path | `REJECTED` |

No README, roadmap, generated report, or agent response may advance a
status without gate evidence.

---

## 16 · LANDMINE REGISTER

| Landmine | Response |
|---|---|
| "lat.md is only for AI" | human-readable Markdown is the primary durable layer |
| "lat.md is only for humans" | agent parsing is intentional optional use |
| "embedding model ⇒ LLM required" | retrieval embeddings ≠ generative LLMs |
| compiler tooling becomes a lat dependency | keep FCS/FSAC in a separate profile |
| `lat check` becomes proof of correctness | bound to declared referential checks (▲ a referential box only) |
| backlink becomes implementation proof | declared relationship only |
| F# symbol support assumed from `//` comments | pinned corpus + honest labels |
| graph copies and drifts from the constitution | link and yield on conflict |
| agent silently rewrites architectural history | candidate diff + human semantic review |
| graph becomes documentation bureaucracy | small sitemap; evidence-based split rule |
| search result becomes authority | candidate-only retrieval law |
| Headroom installed before a problem exists | measure selection strategies first |
| generic compression corrupts F# code | exact code never enters a lossy path |
| reversible cache expires unnoticed | receipt + TTL + digest verification |
| proxy/learning changes prompts invisibly | dormant or rejected in governed work |
| ▲ lat and OKF blur into one plane | §0.1 demarcation; drift gate watches both |
| ▲ lat prose read as permission | authority lives only in the packet projection |
| ▲ Gate L facts drift into model memory instead of nodes | §7.1 interlock; memory stays rejected |

---

## 17 · IMMEDIATE EXECUTION ORDER

```text
1.  Keep the separation: knowledge ≠ compiler tooling ≠ compression.
2.  Create the minimal lat.md/ sitemap (§5).
3.  Write index, purpose/boundaries, architecture, authority first.
4.  Link to constitutions and ADRs; never copy them wholesale.
5.  ▲ Add the OKF demarcation note to authority.md on day one.
6.  Pin the lat CLI (R1 tooling packet) and test check/locate/section/refs
    offline.
7.  Test F# file/code/backlink behavior; publish the honest support matrix.
8.  Use the graph in one real human code review (a CFF v1 train item).
9.  ▲ Route the first Gate L retro facts into nodes via candidate review.
10. Add agent retrieval only if it improves that workflow; wire the
    three-plane composition when it does.
11. Add local semantic search only if exact navigation is insufficient.
12. Evaluate Headroom only after a measured LLM context problem remains.
```

```text
PreferredV1 = PlainMarkdownGraph ∧ HumanReview ∧ PinnedReferentialChecks
            ∧ NoCompilerDependency ∧ NoLLMDependency ∧ NoHeadroomDependency
```

---

## 18 · ▲ IMPORT FILTER SELF-TEST AND DOC-SET DELTAS

§46.7: DomainNeutral ✓ (knowledge planes exist for EDIFlow identically) ·
Enforceable ✓ (Gate 0 CI assertion, referential checks, plane-separation and
context-authority falsifiers) · Falsifiable ✓ (§13 matrix, per-law) ·
NonDuplicative ✓ (OKF demarcation prevents the duplicate it might have
created) · ReducesFailureOrAmbiguity ✓ (answers where durable knowledge and
retro facts live; mechanizes half of B2 grounding) · RespectsOwnership ✓
(documentation and CI tooling; nothing in Core, nothing in the kernel).

Deltas proposed to the doc set: Pipeline §5.1 (`constitution_refs` may cite
lat workflow nodes, authority via their source refs) · Pipeline Gate B2
(grounding split: lat check for paths, reviewer/f-sharp-tooling for symbols)
· Pipeline Gate F2 (capsule knowledge anchors + resume `lat check`) ·
Pipeline Gate L (durable facts land as candidate lat nodes) · Pipeline §13
(+`lat-check` CI step over packet-cited references) · Base §15.4 (OKF
section gains the authored-vs-emitted demarcation note) · DYNAMIC annex §7
(discharge vocabulary gains `referential-check`).

---

## 19 · FINAL LAW

```text
The knowledge graph belongs to humans.
Tools may check it.  Search may find it.
Agents may read and propose changes to it.
No compiler is required to understand it.
No LLM is required to preserve it.
No compressor is allowed to become its authority.
```

```text
Plain knowledge first.
Deterministic navigation second.
Optional intelligence third.
Compression only after evidence.
```
