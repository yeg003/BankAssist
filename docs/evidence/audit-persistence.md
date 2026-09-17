# Local audit persistence evidence

BankAssist keeps privacy-minimized events in memory for tests and can optionally append the same small event shape to a local JSONL file using `BANKASSIST_AUDIT_LOG`. Events contain only event type, outcome, and fictional actor ID. Passwords, tokens, prompts, balances, and account IDs are not written.

Verification:

```bash
./.venv/bin/python -m pytest -q tests/security/test_audit_persistence.py
```

This supports local release evidence only. A production design would need protected centralized storage, retention, integrity controls, access monitoring, and alerting.
