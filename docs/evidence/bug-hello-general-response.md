# Bug fix: ordinary greeting returned a generic model error

Date: 2026-09-16

## Finding and root cause

An ordinary browser message such as `Hello` returned `503 Local model unavailable or returned an invalid response`. The local model was available. Direct inspection showed Qwen returned `{"action":"summarize","account_id":""}` without a summary. The strict document-summary validator correctly rejected an empty summary, but the browser path treated that valid no-account general response as a model failure.

Impact: ordinary support conversation could fail and teach the user that every message must be an account lookup. No account data was disclosed.

## Fix

An empty summary is accepted only when no document is supplied. Browser and CLI paths convert it to a fixed general greeting. Empty document summaries remain invalid. No model proposal can set actor identity, choose an unregistered action, or bypass account authorization.

## Verification

Before fix: actual `python -m app.live_demo --message "Hello"` returned `{"result":"ERROR","detail":"Local model unavailable or invalid input/output. No data returned."}`.

After fix: actual command returned `result: GENERAL RESPONSE`, `backend_authorization: NOT EXERCISED`, and `answer: Hello. I can help with fictional account, transaction, fraud-guidance, and general-support questions.`

Focused tests: `test_empty_summary_is_allowed_only_for_general_no_document_response` and `test_general_greeting_returns_safe_fallback_when_model_summary_is_empty` pass.

Regression: `.venv/bin/python -m pytest` returned `79 passed in 0.23s` after the fix.

Real browser smoke: Playwright login as Alex, fill `Hello`, send to `/assistant`. Actual response status 200, outcome `answered`, fixed general answer, control `Model returned a general response; backend authorization not exercised`.

Security meaning: no account lookup occurs for a greeting. This fixes usability and error classification; it is not evidence of prompt-injection resistance. The local server was restarted after the fix and remains available on 127.0.0.1:8008.
