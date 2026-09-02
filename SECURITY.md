# Security Policy

## 1. Purpose

The **Privacy for Identities (PFI)** project maintained by **KyoSys** contains security-sensitive architecture and cryptographic functionality.

This policy defines minimum security requirements for PFI design, development, testing, review, and demonstration.

## 2. Security Principles

PFI follows:
- privacy by design;
- least privilege;
- data minimisation;
- local custody of Holder private/master secret material;
- trusted Issuer attestations;
- integrity-protected Registry state;
- replay-resistant verification;
- independent Registry retrieval by the Verifier;
- minimal logging;
- synthetic test data only;
- no secrets in source control.

## 3. Prohibited Content in Source Control

Never commit:
- Holder private keys or master secrets;
- Issuer signing private keys;
- authentication tokens;
- passwords;
- database credentials;
- cloud credentials;
- private API keys;
- real identity documents;
- real age evidence;
- personally identifiable information (PII);
- production certificates/private certificate material;
- unencrypted secret backups;
- populated `.env` files containing secrets.

This applies even when the repository is private.

If a secret is accidentally committed, treat it as compromised and rotate/revoke it where applicable.

## 4. Test Data

Development and testing must use:
- synthetic identities;
- synthetic age evidence;
- test-only cryptographic material;
- non-production credentials;
- isolated development/test environments.

Real government identity documents or real user identity data must not be committed or used for routine prototype development.

## 5. Cryptographic Key Handling

### Holder keys

The Holder private/master secret must remain within the Holder environment and must not be transmitted to the Issuer, Registry, or Verifier.

Applications should use a local key reference/handle where practical rather than unnecessarily passing raw secret material between components.

### Issuer signing keys

Issuer signing keys are high-value secrets and must not be:
- stored in Git;
- embedded in container images;
- hard-coded in source;
- included in logs;
- shared through Issues or PRs.

Development/test signing keys must remain separate from any future production-grade key material.

## 6. Secrets and Environment Configuration

Use `.env.example` to document required variable names without real secret values.

Typical exclusions:

```text
.env
.env.*
!.env.example
*.key
*.pem
*.p12
*.pfx
secrets/
keys/
```

Repository/environment secret access must follow least privilege.

## 7. Secure Development Requirements

Security-sensitive changes require explicit review, including changes to:
- proof generation or verification;
- key handling;
- Issuer attestation generation;
- Issuer signature validation;
- trust anchors;
- Registry insertion logic;
- Registry snapshot/version handling;
- challenge generation/replay protection;
- authentication/authorisation;
- logging/data retention;
- external interfaces;
- container security;
- CI/CD permissions.

Such PRs must document security/privacy impact.

## 8. Threat Model Alignment

Implementation must remain consistent with the approved PFI Threat Model.

Key threats include:
- forged Issuer attestations;
- Registry tampering/malicious entries;
- Holder private-key compromise;
- replayed proofs;
- live relay attacks;
- verifier tracking/linkability;
- MITM/API tampering;
- compromised Issuer signing keys;
- manipulated Registry snapshots/versions;
- malicious or untrusted Verifiers;
- excessive logging/privacy leakage;
- Registry unavailability.

Update the Threat Model when a change introduces or materially alters a threat.

## 9. Minimum Security Controls

Where applicable, the architecture expects:
- Issuer digital signatures;
- trusted Issuer verification keys/trust anchors;
- Holder-local key custody;
- fresh unlinkable proof generation;
- fresh nonce/challenge per verification;
- single-use challenge state;
- challenge expiry;
- HTTPS/TLS for network service calls;
- versioned Registry snapshots;
- independent Registry retrieval by the Verifier;
- controlled Registry writes;
- minimal logging;
- data minimisation;
- rejection of stale/replayed/already-consumed challenges.

Controls must not be weakened without approved architecture/security review.

## 10. Logging

Do not log:
- Holder private/master secrets;
- Issuer signing keys;
- raw age evidence;
- unnecessary PII;
- reusable proof material where avoidable;
- stable identifiers that unnecessarily enable cross-session correlation.

Log only the minimum required for troubleshooting, security-event analysis, status, errors, and Registry diagnostics.

## 11. Dependency and Supply-Chain Security

Where available, enable:
- Dependabot alerts;
- Dependabot security updates;
- dependency review;
- secret scanning;
- push protection;
- code scanning;
- appropriate static analysis.

Cryptographic dependencies require additional scrutiny.

## 12. Container and Build Security

Container images/build artefacts must not contain private keys, passwords, source-control tokens, secret-bearing `.env` files, or unnecessary development tooling in runtime images.

Prefer reproducible builds and minimal runtime images.

## 13. Vulnerability Reporting

Do **not** disclose suspected vulnerabilities, secrets, or exploit details in a public GitHub Issue.

Where available, use GitHub private vulnerability reporting or a Security Advisory. Otherwise, contact a KyoSys repository maintainer or project security/architecture owner through the approved private project channel.

Include:
- affected repository/component;
- description;
- reproduction conditions where safe;
- likely impact;
- affected version/commit if known;
- suggested mitigation if available.

Do not include real identity data or active secrets.

## 14. Accidental Secret Exposure

If a secret/private key is accidentally committed:

1. Stop using the exposed value.
2. Notify the project security owner.
3. Rotate or revoke the value where applicable.
4. Remove it from the working tree.
5. Assess whether Git history requires remediation.
6. Review downstream exposure.
7. Document corrective action.
8. Review controls to prevent recurrence.

Deleting the file in a later commit does not remove the secret from Git history.

## 15. Security Review Triggers

Request architecture/security review when a change:
- crosses a new trust boundary;
- introduces a new external service;
- changes cryptographic parameters;
- changes key storage/lifetime;
- changes Registry trust validation;
- changes challenge/replay logic;
- changes logging/retention;
- changes proof payload;
- changes API authentication;
- changes container/network exposure;
- introduces a new data store;
- introduces a new identity attribute.

## 16. Prototype Status

PFI is currently a prototype/research environment.

These controls demonstrate good engineering posture and protect development/test activity. They are not a claim that the prototype is production-certified, legally accredited, or suitable for processing real government identity data.
