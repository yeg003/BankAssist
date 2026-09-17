# Tool-permission evidence

The model may propose only a read-only account lookup or a refusal/summary. The backend has a second explicit allowlist: only `lookup_account` is a callable tool, and it still requires deterministic ownership. Money movement, ownership changes, card issuance, and deletion are not callable capabilities.

Verification:

```bash
./.venv/bin/python -m pytest -q tests/security/test_tool_policy.py tests/integration/test_chat.py
```

This demonstrates least privilege and defense in depth. A syntactically valid model proposal does not grant a new tool permission.
