# Architecture

## Current slice: authentication and account authorization

```text
Local client
  -> POST /auth/login
  -> opaque local session token
  -> GET /accounts/{account_id}
  -> deterministic `may_view_account()` policy check
  -> fictional fixture account or generic denial
  -> privacy-minimized audit event
```

`app/security.py:may_view_account` owns the access decision. No AI component is present in this request path, by design. An eventual chat planner can request a read operation, but it cannot select another user's data or override this function.

## Trust boundaries

1. Client input is untrusted.
2. Session token establishes an authenticated fictional user.
3. Account identifiers are untrusted object references until deterministic ownership validation passes.
4. Fixture data is only released after authorization.
5. Audit events are security-relevant but deliberately omit passwords, tokens, prompts, balances, and account identifiers.

## Controlled baseline

Normal startup rejects `BANKASSIST_SECURITY_MODE=baseline`. The local `app.security_demo` exercise installs a temporary in-process dependency override to demonstrate the weak ownership check, then restores it. The normal server defaults to hardened mode. This prevents accidental weak environment configuration; it does not restrict a developer who can edit code or install their own override.

## Bounded chat flow

### Browser flow added

The static same-origin UI at `/` sends demo credentials to `/auth/login` and keeps the returned token in page memory only. It submits `/assistant` requests with that bearer token. The server resolves identity via `require_user`, and passes the actual actor ID to Qwen's planning prompt. This fixes the CLI-only assumption that every caller is Alex.

`browser_api.answer` handles fixed high-risk/fraud guidance, otherwise asks Qwen for a strict proposal. For normal account lookup it enforces ownership against the authenticated actor. For ticket summarization it rejects any account lookup, including an owner's lookup, because that task needs no account data. Model summaries remain untrusted text with a visible review caveat. The frontend inserts text using textContent, never innerHTML. CSP restricts scripts/styles/connections to the same origin; the backend alone calls local Ollama. Logout revokes the bearer token. The UI cannot request baseline mode or override actor identity.

No financial action tools exist. High-risk keyword routing is limited and does not prove general intent detection; absence of write tools is the separate restriction. A local in-memory review queue records pending requests and allows a different fictional user to approve or reject them, but it does not execute actions or deliver to a staffed team.

### Earlier deterministic endpoint

`POST /chat` accepts a bounded message and optional account or transaction reference. `app/chat.py:respond_to_chat` applies this fixed sequence:

1. Escalate money-transfer and ownership-change language. No action tool exists.
2. Return fixed fraud-reporting guidance without requesting passwords or full card data.
3. Authorize the owning account before exposing a transaction explanation.
4. Authorize the selected account before exposing its balance.
5. Return only a schema-validated `ChatResponse` with a fixed outcome, category, answer, and optional escalation reason.
