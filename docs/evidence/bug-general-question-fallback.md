# Bug fix: general questions collapsed to greeting

Date: 2026-09-16

## Reproduction

Browser message: `What is a pending transaction?`

Actual pre-fix local result: the real Qwen adapter returned `action: summarize`, `account_id: ""`, `summary: ""`; the UI fell back to the same generic greeting used for `Hello`.

Root cause: the planner prompt only explicitly described account lookups and ticket summaries. General support questions had no required answer contract, and the empty summary was treated as a generic response.

## Fix and verification

The planner prompt now directs normal banking-support questions that need no account data to place a short fictional educational answer in the validated summary field. It also tells the model not to invent balances, fees, policies or actions. An empty no-document summary remains a safe fixed greeting fallback. Ticket summaries still require nonempty text.

Focused reproduction after fix: `.venv/bin/python -m app.live_demo --message "What is a pending transaction?"`

Actual output: `result: MODEL SUMMARIZED`, `backend_authorization: NOT EXERCISED`, with an answer explaining that a pending transaction has not finalized yet and may require support if unresolved.

Regression: `.venv/bin/python -m pytest` returned `79 passed in 0.22s`.

Real browser smoke after server restart: Playwright logged in as Alex, submitted the same message to `/assistant`, and observed HTTP 200, `outcome: answered`, and the model explanation rendered in the chat.

Security meaning: no account lookup was needed or authorized. The answer remains untrusted model text and is not a statement of real bank policy or financial advice. PII redaction, comprehensive factuality checks, prompt filtering, and production model controls remain NOT VERIFIED.
