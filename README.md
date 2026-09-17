# BankAssist

An independent secure AI banking-support chatbot case study using fictional users, mock accounts, and local security testing.

This repository is a portfolio presentation of the project. It intentionally does not publish application source code, credentials, real banking data, or deployment configuration.

## What this case study demonstrates

- Prompt-injection and indirect-injection testing
- Cross-account authorization failure and remediation
- Backend ownership checks and tool allowlisting
- Structured response validation and PII redaction
- Human review for high-risk requests
- Before-and-after security evidence
- OWASP Top 10 for LLM Applications and MITRE ATLAS mapping

## Evidence and visuals

- [Browser screenshots](docs/evidence/browser/)
- [Security findings and remediation evidence](docs/evidence/)
- [Architecture](docs/architecture.md)
- [Threat model](docs/threat-model.md)
- [Security controls](docs/security-controls.md)
- [Framework mapping](docs/security-framework-mapping.md)
- [Interview preparation](docs/interview-preparation.md)
- [Carousel visuals](docs/evidence/linkedin-generated/)

## Scope

BankAssist is a local, fictional case study. It does not connect to production banking systems, move money, make financial decisions, or use real customer information. AWS deployment is documented as a future design only.

## Project story

The project compares an intentionally weak baseline with a hardened design. In the baseline scenario, a prompt changed the requested account and data was returned. After remediation, deterministic backend authorization returned HTTP 403 and blocked the request.

See [claims and evidence](docs/claims-and-evidence.md) for the exact boundary between demonstrated results and future work.
