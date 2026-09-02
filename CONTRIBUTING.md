# Contributing to Privacy for Identities (PFI)

This repository is maintained by **KyoSys** org and follows a controlled, documentation-led development workflow intended to mirror professional corporate engineering practice.

## 1. Core Development Rules

1. Do not develop directly on `main`.
2. Every change must originate from a tracked GitHub Issue.
3. Architecture, security, API, or design documentation must be updated before implementation where the change affects them.
4. Use a dedicated branch for every change.
5. All changes must be merged through a Pull Request (PR).
6. At least one reviewer must approve a PR before merge.
7. Relevant tests must pass before merge.
8. Security and privacy impact must be considered for every change.
9. Documentation must remain consistent with implementation.
10. Secrets, real identity information, and private cryptographic material must never be committed.

Expected workflow:

```text
Requirement / Issue
        ↓
Design / Architecture Review
        ↓
Documentation / ADR Update (if required)
        ↓
Dedicated Branch
        ↓
Implementation
        ↓
    Testing
        ↓
Pull Request
        ↓
Peer Review
        ↓
Merge to main
        ↓
Integration / Validation
```

## 2. Issue-First Workflow

Before making a change, create or identify the GitHub Issue that defines the work.

Where applicable, an Issue should contain:
- problem or requirement;
- expected outcome;
- acceptance criteria;
- architecture impact;
- security/privacy impact;
- testing requirements;
- documentation requirements;
- dependencies or blockers.

A Pull Request must reference its originating Issue.

Example:

```text
Issue #42: Implement Registry snapshot versioning
Branch: feature/42-registry-snapshot-versioning
PR: feat(registry): implement versioned snapshot retrieval
```

## 3. Branching Strategy

The `main` branch represents reviewed and accepted repository state. Direct pushes to `main` are prohibited after repository bootstrap.

Use:

```text
feature/<issue-number>-<description>
fix/<issue-number>-<description>
security/<issue-number>-<description>
docs/<issue-number>-<description>
test/<issue-number>-<description>
```

Examples:

```text
feature/42-registry-snapshot-versioning
fix/51-invalid-attestation-validation
security/57-challenge-replay-validation
docs/61-update-threat-model
test/73-invalid-proof-submission
```

Branch names must be lowercase, hyphen-separated, scoped to one unit of work, and include the related Issue number.

## 4. Architecture and Design Changes

PFI uses a documentation-led engineering model.

Before implementing a change, determine whether it affects:
- System Context Diagram;
- Container Diagram;
- Credential Issuance Sequence;
- Age Verification Sequence;
- Trust Boundary / Data Flow Diagram;
- Threat Model;
- Deployment Diagram;
- API/message contracts;
- Architecture Decision Records (ADRs);
- standards/reference alignment documentation.

If an accepted architectural decision changes, create or update an ADR before implementation is merged.

Code must not silently diverge from approved architecture.

## 5. Architecture Decision Records

Significant technical decisions must be documented in `adr/`.

Filename convention:

```text
ADR-###-short-decision-name.md
```

Each ADR should contain:
- Status
- Context
- Decision
- Rationale
- Consequences

## 6. Commit Convention

Use clear, scoped Conventional Commit-style messages where practical.

Examples:

```text
feat(registry): add versioned snapshot retrieval
fix(issuer): reject invalid attestation binding
security(verifier): reject consumed challenge
docs(architecture): update threat model
test(holder): add invalid proof test case
refactor(crypto): isolate proof serialization
chore(ci): update workflow configuration
```

Avoid vague commits such as `update`, `changes`, `stuff`, `working`, or `final`.

## 7. Pull Request Requirements

Every non-bootstrap change must be submitted through a PR.

A PR must:
- link the originating Issue;
- describe the change;
- identify architecture impact;
- identify security/privacy impact;
- describe testing performed;
- identify documentation updates;
- confirm that no secrets or real identity data are included.

Before merge:
- at least one reviewer must approve;
- review conversations must be resolved;
- required tests/checks must pass;
- documentation must be updated where required;
- acceptance criteria must be satisfied.

## 8. Testing Expectations

Testing must be proportionate to the change.

Where relevant, include:
- unit tests;
- integration tests;
- negative tests;
- security tests;
- replay-resistance tests;
- invalid-proof tests;
- invalid-attestation tests;
- Registry-version mismatch tests;
- error-handling tests.

Changes affecting cryptographic or security-sensitive behaviour require explicit negative/security test coverage.

Only synthetic test data may be used.

## 9. Security and Privacy Requirements

Never commit:
- Holder private/master keys;
- Issuer signing keys;
- API tokens;
- passwords;
- database credentials;
- real identity documents;
- real age evidence;
- personal information;
- production certificates;
- populated secret-bearing `.env` files.

Use `.env.example` to document required variable names without secret values.

See `SECURITY.md` for the full policy.

## 10. Documentation Requirements

Documentation is part of the deliverable.

When implementation changes behaviour, update the relevant documentation in the same PR or in a preceding approved documentation PR.

Editable source artefacts such as `.drawio` files are authoritative where applicable; PNG/PDF exports are presentation artefacts.

## 11. Review Expectations

Reviewers should verify:
- the Issue and acceptance criteria are satSWisfied;
- architecture remains consistent;
- security/privacy impacts were considered;
- tests are appropriate;
- error paths are handled;
- no secrets are present;
- documentation is current;
- naming/interfaces remain consistent;
- scope has not expanded without approval.

## 12. Merge Policy

The preferred strategy is **Squash and merge** where practical, using a clear final commit message.

## 13. Contributor Checklist

Before opening a PR:

- [ ] A GitHub Issue exists.
- [ ] The branch follows the project naming convention.
- [ ] Architecture/design documentation was reviewed.
- [ ] An ADR was created or updated if required.
- [ ] Relevant tests were added or updated.
- [ ] Tests pass.
- [ ] Security/privacy impact was considered.
- [ ] Documentation is current.
- [ ] No secrets are committed.
- [ ] No real identity or age-evidence data is committed.
- [ ] The PR links the originating Issue.

If a proposed change affects trust boundaries, cryptographic behaviour, identity data handling, Registry state, verifier behaviour, or security controls and the correct approach is unclear, request architecture/security review before implementation.
W