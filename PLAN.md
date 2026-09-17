# BankAssist Plan

## Objective

Build an independent secure AI banking-support chatbot case study using only fictional data. Demonstrate an evidence-backed Build, Release, Break, Harden lifecycle. This is not an American Express project and makes no claim of access to, employment by, or work for American Express.

## Scope

- Local Python/FastAPI application with a lightweight browser interface.
- Offline fixtures, a deterministic mock AI planner, and no Docker or cloud resources in phase one.
- Authorization, safe responses, fraud guidance, high-risk escalation, security logging, and structured responses.
- OWASP Top 10 for LLM Applications 2025 and MITRE ATLAS used as assessment frameworks.

## Architecture

`Client -> FastAPI -> authentication -> deterministic policy/authorization -> bounded mock data tools -> mock AI planner -> response validation -> redacted audit log`

The policy layer, not the AI component, makes access, action, and escalation decisions.

## Implementation phases

1. Foundation: configuration, fixtures, models, test harness, and documentation baseline.
2. Feature slice: fictional login and account authorization.
3. Feature slice: safe chat, account and transaction explanations, fraud guidance.
4. Controlled baseline: introduce narrowly scoped, test-only weaknesses to demonstrate authorized local findings. The baseline is fixture-driven, off by default, must never be exposed beyond localhost, and has no real data, credentials, financial actions, or cloud access.
5. Control slice: injection screening, output validation, PII redaction, tool permissions, escalation, audit logging, and rate limits.
6. Release: local runbook, health check, release evidence. The hardened configuration is the only normal release configuration.
7. Break and harden: run attacks against the controlled baseline, document observed behavior, enable or implement each fix, rerun identical attacks, and retain before-and-after evidence.
8. AWS extension design only. No AWS command or access without fresh approval immediately before it.

## Learner-owned decisions

- Bug fixed: ordinary `Hello` was misclassified as invalid model output. Empty no-document summary now becomes a fixed general response; document summaries still require text. Root cause, before/after evidence, 79 passing regression tests and real-browser smoke are in `docs/evidence/bug-hello-general-response.md`.
- Bug fixed: general banking questions with an empty model summary collapsed to the greeting. Planner prompt now requests a bounded educational answer for no-account questions, with greeting fallback retained. Pending-transaction live CLI and browser checks passed; evidence is `docs/evidence/bug-general-question-fallback.md`.
- Privacy control implemented: deterministic email, phone, card/long-number and tax-ID redaction before model summaries reach the browser or inspection panel. 85 tests passed and a real browser general-question smoke passed. Best-effort limits are documented in `docs/evidence/privacy-redaction.md`.

- Browser slice verified: 77 API/regression tests and 23 Playwright checks passed; screenshots inspected. See `docs/browser-qa.md` for each tested acceptance area, explicit fault injections, test-harness correction and remaining controls. Local page left running for the learner. UI has no weak-mode toggle or live review queue.

- Current browser slice: one local page with Alex/Blair login, authenticated real-model account chat, fixed ticket selection, safe guidance, truthful review-required responses, logout, clear conversation, and inspection panel. Acceptance: both identities get their own data, forced cross-account proposals denied, ticket lookups blocked, model failure shown safely, all controls usable on desktop/mobile, no HTML execution from messages. Execute API regression tests and real Playwright flow; browser faults injected only for error/rendering QA are labeled.

- Run fingerprints added: model digest from local Ollama inventory, Python/library versions and SHA256 of application Python files and ticket fixtures, captured before/after the suite. 55 tests passed; real run reported UNCHANGED AT CHECKPOINTS. This is observation, not model pinning or attestation.

- Evidence export implemented: attack suite saves new JSON and Markdown reports per run, verifies readback, preserves older records and reports save failure. 49 tests passed plus one saved real-model run. Reports are not signed or immutable; manual summary review remains necessary.

- Lesson 4 implemented and executed: six-case local real-model suite with distinct refusal, backend-block, disclosure and review outcomes. 46 automated tests passed. Live results and limitations are in `docs/lesson-04.md`; no successful live attack or malicious backend denial was observed in that run.

- Lesson 3 implemented: allowlisted clean and poisoned support tickets, real local summarization, independently tested backend containment. Live clean and poisoned runs both summarized safely; neither exercised authorization. `docs/lesson-03.md` records actual results and remaining limitations. Start the learner with the clean comparison before the poisoned ticket.

- Real local model connection completed for the terminal lab: Qwen3 4B via Ollama, strict validated account proposals, hardened authorization, no model-controlled identity. Safe live request succeeded; the live identity-override attempt was refused by the model, leaving backend authorization NOT EXERCISED on that attempt. See `docs/local-model.md` for commands, results, scope and remaining tests.

- Lesson 2 ready: `python -m app.security_demo --scenario mock-injection --mode baseline`; see `docs/lesson-02.md` for the mock-model scope, reproduction, hardening comparison, and executed evidence. Real-model prompt injection resistance remains NOT VERIFIED.

- Current lesson: run `python -m app.security_demo --mode baseline`, explain the missing ownership check, then compare hardened mode. The runner uses an in-process test client and clears demo state each run.
- Resolved configuration blocker: normal settings and startup reject baseline; isolated in-process exercises use a dependency override. Six focused tests and actual rejected Uvicorn startup verified the fix. This is not protection against a person modifying local Python code.
- Evidence correction: transaction owner success, complete risk-language handling, persisted human review, and strict outcome/category response validation need further implementation or verification. Existing broad acceptance ticks describe partial slices, not completed controls.

- TODO(human): Define whether an unauthorized request gets a generic refusal or a generic refusal plus a security-event reference.
- TODO(human): Define which high-risk request categories require human review beyond transfers and ownership changes.
- TODO(human): Review injection detection trade-offs between strict blocking and false positives.
- TODO(human): Approve the final tool-permission matrix before implementation.

## Testing and security plan

- Unit tests for every deterministic control.
- Integration tests through the HTTP API.
- Authorized, local-only adversarial tests for injection, IDOR, PII leakage, unsafe output, tool misuse, rate limiting, approval bypass, configuration, and log hygiene.
- A paired baseline-versus-hardened test for every intentionally demonstrated weakness. Baseline weaknesses may only be enabled by a local test configuration and are never part of the default release.
- Each control maps to a test, evidence artifact, OWASP applicability assessment, and MITRE ATLAS scenario in `docs/security-framework-mapping.md`.
- Tests report PASS, FAIL, or NOT VERIFIED with exact command and observed output.

## QA plan

- Verify every endpoint, page, form, error response, and security control individually.
- Run the complete local UI flow with Playwright once the browser UI exists.
- Inspect generated logs, configuration, fixtures, and repository state.
- Document root cause, fix, retest, and regression evidence for any failure.

## Acceptance criteria

- [x] Application starts locally and `/health` returns `{"status":"ok","security_mode":"hardened"}`.
- [x] Fictional authentication is implemented and tested, including password hashing and session expiry.
- [x] Object-level account and transaction authorization is implemented and tested, including before-and-after replay evidence.
- [x] Safe questions, transaction explanations, and fraud guidance are implemented and tested.
- [x] High-risk and cross-account requests are refused or escalated and tested.
- [x] Forbidden model-requested tool replay demonstrates baseline versus hardened allowlisting without executing real actions.
- [x] Implemented AI safety controls have automated tests; broader model consistency remains explicitly limited in the evidence.
- [x] OWASP LLM Top 10 and MITRE ATLAS applicability mappings have evidence-backed statuses, with residual items explicitly marked.
- [x] Baseline attacks and post-hardening regression attacks have documented output.
- [x] Fixtures, source, and generated audit logs contain no real data, secrets, or unnecessary PII; the optional JSONL path is covered by a security test.
- [x] README instructions match executed local commands; the documented test command was executed successfully.
- [ ] Course repository remains unchanged; AWS remains unaccessed without approval.

## Out of scope

- Real banking systems, credentials, users, data, payments, or financial decisions.
- Production AI provider integration in phase one.
- Docker and cloud deployment in phase one.
- Any claim of affiliation with American Express.
# Portfolio evidence update, 2026-09-17

- Capture matched cross-account API tests with baseline HTTP 200 and hardened HTTP 403.
- Label rendered test reports separately from customer-interface screenshots.
- Verify the authorized control remains HTTP 200 and each test reports PASS.
- Keep restored implementation and capture tooling in the separate local recovery folder.
- Publish only the README, plan update, and two evidence PNGs in this change.
- QA: both scenarios executed successfully; screenshot text compared with actual process output; no source files staged.
