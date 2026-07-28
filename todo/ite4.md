My recommendation: don’t run M9→M12 serially. Start the official ONDC track now, while engineering completes a narrow M8.5→M10→M11 path.

| Priority | Next action | Reason |
|---|---|---|
| 1 | Ask ONDC whether third-party evaluator accreditation exists | Public documentation describes certification/onboarding for Network Participants—not independent validation products |
| 2 | M8.5 trust closure | Add trusted signature, registry, replay and timestamp observers; test against real anonymized traces |
| 3 | Move profile from 1.2.0 preview to official Retail 1.2.5 | Current source/version drift undermines official claims |
| 4 | M10 parity | Prove native and WASM produce identical canonical bytes, hashes, verdicts and receipts |
| 5 | M11 secure viewer | Verify receipts before rendering; remove unsafe `innerHTML` paths |
| 6 | M9 PostgreSQL | Only when a design partner requires database differential checks |
| 7 | M12 certification issuance | Only after ONDC provides written authority, governance and branding rules |

ONDC publicly positions [Pramaan](https://github.com/ONDC-Official/pramaan) as its protocol-integration test bench. Its [onboarding documentation](https://github.com/ONDC-Official/developer-docs/blob/main/registry/Onboarding%20of%20Participants.md) and [Network Policy](https://resources.ondc.org/ondc-network-policy) primarily cover Network Participants. I found no published accreditation route for an independent evaluator like CanonFlow. That is the biggest current uncertainty.

### The official-confidence ladder

- Current: **5–10% official authority**, but approximately **90% technical confidence** for the bounded offline evaluator.
- 20–30%: ONDC confirms that an evaluator/tool-recognition route exists and names an owner.
- 40–60%: official 1.2.5 scope, three or more real traces, Pramaan comparison, design partners and governance draft.
- 70–80%: ONDC-supervised pilot, approved keys, issuance/revocation process, liability and branding approval.
- 100%: written final recognition or official listing.

If ONDC confirms that no third-party accreditation category exists, “official certification” cannot honestly progress beyond approximately 10%. The viable objective would become “independent ONDC assurance,” an ONDC tooling contribution, or integration through an onboarded Network Participant/TSP.

### Implementation learnings

The implementation significantly raised engineering confidence:

- Clean WSL clones and all remote workflows now pass.
- CanonFlow tests are 49/49; ONDCFlow 21/21; FsAssay regression 39/39.
- Locked cross-repository integration, Docker hardening, air-gapped packaging, SBOM and native WSL Container execution pass.
- Deterministic source normalization, dependency pinning and hash-chain regeneration must be atomic.
- Cached dependencies and Windows filesystem normalization can conceal serious reproducibility defects.
- Green behavioral tests do not mean architectural debt is absent: FsAssay’s complete self-audit still reports 425 findings.
- A locally signed receipt proves mechanics—not trusted identity, official authority or governance.

My confidence:

- Bounded evaluator correctness: **90–93%**
- Controlled design-partner pilot: **75–80%**
- Completing M10/M11 technically: **85%**
- Product-market validation: **25–35%**
- Existence of an official CanonFlow accreditation route: **25–35%**

### Blind spots now visible

- No confirmed ONDC category for independent validator certification.
- No production-quality ONDC trace corpus or measured false-positive/false-negative rates.
- Profile drift from 1.2.0 preview to current official 1.2.5.
- Differentiation from Pramaan and Workbench remains unproven.
- Production signing authority, key rotation, revocation and trusted timestamps are absent.
- WASM/native receipt equivalence remains incomplete.
- Viewer verification and XSS hardening remain incomplete.
- Legal liability, ONDC mark usage, privacy and trace anonymization are unresolved.
- Native `wslc` received build/run testing, but not the complete hardening-equivalence testing performed through the Docker-compatible engine.

The next best move is therefore: **open the ONDC authority conversation immediately, while implementing M8.5 and M10.** M9 is not presently on the critical path, and M12 must wait for written institutional authority.

Yes—OCI already gives ONDCFlow language neutrality. I would not rewrite the F# engine or create full native implementations per language.

My recommendation:

1. Keep F# as the single verified engine.
2. Make OCI/CLI the authoritative execution boundary.
3. Stabilize a versioned JSON input/output contract.
4. Build thin SDKs:
   - TypeScript first
   - Python second
   - Java only after customer demand
5. Add a GitHub Action before additional SDKs.

Official ONDC repositories currently lean heavily toward TypeScript/JavaScript, while ONDC’s utilities explicitly support Node.js, Python, Java, Go and PHP. [ONDC repositories](https://github.com/orgs/ONDC-Official/repositories), [ONDC onboarding documentation](https://github.com/ONDC-Official/developer-docs/blob/main/registry/Onboarding%20of%20Participants.md)

### What the SDK should do

The SDK should be a convenient OCI/CLI client—not another rule engine:

```typescript
const result = await ondcflow.evaluate({
  bundle: "./trace.json",
  profile: "retail-1.2.5",
  image: "ghcr.io/.../canonflow@sha256:..."
});

await ondcflow.verifyReceipt(result.receipt);
```

It should handle:

- OCI invocation and secure mounts
- Canonical JSON serialization
- Exit-code/verdict mapping
- Receipt parsing and offline verification
- Image/profile digest pinning
- Timeouts and structured errors

### Important current detail

ONDCFlow itself is presently an F# library embedded in the CanonFlow evaluator. The OCI image belongs to CanonFlow; there is no standalone ONDCFlow executable/image yet.

Therefore, first create a stable façade such as:

```text
canonflow ondc evaluate --input - --output -
canonflow receipt verify --receipt -
canonflow capabilities --json
```

Then TypeScript and Python wrappers become small and maintainable.

### Priority

- **High:** CLI JSON contract, TypeScript SDK, GitHub Action
- **Medium:** Python SDK
- **Later:** Java SDK
- **Avoid now:** Go/PHP SDKs, HTTP service, independent language ports
- **M10:** WASM only for browser/local interactive validation after byte-equivalence is proven

My confidence in this direction is **90%**. OCI solves portability; the SDK solves usability. Keeping evaluation in one F# engine preserves the determinism and evidence guarantees already earned.


Fable’s multi-target capability is a genuine edge—but supporting every target now would turn it into a liability.

Current official status is:

- JavaScript and TypeScript: stable
- Python and Dart: beta
- Rust: alpha/beta depending on the Fable documentation page
- Erlang/BEAM: experimental

[Fable target status](https://fable.io/docs/), [Fable CLI](https://fable.io/docs/getting-started/cli.html)

My recommended matrix:

| Tier | Targets | Commitment |
|---|---|---|
| Production | OCI/.NET, TypeScript/JavaScript | Fully supported and parity-tested |
| Preview | Python | Supported experiment; no certification claims |
| Labs | Rust, Dart | Build and test periodically |
| Ignore | Erlang, PHP | Reconsider only with customer demand |

JavaScript and TypeScript should be one npm package, not two SDKs.

The best architecture is to extract a pure `ONDCFlow.Portable` kernel:

```text
Canonical JSON input
        ↓
ONDCFlow.Portable — pure F#
        ↓
Canonical verdict/receipt JSON
```

Keep these outside the portable kernel:

- Filesystem and OCI orchestration
- BouncyCastle signing
- Process execution
- Ambient runtime APIs
- HTTP and database access

Those currently depend on .NET libraries and will not automatically work merely by passing `--lang python` or `--lang rust`.

Every promoted target must pass the same:

- Golden trace corpus
- Mutation and malformed-input corpus
- Canonical byte comparison
- Receipt digest comparison
- Verdict and exit-semantics comparison

The product claim then becomes powerful:

> One admitted F# rule source, independently compiled across runtimes, producing byte-identical ONDC decisions.

That is an edge. “Available in six languages” alone is not.

I would sequence it:

1. Stable OCI/CLI JSON boundary.
2. TypeScript/JavaScript npm SDK.
3. Native-versus-TypeScript parity gate.
4. Python preview and parity gate.
5. Rust experiment only if we need tiny WASM/serverless binaries.
6. Dart only if a Flutter/mobile design partner appears.
7. Ignore Erlang for now.

This keeps approximately **80% of the portability value with 20% of the maintenance burden**. The OCI remains the authoritative evaluator; transpiled packages provide fast developer feedback, while authoritative signed assessments continue through the pinned OCI image.

Consumers will love the SDK because it makes ONDC assurance feel native to their existing stack—not like adopting an F# platform.

A TypeScript developer gets:

```ts
const result = await ondcflow.evaluate("./transaction.json");

if (!result.pass) {
  console.log(result.findings);
}
```

A Python developer gets:

```python
result = ondcflow.evaluate("transaction.json")

if not result.pass_:
    print(result.findings)
```

They never need to understand F#, .NET, receipt algebra, canonicalization or container flags.

What consumers gain:

- **Five-minute adoption:** install one package and evaluate their first transaction.
- **Familiar types:** autocomplete, documented models and compile-time checking.
- **Actionable errors:** “`on_confirm.order.id` differs from `confirm.order.id`,” instead of a generic validation failure.
- **CI-native use:** GitHub Actions, GitLab, Jenkins and local test runners receive predictable exit codes.
- **No environment fight:** the SDK locates or pulls the pinned OCI evaluator.
- **Reproducibility:** laptop and CI use the exact same evaluator image and rule-pack digest.
- **Safe upgrades:** users can pin an ONDC profile and inspect changes before upgrading.
- **Offline operation:** transaction data does not need to leave their infrastructure.
- **Audit evidence:** every run can return a verifiable `.cff` receipt—not merely a boolean.
- **Vendor independence:** the receipt remains independently verifiable even without the SDK.
- **No duplicated rules:** TypeScript and Python consumers receive the same decision from the same F# truth.
- **Progressive adoption:** start with one test, then CI, then release gates and audit workflows.

The emotional benefit is equally important: consumers feel that CanonFlow fits into their product rather than requiring their product to fit into CanonFlow.

The strongest consumer-facing message would be:

> Validate your ONDC journey in the language you already use. Get actionable findings in seconds and a reproducible evidence receipt when it matters.

The SDK should therefore have three layers:

1. `validate()` — simple local developer feedback.
2. `evaluate()` — deterministic OCI-backed assessment.
3. `verifyReceipt()` — independent audit verification.

This provides a gentle learning curve while preserving the rigorous engine underneath. The simple path is easy; the trustworthy path is always available.

## Implementation record — 2026-07-28

ITE4's first delivery slice is complete.

### Delivered

- Added the stable CanonFlow façade:
  - `canonflow ondc evaluate --input ... --output ... --profile ... --instant ...`
  - `canonflow receipt verify --receipt ...`
  - `canonflow capabilities --json`
- Added a versioned SDK result contract that returns structured findings, missing evidence, tool failures and the exact canonical receipt.
- Added strict UTC evaluation-time validation, bounded input/output, structured errors and stable verdict/exit-code mapping.
- Added the `@canonflow/ondcflow` TypeScript/JavaScript SDK with:
  - `validate()`
  - `evaluate()`
  - `verifyReceipt()`
  - `capabilities()`
- Kept the OCI evaluator authoritative; the SDK does not duplicate or reinterpret the F# rules.
- Added immutable-image enforcement and hardened OCI execution: no network, read-only root, dropped capabilities, no-new-privileges and bounded CPU, memory, PIDs, output, timeout and temporary storage.
- Added a composite GitHub Action that exposes result, verdict and exit-code outputs.
- Added SDK, action and protocol documentation with an explicit experimental/non-authoritative status.

The only admitted profile remains `ondc-retail-1.2.0-preview`, with ten bounded predicates and `authority: none`. This delivery does not claim official ONDC certification.

### Verification completed

| Gate | Result |
|---|---|
| CanonFlow release image build on pinned .NET 10 SDK/runtime | Pass; zero build warnings |
| FsAssay during evaluator build | Pass; zero violations |
| Hardened container and SDK façade gate | Pass |
| Invalid/non-UTC timestamp boundary | Pass; structured `INVALID_INSTANT` and exit 64 |
| TypeScript strict compilation | Pass |
| TypeScript SDK tests | 5/5 pass |
| Compiled SDK → real OCI evaluator → receipt verification | Pass |
| ONDCFlow .NET tests | 21/21 pass |
| CanonFlow non-PostgreSQL suites | XP 1/1, assurance 15/15, laws 26/26 |
| PostgreSQL integration suite | 7/7 pass with the nested-WSL Testcontainers host configured |
| Package audit and dry-run packaging | Pass; zero known audit vulnerabilities |

### What remains

1. Start the ONDC authority/accreditation conversation now; code cannot create institutional authority.
2. Complete M8.5 trust closure: trusted observers, signatures, timestamp/replay controls, registry evidence and real anonymized traces.
3. Admit the current official Retail profile only after its sources, hashes, scope and expected outcomes are reviewed and pinned.
4. Publish and sign the evaluator image by immutable digest, then publish the npm package with provenance.
5. Complete M10 native/portable byte, digest, verdict and receipt parity.
6. Add the Python preview only after the TypeScript/OCI contract survives design-partner use.
7. Complete M11's verify-before-render secure viewer.
8. Pull M9 forward only for a real database-differential customer; keep M12 blocked on written authority and governance.

This is the intended narrow path: **SDK/contract foundation → M8.5 trust closure → official-profile admission → M10 parity → M11 viewer**. M9 is demand-driven, and M12 is authority-driven.
