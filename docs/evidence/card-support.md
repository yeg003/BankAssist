# Mock card support evidence

BankAssist now exposes fictional read-only card status through `GET /cards/{card_id}`. The hardened route checks the authenticated session against the card owner. Alex can view `card_demo_alex_physical`; Alex receives a generic `403` for Blair's `card_demo_blair_virtual`.

Verification:

```bash
./.venv/bin/python -m pytest -q tests/security/test_card_authorization.py
```

No payment, replacement, ownership-change, or other financial action is implemented.
