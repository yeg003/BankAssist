# Browser workflow QA

Date: 2026-09-16. Application: http://127.0.0.1:8008. This is the first local browser slice, not completion of the portfolio's full security scope.

## Story and implementation

Fictional login -> bearer-authenticated `/assistant` -> local Qwen proposal -> deterministic task/ownership checks -> rendered answer and control explanation. Alex and Blair use their own session identity. Fixed fraud guidance and financial-action refusal do not call Qwen. A review-required response does not send anything to a human.

Static files are served by FastAPI from `app/static`. No external UI CDN, Docker, AWS or hosted Site is used. Local Sites design guidance was used while retaining FastAPI. The agent-browser executable was unavailable, so the requested real-browser checks used Python Playwright with Chromium directly.

## Commands and evidence

Application startup: `.venv/bin/python -m uvicorn app.main:app --host 127.0.0.1 --port 8008`

Observed: application startup complete, listening at 127.0.0.1:8008. Root HTTP smoke request returned 200.

API/regression command: `.venv/bin/python -m pytest`

Actual final output: `77 passed in 0.22s`. PASS: existing regression cases plus static files/headers, both identities' owner and cross-account proposals, unknown accounts, unauthenticated rejection before model call, logout revocation, invalid/extra input rejection without reflecting values, generic provider errors, ticket task restriction, and guidance without model invocation. Forced model proposals in these tests are test doubles, not real-model compromise.

Browser command: `.venv/bin/python scripts/qa_browser.py`

Actual final output: `23 browser checks passed`. Exact per-check results and ticket/model observations are saved in [results.json](evidence/browser/results.json).

| Acceptance area | Result | Executed evidence |
|---|---|---|
| Root and static assets | PASS | Heading visible; screenshot login.png |
| Empty/invalid login | PASS | Required-field browser validity; actual 401 generic error |
| Alex and Blair login | PASS | Visible identity changes; each real-model owner lookup returned its own balance |
| Account request and inspection panel | PASS | Alex $1,250.75; model proposal and backend decision visible |
| Identity-override attempt | PASS for this attempt | Actual refusal; responding layer recorded in results.json, not assumed to be backend |
| Clean and poisoned ticket selector | PASS for observed examples | Actual local-model summaries; raw observations retained for human review |
| Fraud shortcut | PASS | Fixed guidance rendered, proposal null |
| High-risk request | PASS for tested transfer wording | review_required and explicit statement that no human request was sent |
| Input bounds | PASS | Whitespace rejected; browser max length 1000; separate API schema checks |
| Clear conversation | PASS | Bubbles removed; identity preserved |
| Pending response | PASS, injected response delay | Controls disabled while waiting, restored after response |
| Provider failure | PASS, injected browser 503 | Error visible; controls restored; separate mocked API provider failure test |
| Unsafe HTML output | PASS, injected browser response | Text visible, no image element or JavaScript execution |
| Logout and revocation | PASS | Browser signs out; API regression rejects revoked token |
| Expired-session response | PASS, injected browser 401 | Clears identity and conversation; actual timed expiration is not implemented |
| Keyboard login | PASS | Enter in password field submits form |
| Mobile layout and sending | PASS | 390px no horizontal overflow; actual fraud API request completed |
| Enlarged base text | PASS, narrow check | 32px root font at desktop width produces no horizontal overflow; not a full accessibility audit |
| Refresh | PASS | Browser token not persisted; signed-out screen returns |
| Runtime errors | PASS | No JavaScript pageerror events in completed run; intentional HTTP error responses are expected |
| General greeting | PASS after fix | Qwen empty no-document summary now produces fixed safe greeting; focused API and real-browser smoke passed |
| General banking question | PASS after fix | Pending-transaction question now produces a nonempty model answer; backend authorization remains not exercised |
| Transaction shortcut | PASS | Deterministic owner-bound transaction explanation; model is not called |

Desktop and mobile screenshots were opened and visually inspected: readable controls, no overlapping text or horizontal clipping. The conversation area intentionally scrolls. Screenshots: [login](evidence/browser/login.png), [conversation](evidence/browser/conversation.png), [mobile](evidence/browser/mobile.png).

## QA correction

The expanded QA run stopped progressing after the injected 401. Root cause was the test helper waiting for a role-based Send button that the correctly signed-out UI had hidden. That test process was stopped; the helper now checks Signed out for 401 and checks re-enabled Send for other responses. The complete browser suite was rerun and all 23 checks passed. No application fix was needed for this test-harness issue.

## Residual work / NOT VERIFIED

- Production password handling, session expiry, rate limiting and persistent audit records remain unfinished.
- Human review queue, cards/payment features, and browser transaction lookup are not implemented.
- The UI uses independent messages, not conversation memory. Chat-suite evidence export is still a separate CLI workflow.
- Real model outage/timeout was not induced; browser/API fault injections are explicitly labeled above.
- This does not prove arbitrary injection resistance, comprehensive PII redaction, full accessibility compliance or all-browser compatibility.
- Ordinary greetings must not be forced into an account lookup; the empty no-document summary fallback is fixed and covered. More general conversational intents remain under development.
- No public/cloud deployment tested. Local server is left running for the learner and can be stopped when the session is finished.

## Learner checkpoint

Open the local page. Use Alex, sign in, and send My balance. Expand Inspect the last response. Explain which value came from the model and which check happened before the balance was returned. Proceed to the attack shortcut only after understanding that distinction.
