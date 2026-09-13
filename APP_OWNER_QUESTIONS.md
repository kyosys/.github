# PFI / KyoSys — Questions for the App Owner

**Prepared:** 13 September 2026  
**Scope:** WBS 3.x — Issuer, Registry, Holder and Verifier applications; joint integration under 4.x.  
**Purpose:** Collect the decisions and implementation evidence needed to proceed. This document does not select a protocol, stack, credential format or security policy.

## How to respond

Start with AP-01–AP-05 so the team can scope the first application work. The remaining answers are needed before implementing or enabling the affected flow, not before every task can start. Parallel scaffolding and synthetic tests can proceed where dependencies are clear; unresolved security choices must not become permissive defaults.

For each question, copy and complete:

```text
Question ID:
Answer / proposed decision:
Evidence: repository + commit, document section, synthetic example or test result
Still unresolved / missing information:
Decision owner and required reviewers:
Next action and target date:
```

If undecided, name what is needed and who will supply it. Provide documentation and synthetic or redacted examples, never private keys, access tokens, passwords or real identity evidence. The companion [Crypto Owner questions](CRYPTO_OWNER_QUESTIONS.md) cover cryptographic construction and encoding; agree shared answers once rather than creating competing decisions.

## Existing boundaries — do not reopen through implementation defaults

- OpenID4VCI governs issuance; its exact implementation profile remains open. OpenID4VP and the presentation transport are not selected.
- Holder mediates Registry registration. Issuer is absent during normal verification and has no Registry runtime write path.
- Holder secrets and CRS-ASC generation stay local; CRS-ASC verification is local to Verifier. There is no remote crypto service.
- Registry admits independently validated, issuer-authenticated 18+ commitments. It receives no raw age evidence or Holder secrets and stores no verification nullifiers.
- Snapshots identify exact complete ordered sets. Verifier independently retrieves the referenced state and cannot fall back to `current` after failure.
- Accepted ADR-009 retains same-context nullifier linkability and targets cross-Verifier unlinkability under the selected construction. Fresh challenges remain necessary; no global Holder identifier is introduced.
- API shapes and operational numbers are review proposals, not verified implementation. Docker startup and application runtimes remain unverified/unavailable in the reviewed scaffold.

## Answer first — establish the implementation starting point

### AP-01 — What application implementation and documentation already exist?

Which repositories, branches and commits contain Issuer, Registry, Holder and Verifier code? Which SDKs, sample applications, API specifications, database schemas and tests are already available? Which components still need to be built?

**Needed output:** A component inventory with source/document links, build/run commands and current limitations. Identify the architecture/API revision being implemented with the Architecture Lead; do not assume an unmerged branch is the shared baseline.

### AP-02 — What are the first supported client runtimes and demonstration devices?

Which languages/frameworks and operating systems will the four applications use? Will Holder/Verifier initially be native, desktop, browser or PWA applications, and will they run on separate physical devices? Is browser/WASM support a requirement for the first milestone or a later option?

**Needed output:** A minimal supported platform matrix and first demonstration target, reviewed with Crypto for local-library compatibility. Do not commit to a browser solution before its key-access and build constraints are understood.

### AP-03 — Which OpenID4VCI implementation and issuance flow will be used?

What exact specification revision and library/version will Issuer and Holder implement? Which grant and offer/initiation flow, same-device or cross-device path, and required protocol features will the prototype support? Which optional features are explicitly outside the initial implementation?

**Needed output:** Implementation documentation, a selected-flow sequence and supported-feature list for Architecture/standards review. Do not infer the grant or credential format merely from an SDK name.

### AP-04 — What credential will be issued, and what can safely reach Registry?

Which credential/attestation format and authenticated claims express 18+ eligibility and the exact PFI commitment? What exact issuer-authenticated representation will Holder submit for admission without forwarding raw age evidence or unnecessary identity attributes? How will any issuance binding key relate to the CRS-ASC witness/commitment?

**Needed output:** A proposed format/claim-to-contract mapping and synthetic issuance-to-registration example, jointly reviewed with Crypto and Architecture. Generic key possession and arbitrary removal of signed fields are insufficient. If the format cannot meet both validation and minimization requirements, identify the blocker.

### AP-05 — Who can register commitments, and how are writes authorized?

Which authentication scheme and authorization policy protect `POST /v1/registrations`? Who is the authenticated principal, how does Holder obtain access, and how is retry identity scoped to that principal? What separate access policy applies to read-only snapshots without creating unnecessary verification tracking?

**Needed output:** Registration/read access rules, authentication lifecycle and configuration requirements. Coordinate the OpenAPI security definition and appropriate HTTP authentication responses with Architecture. Issuance authorization does not automatically grant Registry write access.

## Issuance — answer before enabling the selected flow

### AP-06 — Where will issuance authorization and token services run?

Will the Authorization Server and Token Endpoint be co-located with or separate from the Credential Issuer for the selected flow? Who operates them, what are the trusted identifiers/endpoints, and what must Holder and Issuer configure?

**Needed output:** Placement and dependency notes tied to AP-03, plus non-secret configuration examples. Record required deployment adjustments without treating placement as already selected.

### AP-07 — How will age eligibility and issuance decisions be handled?

What evidence or synthetic test workflow establishes 18+ eligibility? Who checks it, what makes issuance succeed or fail, and how are cancellation, repeat issuance and abandoned sessions handled? Which evidence is retained, by whom and for how long?

**Needed output:** An issuance decision flow and data-handling table distinguishing the synthetic prototype from any future real-evidence use. Keep raw evidence inside the Issuer boundary and coordinate minimum accountability with AP-23.

### AP-08 — How will the selected issuance profile address its protocol review areas?

For the selected profile, what validation, lifecycle and tests cover each of these eight areas?

1. Credential-offer/request manipulation.
2. Grant interception, injection or misuse, where applicable.
3. Issuance nonce/freshness handling.
4. Credential Endpoint replay and concurrent requests.
5. Holder/key binding, including its relationship to the PFI commitment.
6. Issuer metadata and endpoint trust.
7. Token storage, audience, lifetime, leakage and reuse.
8. Credential substitution or incorrect commitment binding.

**Needed output:** A profile-specific implementation/test mapping, with unsupported or inapplicable features explained. Coordinate cryptographic binding with Crypto. These remain review areas until the profile is chosen; do not invent threat/control IDs or substitute PFI verification challenges for issuance freshness.

### AP-09 — How are issuer trust and admission validity managed?

How are trusted issuer identifiers, selected-format verification material and allowed formats provisioned to Registry? How are signing material access, key rotation/compromise, credential expiry and trust changes handled? What does an already admitted commitment mean after its source credential expires or an issuer key changes?

**Needed output:** Trust provisioning and lifecycle rules, scoped runtime signing inputs, and an explicit admission-validity statement. Review with Architecture and Crypto where format validation is involved. Credential revocation infrastructure remains outside MVP; do not claim ongoing status checks.

## Registry — answer before admission and snapshot integration

### AP-10 — How will admission validation and the stored data model work?

Which validation adapter authenticates the selected issuer artefact, checks 18+, rejects substituted commitments and enforces the active suite? Which fields are persisted for validation/accountability, and which are excluded from both storage and public snapshots? How will migrations and access roles implement this model?

**Needed output:** Validation order, a minimal schema/migration proposal and a synthetic accepted/rejected registration pair. Crypto supplies commitment/encoding validation rules; Registry must not trust unsigned wrapper fields or arbitrary key URLs embedded in input.

### AP-11 — How will registration, uniqueness and snapshot publication be atomic?

What transaction/concurrency design makes admission, commitment uniqueness, immutable snapshot publication and successful idempotency-result storage one durable outcome? How are concurrent duplicate submissions and failures before/after commit handled without creating extra membership or changing a published version?

**Needed output:** A transaction design and race/failure tests. Preserve complete ordered sets, one active suite per namespace, and the proposed new-registration versus already-registered response semantics unless a reviewed contract change is recorded.

### AP-12 — What are the concrete idempotency and uncertain-write recovery rules?

How long are successful retry records retained, how is the caller/key mapped, and how are original request bytes/fingerprints stored without unnecessary data exposure? What bounded behavior applies to an in-progress identical retry, changed bytes under the same key, expired retry records and a lost success response?

**Needed output:** Retention/access rules and retry tests consistent with `common-types.md`. Revalidate current access on retries; returning recorded success must not create new membership or silently rerun admission under later trust conditions.

### AP-13 — What snapshot acceptance and anonymity policy applies to the demo?

Which historical snapshots are acceptable, what minimum anonymity-set size is required, and what retention policy applies to published versions? How will empty/insufficient sets and retired/unavailable versions be handled? Which limits require Crypto benchmark evidence?

**Needed output:** Explicit Holder/Verifier policy and Registry retention settings, reviewed with Architecture and Crypto. `registryVersion` is opaque and snapshots currently expose no timestamp; do not infer age from a version string. If policy needs extra metadata, propose a contract change first.

### AP-14 — How will Registry namespace, restore and reset behavior be implemented?

Who provisions the Registry identity and approved client configuration? How do backup/restore preserve each published namespace/version, suite and ordered set exactly? If state cannot be restored exactly, how will a new namespace and client reconfiguration be enforced?

**Needed output:** Initialization/reset/restore procedure and demonstration fixtures. No new backup service is required just to answer this question; distinguish the initial development procedure from later hosting requirements.

## Holder and Verifier — answer before real proof integration

### AP-15 — How will applications call the local crypto core and protect Holder material?

Which adapter/runtime boundary is needed on each client? How will a local `keyReference`, secret lifetime, randomness source, buffers, concurrency and errors work with the actual library? Does CRS-ASC need raw scalar access, and what storage, lock/unlock, restart and loss behavior can the chosen client honestly support?

**Needed output:** A joint App/Crypto interface and custody plan, including limitations and synthetic adapter tests. Do not assume ordinary browser storage or WASM memory provides hardware-backed isolation. Any secret export/recovery feature needs an explicit reviewed decision, not an implicit backup of application state.

### AP-16 — How will approved Verifier contexts be assigned and authenticated?

Who defines the service boundary represented by a context, and how are its identity and purpose provisioned to Verifier and validated by Holder? How are shared deployments, multiple instances, context changes and accidental reuse across separate services handled?

**Needed output:** A context ownership/trust mapping and configuration examples, reviewed with Architecture and Crypto. Crypto defines canonical mapping into derivation/transcripts. A self-declared `verifierId` is not authentication; shared contexts intentionally permit shared linkability.

### AP-17 — What presentation transport and Holder intent flow will be implemented?

Is the first presentation flow QR inline data, QR reference, an authenticated local transport, OpenID4VP, or another reviewed option? How does Holder know the intended Verifier and transaction? If references are used, how are endpoint trust, retrieval authorization, expiry, size bounds, session binding and leakage handled?

**Needed output:** A minimal transport/session contract and user interaction sequence. QR itself does not authenticate a peer. Record remaining live-relay limitations rather than claiming that freshness alone solves them; no remote proving/verification service is introduced.

### AP-18 — Where is challenge state owned, and how is one-attempt handling enforced?

Which process/device owns challenge state? How will creation, atomic reservation, cancellation, finalization, late workers, concurrent submissions, restart and lost state preserve the proposed one-attempt lifecycle? How are authoritative deadlines enforced during Registry retrieval and crypto work?

**Needed output:** A state-storage/concurrency design and race tests. Review the proposed 16-byte challenge ID, 32-byte nonce and 120-second lifetime with Crypto and measured budgets; they are draft values, not established performance results. Reserved attempts cannot be revived after failure, and success cannot occur after expiry or failed finalization.

### AP-19 — What local policy, if any, will valid nullifiers support?

What concrete service rule needs same-context linkability? Which repeat uses are allowed or rejected, over what scope/window, and what is retained, where, for how long and with what access/deletion controls? If no policy is selected for the initial milestone, how is that limitation made explicit?

**Needed output:** An Architecture-reviewed local policy and retention specification. Do not default to rejecting every repeated nullifier. Keep policy storage within Verifier, separate from incidental logs; no Registry nullifier storage or sharing for cross-service tracking.

### AP-20 — How will policy acceptance and challenge finalization interact?

After a proof/nullifier is cryptographically valid, how will any local nullifier check/update and final acceptance handle two concurrent challenges, cancellation, expiry or a crash? What result distinguishes a valid proof rejected by application policy from an invalid proof?

**Needed output:** A concurrency/failure design and proposed policy-rejection outcome, reviewed with Architecture and Crypto for the validity boundary. Unverified nullifiers must not poison policy state. The current contract does not yet define this policy outcome or its exact insertion into finalization.

### AP-21 — What input, processing and retry budgets will be enforced?

What encoded/decoded request, credential, proof and snapshot limits apply? What are the set-size, parsing, concurrency, request-rate, database, network and local crypto deadlines? Which values can be set now and which depend on Crypto measurements and AP-02 devices?

**Needed output:** A shared limits/budgets table with evidence and ownership. Enforce bounds before expensive work; never truncate a snapshot or crypto payload into a successful result. Keep retries within the owning challenge deadline and preserve the Registry's uncertain-write semantics.

### AP-22 — How will errors and retry behavior reach clients and operators?

How will implementations map Registry HTTP problems, selected-profile issuance errors and local crypto outcomes to the existing contract? How will malformed/non-JSON upstream responses, unsupported configuration, lost results, timeouts and exceptions fail safely without exposing internal data or producing duplicate acceptance?

**Needed output:** An error/retry mapping with tests and the AP-20 policy outcome proposal. Keep cryptographic invalidity, application-policy rejection and internal failure distinct. Do not automatically replay a presentation after a lost success response.

## Operational and integration handoff

### AP-23 — What is the minimum audit and retention policy for each component?

Which issuance audit fields support accountability, and how long are they needed? What operational records may Registry and Verifier keep? How will logs, traces, crash reports and any approved nullifier policy store avoid persistent issuance-to-verification linkage and unnecessary IP/timing correlation?

**Needed output:** A per-component field/purpose/retention/access table, including deletion and logging tests. Resolve the older blanket wording about verification-linkage with ADR-009's permitted local policy state; permission to retain scoped nullifiers is not permission to log proofs, credentials or tokens.

### AP-24 — How will real service images satisfy the scaffold's runtime contract?

Which repositories/build commands will produce Issuer and Registry images? How will entrypoints, unprivileged execution, environment/config loading, scoped secret files, database migrations and liveness/readiness behave? Which pending configuration disables which operations, including any explicit read-only Registry mode?

**Needed output:** Image/configuration integration notes and implemented readiness evidence when available. Adapt the scaffold's conventions explicitly if needed; do not assume the password-file loader, health endpoints or service images already exist. App readiness must reflect usable configuration/dependencies, not just a running process.

### AP-25 — What changes are needed for the first networked demonstration?

Will the first run remain synthetic and on one host, or require separate devices? If network access is needed, what trusted service URLs, TLS termination/certificates, client trust and browser-origin rules are required? Who has a Docker-capable machine and owns the first actual startup check?

**Needed output:** A bounded development/demo connection plan and runtime-check owner. Current loopback HTTP is a synthetic single-host exception, not the networked security baseline. PostgreSQL stays internal with no published port; production hosting is not selected by this questionnaire.

### AP-26 — What evidence will make the first integration milestone complete?

What can the App Owner deliver independently now, what awaits Crypto decisions/vectors, and who owns each cross-component test? When will the team demonstrate issuance, controlled registration, exact snapshots, local proofs, same-context policy behavior and cross-context separation using synthetic wallets?

**Needed output:** A short dependency/milestone list and acceptance plan. Cover duplicate registration races, replay races, expiry during work, restart, unavailable/wrong state, tampered binding/nullifiers, policy concurrency, malformed/oversized messages and privacy checks. Distinguish library tests, application tests, Docker startup and full integration evidence.

## Joint contract follow-ups to record with Architecture

These are coordination questions, not authorization to silently rewrite the contracts:

- The conceptual crypto-call snippet in `holder-verifier-protocol.md` still omits nullifier input/output although its main flow and message schema include it. Who will synchronize the selected adapter contract and examples? See AP-15.
- Which approved local policy state is permitted under ADR-009, and how will broad no-linkage wording distinguish it from unnecessary logs? See AP-19/AP-23.
- What policy-rejection result and atomic finalization rules complete the contract? See AP-20/AP-22.
- Can historical-state policy be implemented using current fields, or does it require a reviewed change? See AP-13.

## Source basis

Prepared from the local project documents reviewed on 13 September 2026:

- `pfi-architecture/api-contracts/README.md`, `common-types.md`, `registry-api.yaml`, `holder-verifier-protocol.md`, `error-model.md`, `openid4vci-profile.md`.
- Accepted `pfi-architecture/adr/ADR-009-verifier-scoped-nullifiers.md` and the existing Holder custody, registration and snapshot boundaries.
- `pfi-infrastructure/docs/IMPLEMENTATION_HANDOFF.md`, `SERVICE_MAP.md`, `VALIDATION.md` and the Issuer/Registry/Verifier configuration templates.

The questions seek owner evidence and decisions; they do not claim that any unresolved value, runtime, security control or standards profile is implemented or verified.
