# BankAssist interview preparation

## What is BankAssist?

An independent secure AI banking-support chatbot case study using fictional data, a local Qwen3 model, deterministic authorization, and authorized security testing.

## What problem does it solve?

It demonstrates how a support assistant can answer bounded questions while preventing model output from becoming an authorization or financial-action decision.

## What are the trust boundaries?

Browser to FastAPI, authenticated session to policy layer, model proposal to backend tools, and untrusted support-ticket content to the summarizer. See `docs/architecture.md`.

## How does authorization work?

The session identifies the actor. Deterministic backend code compares that actor with the resource owner. Account, card, and transaction replays show cross-account denial.

## How did you handle prompt injection?

Trusted system instructions and untrusted ticket text are separated. The model output is treated as a proposal. The backend performs ownership, task, and tool checks. Lesson 3 and Lesson 4 contain the actual local runs.

## Why can’t the model enforce security?

The model can be manipulated, inconsistent, or unavailable. Authorization, tool allowlisting, escalation, and output validation therefore run in application code.

## What happens for high-risk requests?

The application does not call the model. It creates a local review request. A reviewer-role user can approve or reject it, but no financial action exists to execute.

## What vulnerabilities were demonstrated?

Controlled baseline tests demonstrated cross-account account, card, transaction, prompt-proposal, and forbidden-tool failures. Hardened replays blocked them. Every baseline is isolated and fictional.

## How did you test it?

Run `./.venv/bin/python -m pytest -q`, `./.venv/bin/python scripts/qa_browser.py`, `./.venv/bin/python scripts/qa_reviewer.py`, and the individual baseline/hardened demo commands in `docs/evidence/`.

## How would you deploy to AWS?

Use the design in `docs/aws-extension-plan.md`: Cognito, API Gateway, Lambda or ECS, IAM least privilege, Secrets Manager, KMS, CloudWatch, WAF, and a protected datastore. No AWS deployment was performed.

## What limitations remain?

This is local fictional data, in-memory session and queue state, local JSONL audit persistence, a local rate limiter, limited model trials, and no production identity, monitoring, distributed storage, or CVE scan.
