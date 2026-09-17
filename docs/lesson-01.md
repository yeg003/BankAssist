# Lesson 1: Login does not grant access to every account

Objective: explain authentication versus object authorization, reproduce a controlled access failure, then compare the identical request with ownership enforcement enabled.

Authentication answers "Who are you?" Authorization answers "May you read this particular account?" Alex logging in does not give Alex permission to read Blair's savings account.

Source: `app/security.py`, `may_view_account`. The baseline returns True without checking ownership; hardened mode compares the session's actor ID with the fixture account's owner ID. `app/main.py`, `get_account`, calls that check before returning account fields.

The runner uses FastAPI TestClient to exercise the actual API handlers in the same process. No server or browser is required. Each run creates a fictional session and clears sessions, audit events, and its configuration override afterward. No token is printed. This is an authorization demonstration, not evidence of prompt injection resistance or a real model attack.

From the BankAssist directory on this Mac:

```sh
.venv/bin/python -m app.security_demo --scenario cross-account --mode baseline
```

Expected: Alex's own request is 200, Blair's account request is also 200, and the result is FINDING DEMONSTRATED. Verification PASS means the vulnerable behavior was reproduced, not that the application is secure.

After inspecting that result with your mentor, rerun with `--mode hardened`. Expected: owner request 200, other owner's request 403, result BLOCKED. This selects an already implemented fix; it does not edit source code or prove every authorization path is secure.

Learner checkpoint: which comparison is missing in the baseline, and why does login alone fail to protect Blair's account?

Known limitations: sessions and audit events are in memory; no session expiry, password hashing, review queue, or browser exists yet. Normal server startup now rejects baseline environment configuration; weak exercises use an in-process override. Existing high-risk escalation is only a response label, not delivery to a human reviewer.
