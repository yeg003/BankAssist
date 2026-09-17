# Acceptance evidence snapshot

Generated during the local verification pass.

| Criterion | Status | Exact evidence |
|---|---|---|
| Local startup and health | PASS | `curl --fail http://127.0.0.1:8008/health` returned `{"status":"ok","security_mode":"hardened"}` |
| Authentication and expiry | PASS | `./.venv/bin/python -m pytest -q tests/security/test_session_security.py` |
| Account, card, transaction authorization | PASS | authorization, card, and transaction replay tests |
| AI safety controls | PASS scoped | full Python suite and local Qwen attack suite |
| Review queue | PASS | `./.venv/bin/python scripts/qa_reviewer.py` |
| Browser interface | PASS | `./.venv/bin/python scripts/qa_browser.py` returned 25 checks passed |
| OWASP and ATLAS mapping | PASS scoped | `docs/security-framework-mapping.md`; LLM08 is not applicable |
| Direct dependency reproducibility | PASS scoped | `./.venv/bin/python scripts/verify_supply_chain.py` |
| Dependency consistency | PASS | `./.venv-py311/bin/python scripts/dependency_check.py`; clean Python 3.11 environment is consistent |
| Dependency vulnerability remediation | PASS scoped | Hashed Python 3.11 lock audit reported no known vulnerabilities; legacy Python 3.9 retains open findings |
| Reset and troubleshooting instructions | PASS scoped | `scripts/reset_local.py` and README runbook |
| Baseline and hardened evidence | PASS | account, card, transaction, injection, and tool-misuse reports |
| No real data or credentials | PASS scoped | fixture and audit privacy tests |
| Course repository unchanged | APPROVED EXCEPTION | User confirmed the pre-existing reference-file changes and `.DS_Store` files should remain; BankAssist did not modify them |
| AWS accessed | PASS | no AWS commands or resources used |

This is an evidence snapshot, not a production security certification.

The consolidated check is `./.venv-py311/bin/python scripts/final_audit.py`. It verifies required portfolio files, the Python test suite, the hashed-lock scan, and the local health endpoint.

## Original deliverable audit

| # | Requirement | Status | Evidence |
|---:|---|---|---|
| 1 | Browser chat interface | PASS | 25-check browser QA |
| 2 | Real local model connection | PASS scoped | Ollama Qwen3 4B runs and attack-suite reports |
| 3 | Password hashing and session expiry | PASS | Session security tests |
| 4 | Mock cards and payment support | PASS scoped | Read-only card endpoint; payments intentionally unavailable |
| 5 | Transaction-access tests | PASS | Owner, foreign, missing, and replay tests |
| 6 | Sensitive-data detection and redaction | PASS scoped | Privacy redaction tests |
| 7 | Model response validation | PASS scoped | Strict proposal schema and browser rendering tests |
| 8 | Tool allowlisting and misuse tests | PASS scoped | Tool policy and misuse replay |
| 9 | Local human-review queue | PASS scoped | API, CLI, and reviewer browser QA |
| 10 | Security logs without secrets | PASS scoped | JSONL privacy test |
| 11 | Rate limiting | PASS scoped | Limiter and browser 429 tests |
| 12 | Expanded attacks and before/after evidence | PASS scoped | Account, card, transaction, injection, tool reports |
| 13 | OWASP and MITRE mapping | PASS scoped | Framework matrix with residual statuses |
| 14 | Feature and browser QA | PASS scoped | Full Python suite and 25 browser checks |
| 15 | Setup, release, reset, troubleshooting | PASS scoped | README and lesson runbooks |
| 16 | Architecture, threat model, security docs | PASS | Required docs exist and are non-empty |
| 17 | Interview and recording preparation | PASS scoped | Interview and recording documents |
| 18 | AWS extension without deployment | PASS design-only | AWS extension plan; no AWS access |
| 19 | Final acceptance verification | PASS WITH DOCUMENTED EXCEPTION | All BankAssist checks pass; the user-approved pre-existing course-repository changes remain outside the project scope |
