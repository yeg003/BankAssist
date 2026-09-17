# Tool misuse before-and-after

Run the simulation:

```bash
./.venv/bin/python -c 'from app.tool_demo import run_tool_demo; import json; print(json.dumps(run_tool_demo("baseline"), indent=2)); print(json.dumps(run_tool_demo("hardened"), indent=2))'
```

Baseline reports a simulated forbidden `transfer_money` execution. Hardened mode blocks it using the explicit allowlist. `real_action_executed` is false in both results. This is a controlled lab demonstration, not a payment system.
