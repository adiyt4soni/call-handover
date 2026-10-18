# Call handover

Turns a discovery-call transcript into CRM fields, a qualification check and an AE handover note, where every field quotes the exact words it came from.

**Type:** Claude Code skill · **Area:** Sales research

## What it does

- Reads the call transcript and fills twelve Salesforce-style fields (contact role, lead source, store count, problem in their words, current system, competitors, stakeholders, timing, next step and date, budget, turnover).
- Each field carries a word-for-word quote from one turn of the call, at least four words long; fields the call didn't cover are exactly `not stated`.
- Checks the lead against a three-part qualification bar (right role, real problem, second meeting agreed) with met / not met / unclear.
- Writes a five-line AE handover note and insight-log entries (objections, competitor mentions, product gaps), then runs a grounding checker and fixes every failure by quoting more exactly, never by inventing a quote.

## Guardrails

- Every number in the output must have been said on the call. No rounding, no estimating, no inferred budgets.
- The test answer keys are off-limits to the skill, so the score means something.
- An unqualified meeting is never marked qualified.

## When it triggers

> Turn a discovery-call transcript into Salesforce fields (each quoting its evidence), a check against the team's qualification bar (right role, real problem, second meeting), an AE handover note and insight-log entries; then run the grounding checker. Use when asked to process call notes, a transcript, or a handover.

## What's in this repo

- `SKILL.md`: the skill itself, the file Claude or Codex reads.
- Not included: The grounding checker (`check_handover.py`), the sample transcript and the test set are not published here.
- Written to be generic: where the skill relies on the user's own files, it names them by role, not by content.

## Install

```bash
mkdir -p ~/.claude/skills/call-handover
curl -fsSL https://raw.githubusercontent.com/adiyt4soni/call-handover/main/SKILL.md -o ~/.claude/skills/call-handover/SKILL.md
```
