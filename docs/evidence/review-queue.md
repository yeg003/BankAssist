# Local human-review queue evidence

High-risk requests create a pending local review record. A different authenticated fictional user may approve or reject the record; the requester cannot approve their own request. Decisions only change queue state and never execute a financial action.

Verification:

```bash
./.venv/bin/python -m pytest -q tests/security/test_review_queue.py
```

Visual walkthrough: open `http://127.0.0.1:8008/reviewer`, sign in as fictional Blair, and refresh the queue after submitting a high-risk request in the main chat. Approve or reject the pending record. No financial action is performed.

End-to-end browser verification:

```bash
./.venv/bin/python scripts/qa_reviewer.py
```
