# Lesson 2: Contain an unsafe model request

## What was built

`app/mock_injection.py:simulated_model_request` replays two explicit fixtures: a safe request for Alex's account and a malicious prompt that makes the simulated model request Blair's account. It is a scripted model failure, not a real LLM, classifier, injection detector, or general prompt parser.

`app/security_demo.py:run_demo` logs in as Alex, obtains the mock request, and submits it to the actual `/chat` handler using TestClient. The same mock model follows the malicious instruction in BOTH modes. Only the authorization setting changes. The authenticated identity remains Alex.

Flow: prompt -> mock account selection -> authenticated chat API -> account ownership check -> answer or refusal.

## Practice

From the BankAssist folder:

```sh
.venv/bin/python -m app.security_demo --scenario mock-injection --mode baseline
```

Inspect the authenticated actor, requested account, and returned fictional balance. After discussing the result, rerun with `--mode hardened`. It should refuse while the safe request continues to work. Each invocation resets its in-memory sessions and audit events; it starts no listening server and makes no external model call.

HTTP 200 on `/chat` means the API delivered a response. Read `attack_outcome` to distinguish an answer from a refusal. The direct account route instead uses HTTP 403 for denial.

## Finding BA-002: Missing ownership enforcement exposes data after simulated model manipulation

- Attack scenario: Alex submits an identity-override instruction and the mocked model selects Blair's account.
- Affected asset: fictional Blair savings balance.
- Trust boundary: untrusted model-selected account reference crossing into authorized data retrieval.
- Impact: another fictional customer's account information is disclosed.
- Reproduction: run the baseline command above.
- Expected insecure behavior: the chat answers using Blair's account.
- Observed behavior: outcome answered, balance 508900 cents disclosed.
- Risk: High within the fictional lab, qualitative assessment based on cross-user financial-data disclosure. No real banking exposure is claimed.
- Remediation: enforce session-bound ownership checks independent of model behavior.
- Retest: same command with hardened mode returns refused and the generic account refusal.
- Residual risks: this tests only a scripted attack path. Real-model behavior, arbitrary prompt variants, prompt filtering, and production authorization remain unproven.

## Executed evidence

Working directory: BankAssist. Executed 2026-09-16.

| Exact command | Observed output excerpt | Status and meaning |
|---|---|---|
| `.venv/bin/python -m app.security_demo --scenario mock-injection --mode baseline` | `attack_outcome: answered`, `result: FINDING DEMONSTRATED`, `verification: PASS` | PASS: expected disclosure reproduced |
| `.venv/bin/python -m app.security_demo --scenario mock-injection --mode hardened` | `attack_outcome: refused`, `answer: I cannot provide that account information.`, `result: CONTAINED BY AUTHORIZATION`, `verification: PASS` | PASS: same requested object refused |
| `.venv/bin/python -m pytest` | `16 passed in 0.08s` | PASS: regression suite including three paired replays, state cleanup, and unsupported-prompt rejection |

Both runs returned `safe_request_outcome: answered`, `authenticated_actor: alex`, and `model_requested_account: acct_demo_blair_savings`. No runtime failure was observed in these checks.

NOT VERIFIED: frontend interaction, real LLM injection resistance, general injection detection, MITRE technique-ID mapping, baseline isolation from the normal server configuration.

Interview wording: "I simulated a model following a malicious instruction and verified that application authorization still prevented another user's data from being returned."
