# Privacy redaction control

Date: 2026-09-16

`app/privacy.py` applies deterministic redaction to model summaries and guidance before a browser response is returned. It covers common email, phone, card/long-number and tax-ID patterns. The browser uses `textContent`, and the validated proposal copy is redacted too. Requests and audit records do not store passwords, tokens, prompts, balances or account identifiers.

Verification: `.venv/bin/python -m pytest` returned `85 passed in 0.24s`. The focused suite verifies all four patterns, unchanged non-PII, model summary and proposal redaction, and existing regressions.

Real browser smoke after server restart: pending-transaction question returned HTTP 200 and `outcome: answered`; answer contained no balance marker or PII marker. This confirms the normal general-question flow remains functional after the privacy change.

Limits: pattern matching is best effort and not a complete privacy classifier. Encoded, obfuscated or novel identifiers require future tests. Account balances intentionally appear for an authorized fictional owner request; that is controlled fixture data, not PII redaction evidence.
