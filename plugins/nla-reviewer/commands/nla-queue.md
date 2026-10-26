---
description: Show my Not Legal Advice review queue
argument-hint: [--all]
---

Use the `nla-reviewer` skill. Run `python3 ${CLAUDE_PLUGIN_ROOT}/scripts/nla_reviewer.py queue $ARGUMENTS` and present the queue to the human grouped as the script prints it (Assigned to you: Initial review, Blocked, Withdraw requested, Signing, FYI; then Team (read only), which the human can read but not act on).

If the script says reviewer actions are not set up or there is no review share, say so and offer `setup` (use `--dry-run` first). Keep the SIMULATED / NOT LEGAL ADVICE notice with what you show. Do not act on any Request from the queue; ask which one the human wants to open.
