# Card authorization before-and-after

Run both modes locally:

```bash
./.venv/bin/python -m app.security_demo --scenario card-cross-account --mode baseline
./.venv/bin/python -m app.security_demo --scenario card-cross-account --mode hardened
```

Expected result: baseline returns `200` for Alex reading Blair's fictional card and reports `FINDING DEMONSTRATED`; hardened returns `403` and reports `BLOCKED`. The test replay is `tests/security/test_card_baseline_replay.py`.
