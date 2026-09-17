# Transaction authorization before-and-after

Run both modes locally:

```bash
./.venv/bin/python -m app.security_demo --scenario transaction-cross-account --mode baseline
./.venv/bin/python -m app.security_demo --scenario transaction-cross-account --mode hardened
```

The transaction endpoint is the existing structured chat route with a transaction identifier. Baseline intentionally skips ownership and answers Alex with Blair's fictional transaction. Hardened keeps the HTTP response structured but changes the outcome to `refused`, without disclosing the transaction. Missing transaction IDs are refused in both modes.
