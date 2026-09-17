# BA-003: Weak mode selectable through normal server configuration

## Finding

Scenario: an operator sets BANKASSIST_SECURITY_MODE=baseline while launching the normal application. The original configuration accepts it and disables account ownership checks. This crosses the boundary between an intentional test bypass and normal serving configuration.

Affected asset: fictional account data accessible through the normal API. Impact: accidental cross-account disclosure if a weak server is run. Risk: High within the lab, qualitative rating; no external exposure was observed or tested.

Root cause: `get_settings` explicitly accepted baseline and the application did not validate configuration at startup. Documentation said test-only, but code did not enforce that restriction.

## Reproduction and before evidence

Command: `.venv/bin/python -m pytest tests/security/test_release_configuration.py -q`

Actual pre-fix output: `FF..F.`; three tests failed, each with `Failed: DID NOT RAISE <class 'ValueError'>`:

- test_normal_environment_cannot_enable_baseline
- test_application_startup_rejects_baseline
- test_invalid_or_weak_configuration_fails_closed[BASELINE]

Expected secure behavior: reject weak configuration. Actual insecure behavior: settings and TestClient application startup accepted it. The uppercase variant was accepted too.

## Remediation

Normal settings now accept only hardened mode. An application lifespan check validates settings before serving requests. Weak demonstrations use an explicit in-process dependency override in TestClient, restored afterward. The original baseline test was updated to use that isolated override; the new tests independently assert that environment-based baseline is rejected.

## After evidence, executed 2026-09-16

| Exact command from BankAssist | Actual output | Assessment |
|---|---|---|
| `.venv/bin/python -m pytest tests/security/test_release_configuration.py` | `6 passed in 0.01s` | PASS: rejection, invalid settings, normal default startup |
| `.venv/bin/python -m pytest` | `61 passed in 0.14s` | PASS: regression suite |
| `.venv/bin/python -m app.security_demo --mode baseline` | owner 200, attack 200, FINDING DEMONSTRATED, verification PASS | PASS: isolated exercise still reproduces weakness |
| `.venv/bin/python -m app.security_demo --mode hardened` | owner 200, attack 403, BLOCKED, verification PASS | PASS: hardened demonstration retains legitimate access |
| `BANKASSIST_SECURITY_MODE=baseline .venv/bin/python -m uvicorn app.main:app --host 127.0.0.1 --port 0` | `ValueError: Normal startup requires hardened mode; baseline is available only in isolated exercises`; `Application startup failed. Exiting.`; exit 3 | PASS: actual server refuses weak startup |

Port 0 requested an OS-assigned local port, but startup failed before serving. No weak network server was released by this verification. Normal default startup was tested through application lifespan/TestClient; a normal listening server was not rerun in this change.

## Limits

This is an accidental-configuration safeguard, not a security boundary against someone who can modify Python code or install overrides. Other unfinished controls remain unfinished. Earlier evidence documents reporting this as a blocker describe their historical state; this document records its resolution. No real-model inference was rerun because the model adapter was unchanged; frontend and deployment QA remain NOT VERIFIED.
