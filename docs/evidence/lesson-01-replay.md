# Authorization replay verification

Executed from the BankAssist directory:

| Command | Actual result | What it proves |
|---|---|---|
| `.venv/bin/python -m app.security_demo --mode baseline` | owner_request_status=200, attack_status=200, FINDING DEMONSTRATED, verification=PASS | The fictional cross-account disclosure is reproduced |
| `.venv/bin/python -m app.security_demo --mode hardened` | owner_request_status=200, attack_status=403, BLOCKED, verification=PASS | Same API request is denied while owner access remains available |
| `.venv/bin/python -m pytest` | `14 passed in 0.06s` | Regression suite passes, including three consecutive baseline/hardened replay cycles and session/audit/override cleanup |

All three commands exited 0. No listening server was started by this exercise. Browser behavior, real-model injection resistance, durable evidence export, and baseline server isolation remain NOT VERIFIED.
