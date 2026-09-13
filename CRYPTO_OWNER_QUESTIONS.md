# PFI / KyoSys — Questions for the Crypto Owner

**Purpose:** Close the decisions needed to build the local CRS-ASC core and connect it safely to the Holder, Verifier and Registry.  
**Prepared:** 13 September 2026. **Primary owner:** WBS 2.x Crypto Owner.  
**Status:** Questions for owner response; no algorithm, parameter set, encoding or interface is selected by this document.

## How to use this document

Answer **CR-01–CR-03 first** to establish the implementation baseline and start a repeatable library build. Use CR-04 to record what is known about the suite and assign the remaining decisions; a fully approved suite is required before real proof integration, not before assessing and building the selected core. CR-05–CR-16 close the integration decisions. CR-17–CR-20 define the evidence and handoff needed to accept that integration. Work that does not depend on an unanswered decision can proceed; an answer may identify a specific blocker instead of proposing an unsupported design.

Use this response template for each question. Link an existing document if it already answers the question; do not rewrite it unnecessarily.

```text
Question ID:
Status: Answered / Proposed for review / Blocked / Not applicable (explain)
Answer or proposed decision:
Evidence: paper section, repository + commit/tag, specification, test or document
Outstanding dependency and person responsible:
Reviewers required:
Target date / implementation work this unblocks:
```

Share source references, public parameters, documentation and synthetic test vectors. **Do not send real Holder secrets, issuer private keys, credentials, tokens or personal age evidence.**

## Architecture already agreed

These are implementation constraints, not questions to reopen:

- CRS-ASC is a dedicated C-based local library. Holder generation and Verifier verification stay inside their respective trust zones; there is no remote crypto service.
- The Holder secret remains local. A `keyReference` is a local handle, not secret bytes on the wire.
- ADR-009 retains verifier-scoped nullifiers: the proof must establish membership and correct nullifier generation from the **same Holder witness**. Same approved context permits intentional local linkability; different contexts target cross-Verifier unlinkability under the construction's assumptions. ADR-009 refines the earlier blanket transaction-unlinkability wording in ADR-001.
- Fresh per-attempt challenges remain necessary. A nullifier does not replace replay controls or establish liveness, non-transferability or resistance to live relay.
- Proofs bind to the intended Verifier context, challenge and exact Registry state. The Verifier independently fetches the complete, ordered, exact-version snapshot.
- Presentations contain only `contractVersion`, `challengeId`, `registryVersion`, `nullifier` and `proof`. They disclose no Holder commitment, credential, matching index or secret. Nullifiers are never stored in Registry snapshots.
- OpenID4VCI governs issuance; its exact profile and credential format remain open. Holder-mediated Registry admission remains PFI-specific. Browser/PWA and WASM are proposals, not settled platforms.

## First answers — establish the construction and build

### CR-01 — Which exact research construction and source implementation are we implementing?

Please provide the paper/version, relevant construction and security sections, source repository, exact commit or release, and any local patches. If the paper or repository uses different names for related constructions, identify exactly which algorithms and components form the PFI baseline.

**Expected output:** A pinned source/reference list and implementation selection record, including any missing or inaccessible material. **Review:** Architecture Lead; standards/research reviewer where needed. **Unblocks:** WBS 2.1 and the dependent crypto issues.

### CR-02 — Does that implementation support the complete PFI proof statement?

Which existing algorithms establish anonymity-set membership, the verifier-scoped nullifier's same-witness relationship, and the required context/challenge/state binding? What is already supported, what requires an adapter, and what would require a change to the cryptographic construction? State the security assumptions, known limitations and available review evidence, including cross-Verifier unlinkability and maliciously chosen context/public-input risks.

**Expected output:** A requirement-to-algorithm/source mapping and explicit gaps. Changes to the construction need review before dependent implementation. **Review:** Architecture Lead and a qualified cryptography reviewer.

### CR-03 — What is the reproducible local build baseline?

Which compiler/toolchain, supported native targets, dependency versions, build flags and licenses are required? What commands produce the library and standalone harness from a clean checkout? Which build or portability constraints should the App Owner know before selecting client runtimes?

**Expected output:** Build instructions, dependency/license inventory and planned first working harness. Separate demonstrated native support from proposed WASM support. **Review:** App Owner for target compatibility. **Unblocks:** WBS 2.2 without requiring Docker or application images.

### CR-04 — What will the approved crypto-suite descriptor contain, and how will it be versioned?

Specify the selected construction revision, public parameters, algorithms, encoding rules, transcript/domain rules and supported bounds that one `cryptoSuite` identifier will select. Which changes require a new identifier, and how will applications reject unknown or incompatible profiles?

**Expected output:** A reviewed suite descriptor and approval owner; retain `PENDING_CRYPTO_OWNER` until approved. Do not choose a plausible identifier independently of its complete definition. **Review:** Architecture Lead and App Owner for allowlist/configuration integration.

## Answers required before real application integration

### CR-05 — How are the Holder master secret and public commitment generated and validated?

Define the secret/commitment relationship, accepted mathematical types, randomness source and failure handling, valid value ranges and public-input checks. How is fresh proof randomness obtained without accidental reuse, weak fallback or reuse of test-only deterministic randomness?

**Expected output:** Key/commitment and randomness specification, local generation operation, and synthetic generation/invalid-input vectors. **Review:** App Owner for platform entropy and secret lifecycle. **Maps to:** WBS 2.3.

### CR-06 — What exact nullifier derivation and same-witness proof relation will be used?

Specify the reviewed derivation, its inputs, encoding and how verification establishes its relationship to the membership witness. Show that a new challenge does not change the nullifier for the same Holder and approved context, while changes to approved scope have the reviewed privacy properties. Identify the construction's limitations without introducing a global identifier.

**Expected output:** Algorithm/source references, proof-relation description and same-context/different-context/wrong-witness vectors. **Review:** Architecture Lead; App Owner reviews the resulting local-policy semantics. **Maps to:** WBS 2.4–2.5 and ADR-009.

### CR-07 — How is an approved Verifier context mapped into cryptographic input?

Which parts of `verifierContext` determine nullifier scope, and how are they encoded and mapped into the construction? Define equivalence, case/Unicode handling if applicable, invalid values and scope separation. What happens when contexts are shared, renamed or rotated, and what constraints must provisioning enforce to avoid accidental cross-service correlation?

**Expected output:** Canonical context mapping and test examples. The App Owner/Architecture Lead define approved service identity and authentication; a received identifier string alone does not establish trust. **Review:** Joint Architecture + App + Crypto decision.

### CR-08 — What is the complete canonical proof transcript and domain separation?

How does the reviewed API bind contract/domain version, challenge ID and nonce, Verifier identity and 18+ purpose, Registry identity, suite/parameters, challenge times, exact Registry version and the complete ordered set? Specify field boundaries, encodings, mapping to the actual construction and separation from issuance or other uses of the library.

**Expected output:** Byte-level transcript specification and field-by-field mutation vectors. Passing metadata to a function is not evidence of binding; no ad hoc JSON concatenation or unreviewed hash wrapper. **Review:** Architecture Lead; App Owner verifies envelope-to-transcript mapping. **Maps to:** WBS 2.6/2.8.

### CR-09 — Are setup, public generators or other common parameters required?

What does the selected construction require, how are these values derived or obtained, and who may choose them? What validation, provenance and trust assumptions apply? If a setup ceremony or trusted party is unnecessary, identify the source establishing that; if required, identify the process and the work it blocks.

**Expected output:** Public-parameter provenance/validation record tied to the suite, with no unexplained generated constants. **Review:** Architecture Lead and cryptography reviewer.

### CR-10 — What are the canonical bytes and parser rules for every crypto object?

Define commitment, nullifier, proof and any local secret representation: exact lengths or bounds, endianness, versioning, canonical encodings and applicable scalar/group/subgroup/identity-element checks. What must be rejected before expensive work? How do these bytes map to the proposed unpadded base64url transport encoding without alternate accepted representations?

**Expected output:** Serialization specification plus valid, malformed, noncanonical, truncated and oversized fixtures. Real secret serialization remains local. **Review:** App Owner for validation adapters and API limits. **Maps to:** WBS 2.6.

### CR-11 — What anonymity-set rules and limits does the library require?

What minimum/maximum sizes, ordering, padding or shape constraints apply? How will the adapter validate the complete published set and local membership while preserving Registry order and bytes? How are empty, duplicate, invalid-element and unsupported-size inputs rejected? Any required padding must be defined jointly with snapshot publication, not silently applied differently by clients.

**Expected output:** Snapshot-to-crypto input contract and supported set constraints. Separate mathematical minimums from the Architecture/App privacy policy for minimum acceptable anonymity and historical state. **Review:** Architecture + App Owners. **Maps to:** WBS 2.7.

### CR-12 — How will issuance key binding be tied to the CRS-ASC commitment and witness?

Once the App Owner proposes the OpenID4VCI credential format and binding model, what reviewed relationship connects the issuance binding key, issuer-authorized PFI commitment, Holder witness and presentation nullifier? What does the Registry verify, and what prevents substitution of an unrelated key or commitment?

**Expected output:** Joint binding design and rejection vectors; identify any dependency on profile selection. **A generic key-possession proof must not be assumed to bind the CRS-ASC secret.** **Review:** Architecture + App Owners and standards reviewer.

### CR-13 — What cryptographic validation must Registry admission perform on the issuer artefact?

For the selected format, which authenticated data proves 18+ eligibility and exact commitment/suite binding, and which checks belong to the format adapter versus the CRS-ASC library? What issuer-authenticated representation permits validation without sending raw age evidence or unnecessary identity attributes to the Registry?

**Expected output:** Joint validation/minimization mapping and synthetic accepted/rejected artefacts. If the format cannot meet both requirements, document the blocked admission path. Issuer authentication is a separate selection from any Schnorr operation in the CRS-ASC paper. **Review:** App Owner leads credential-format implementation; Architecture reviews the boundary.

### CR-14 — What is the safe local core/adapter interface?

Define actual operations for key/commitment generation, generation returning **nullifier + proof**, and verification consuming **nullifier + proof** with the trusted challenge and snapshot. Specify input/output types, buffer ownership and allocation, length limits, secret lifetime/zeroization, thread safety, cancellation and error handling. How does a local key handle resolve without distributing raw secret bytes throughout the application?

**Expected output:** Versioned C interface and adapter specification with usage examples and ownership rules. Existing conceptual snippets are not an ABI; older proof-only snippets need alignment during contract updates. **Review:** App Owner and Architecture Lead. **Maps to:** WBS 2.6 and Holder/Verifier integration.

### CR-15 — What local secret-storage and execution protections are compatible with the implementation?

Does the library require a raw scalar in process memory, or can the required operations use an opaque platform-held key? What constraints does this impose on proposed native/browser/WASM clients, memory protection and device/key-loss handling? Which protections are demonstrated, and which cannot be claimed on the intended platform?

**Expected output:** Capability/limitation note for the App Owner's storage decision, including the effect of secret replacement on commitment and nullifier continuity. Do not expand the MVP into a recovery system or claim hardware isolation merely because a handle or WASM is used. **Review:** Joint App + Crypto decision; Architecture review.

### CR-16 — How do failures cross the library boundary without becoming acceptance or leakage?

How are unsupported profiles, invalid crypto objects, failed verification, entropy failure, allocation failure and unexpected internal errors distinguished? What happens to partial outputs and secret buffers on failure or cancellation? Which diagnostics are safe for developers and which must never reach logs, peers or the operator?

**Expected output:** Library-to-application error mapping and failure fixtures. Preserve valid/invalid/internal-failure distinction; the App owns challenge reservation, expiry and atomic finalization. **Review:** App Owner against `error-model.md`.

## Evidence required for validation and handoff

### CR-17 — What reproducible positive vectors will prove that components agree?

Supply synthetic vectors for at least the W1/W2/W3 demonstration: public commitments, exact ordered snapshots, canonical bound inputs, nullifiers, proofs and expected verification results. Include repeated fresh challenges at one approved context and distinct contexts. How will native and any later supported WASM builds run the same vectors?

**Expected output:** Versioned fixtures, generator/harness commands and expected outputs tied to the pinned suite. Clearly distinguish synthetic test secrets from production material and deterministic test generation from runtime randomness. **Review:** App Owner and Architecture Lead. **Maps to:** WBS 2.9/4.8.

### CR-18 — What negative and implementation-security evidence will be delivered?

Cover wrong-witness nullifiers; changed nullifier/context/challenge/state; changed set order/content; unsupported suites; malformed, noncanonical and oversized objects; invalid group/scalar values; and failure paths. Which parser fuzzing, memory-safety tooling and side-channel review are appropriate for this implementation, and what remains unassessed?

**Expected output:** Automated tests with expected failure categories, tool/build configuration and a candid coverage report. App concurrency/replay tests remain separate; a passing test suite is not a formal security proof. **Review:** Architecture Lead; App Owner for integrated failure handling. **Maps to:** WBS 2.9.

### CR-19 — What performance and resource limits can the actual clients support?

Measure generation/verification time, peak memory, proof/nullifier sizes and initialization costs across representative set sizes on stated hardware/builds. What bounded execution strategy and supported limits follow? Is the proposed 120-second challenge lifetime feasible once network and application work are included?

**Expected output:** Repeatable benchmark commands/results and recommended limits for joint review. Do not invent performance thresholds or extend challenge deadlines implicitly. **Review:** App Owner and Architecture Lead for timeouts, body/snapshot bounds and client selection. **Maps to:** WBS 2.10.

### CR-20 — What is the first reviewable crypto handoff, and what still blocks it?

Identify the planned source revision/release, build artifact, suite/transcript/serialization/interface documents, test vectors, benchmark report and known limitations. Which questions need App or Architecture decisions before they can close, who will review the package, and when can application integration consume an approved version?

**Expected output:** A small dependency/milestone list with named owners and dates where known. Separate “buildable library”, “validated crypto behaviour” and “integrated application acceptance”; Docker availability is not a prerequisite for the standalone library.

## Source basis

Prepared from the current local project documents, not from an inspection of a selected crypto implementation:

- `pfi-architecture/adr/ADR-001-use-crs-asc.md`
- `pfi-architecture/adr/ADR-005-local-holder-key-custody.md`
- `pfi-architecture/adr/ADR-009-verifier-scoped-nullifiers.md`
- `pfi-architecture/api-contracts/common-types.md`
- `pfi-architecture/api-contracts/holder-verifier-protocol.md`
- `pfi-architecture/api-contracts/openid4vci-profile.md`
- `pfi-architecture/api-contracts/error-model.md`
- `pfi-infrastructure/docs/IMPLEMENTATION_HANDOFF.md`
- `pfi-infrastructure/docs/CRYPTO_BACKLOG.md`

The corresponding application decisions are collected in [Questions for the App Owner](APP_OWNER_QUESTIONS.md). Joint answers can link to one shared decision record instead of being duplicated in both files.
