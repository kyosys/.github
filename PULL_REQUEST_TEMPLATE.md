# Pull Request

## Summary

Provide a concise description of what this PR changes and why.

## Linked Issue

Closes #

Related Issue(s):
- #

> Every implementation change should originate from a tracked Issue.

## Change Type

- [ ] Feature
- [ ] Bug fix
- [ ] Security change
- [ ] Documentation
- [ ] Test
- [ ] Refactor
- [ ] Infrastructure / CI
- [ ] Architecture / design change
- [ ] Other

## Changes

- 
- 
- 

## Acceptance Criteria

- [ ] Acceptance criteria have been met.
- [ ] Any unmet criteria are documented below.

Notes:

## Architecture Impact

Does this change affect approved architecture, interfaces, data flows, trust boundaries, cryptographic behaviour, or deployment?

- [ ] No architecture impact identified.
- [ ] Architecture impact identified and reviewed.
- [ ] Relevant diagram(s) updated.
- [ ] API/message contract updated.
- [ ] ADR created or updated.

Affected artefacts:

- [ ] System Context Diagram
- [ ] Container Diagram
- [ ] Credential Issuance Sequence
- [ ] Age Verification Sequence
- [ ] Trust Boundary / Data Flow Diagram
- [ ] Threat Model
- [ ] Deployment Diagram
- [ ] Standards Alignment
- [ ] API / message contract
- [ ] ADR
- [ ] None

Architecture notes:

## Security & Privacy Impact

- [ ] No material security/privacy impact identified.
- [ ] Security/privacy impact reviewed.
- [ ] Threat Model updated.
- [ ] Security controls added or changed.
- [ ] Key handling changed.
- [ ] Trust validation changed.
- [ ] Challenge/replay logic changed.
- [ ] Logging/data-retention behaviour changed.
- [ ] New dependency introduced.
- [ ] New network/API exposure introduced.

Security/privacy notes:

## Testing Performed

- [ ] Unit tests
- [ ] Integration tests
- [ ] Negative tests
- [ ] Security tests
- [ ] Replay-resistance tests
- [ ] Invalid-proof tests
- [ ] Invalid-attestation tests
- [ ] Registry-version tests
- [ ] Manual validation
- [ ] Not applicable

Test evidence / results:

```text
Add relevant output, commands, screenshots, or references here.
```

## Documentation

- [ ] README updated where required.
- [ ] Architecture documentation updated where required.
- [ ] API documentation updated where required.
- [ ] ADR created/updated where required.
- [ ] Test documentation updated where required.
- [ ] Operational/setup documentation updated where required.
- [ ] No documentation change required.

## Dependency / Configuration Changes

- [ ] Dependencies
- [ ] Environment variables
- [ ] Database schema/migrations
- [ ] Container configuration
- [ ] CI/CD configuration
- [ ] Network ports/endpoints
- [ ] None

Details:

## Contributor Security Checklist

- [ ] No Holder private/master key is committed.
- [ ] No Issuer signing private key is committed.
- [ ] No passwords, API tokens, or database credentials are committed.
- [ ] No real identity documents or age evidence are committed.
- [ ] No unnecessary PII is included.
- [ ] Synthetic test data is used.
- [ ] Secrets are supplied through approved runtime/local mechanisms.
- [ ] Input validation and error handling were considered.
- [ ] Logging does not unnecessarily expose sensitive/linkable data.
- [ ] New dependencies have been reviewed.

## Contributor Checklist

- [ ] The change originates from a GitHub Issue.
- [ ] The branch follows the KyoSys branch naming convention.
- [ ] Commit messages are clear and appropriately scoped.
- [ ] The implementation matches approved design/architecture.
- [ ] Relevant tests pass.
- [ ] Documentation is current.
- [ ] Security/privacy impact has been considered.
- [ ] The PR is ready for review.

## Reviewer Checklist

- [ ] Linked Issue and acceptance criteria are satisfied.
- [ ] Architecture remains consistent.
- [ ] Security/privacy implications are acceptable.
- [ ] Test coverage is appropriate.
- [ ] Negative/error paths are considered.
- [ ] No secrets or real identity data are present.
- [ ] Documentation is complete and accurate.
- [ ] Dependencies/configuration changes are justified.
- [ ] Review conversations are resolved.

## Additional Notes

Add implementation notes, limitations, follow-up work, or known risks here.