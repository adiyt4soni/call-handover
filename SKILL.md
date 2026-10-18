---
name: call-handover
description: Turn a discovery-call transcript into Salesforce fields (each quoting its evidence), a check against the team's qualification bar (right role, real problem, second meeting), an AE handover note and insight-log entries; then run the grounding checker. Use when asked to process call notes, a transcript, or a handover.
---

# Call to handover

Paths are relative to the project folder.

**Do not open `demos/call_handover/answer_key.json` or any `test_set/key_*.json`.**
They are the tests. Reading one would make the score meaningless.

## Steps

1. Read the transcript (default `demos/call_handover/transcript.md`). Only the turns below the `---` line are the
   call; the header above it is not.
2. Make a new folder for this run, `demos/call_handover/out/live-<HHMM>/`, where HHMM is the current local time
   (`date +%H%M`). Write `handover.json` there. **Never write into `out/prerun/`**: it holds the pre-run backup.
   Use exactly this shape:
   ```json
   {
     "account": "...",
     "fields": {
       "contact_role":           {"value": "...", "evidence": "exact words from one turn"},
       "lead_source":            {"value": "...", "evidence": "..."},
       "store_count":            {"value": "...", "evidence": "..."},
       "problem_in_their_words": {"value": "...", "evidence": ["...", "..."]},
       "current_system":         {"value": "...", "evidence": "..."},
       "competitors_mentioned":  {"value": "...", "evidence": "..."},
       "other_stakeholders":     {"value": "...", "evidence": "..."},
       "timing":                 {"value": "...", "evidence": "..."},
       "next_step":              {"value": "...", "evidence": "..."},
       "next_step_date":         {"value": "...", "evidence": "..."},
       "budget":                 {"value": "...", "evidence": "..."},
       "turnover_rate":          {"value": "...", "evidence": "..."}
     },
     "qualification": {
       "right_role":     {"status": "met|not_met|unclear", "reason": "...", "evidence": "..."},
       "real_problem":   {"status": "met|not_met|unclear", "reason": "...", "evidence": "..."},
       "second_meeting": {"status": "met|not_met|unclear", "reason": "...", "evidence": "..."}
     },
     "handover_note": "five short lines for the AE",
     "insight_log": [{"type": "objection|competitor|product_gap", "text": "..."}]
   }
   ```
3. Run: `python3 -I -B demos/call_handover/check_handover.py demos/call_handover/transcript.md demos/call_handover/out/live-<HHMM>/handover.json`
   (When scoring against a test set, the answer key is added as a third argument after you finish.) Fix every grounding
   FAIL by quoting the transcript exactly, quoting a longer stretch of the same turn, or changing the value to
   "not stated". Never invent a quote.
4. Open `demos/call_handover/out/live-<HHMM>/handover.html`.

## Rules

- **Every field and qualification item above is present**, plus `account`, `handover_note` and `insight_log`.
- **Evidence is copied word for word from ONE turn** (no timestamps, speaker labels or header text, and never
  stitched across two turns). Each quote is **at least 4 words**; if a turn is shorter (for example "Yes."),
  quote a longer line that says the same thing. Use a list when a field needs two quotes; never put an empty or null item
  in a list.
- **Whose words:** quote the **prospect** for every field except `lead_source`, `next_step` and `next_step_date`,
  where the SDR's words also count. A qualification status of `met` or `not_met` needs a quote from the prospect.
- **If the call didn't cover it, the value is exactly `not stated`**, or `not stated (short reason)`, with
  `"evidence": null`. The reason must contain **no numbers, in digits or words** (write "no amount was given", not a
  figure). Nothing may follow the closing bracket. Don't infer budgets, dates or rates.
- **Numbers:** every number anywhere in the output (field values, reasons, handover note, insight log), in digits
  or words, must have been said on the call. Numbers are compared by value: "fifty thousand" may be written
  "£50k", but "140" may not become "£140k". Don't round or estimate.
- **Field definitions:**
  - `budget` = "allocated: yes/no/not stated; amount: <figure> or not stated". If both parts are not stated,
    write just `not stated`.
  - `next_step_date` = a proposed meeting time. Mark it "tentative: ... not confirmed" unless the prospect accepted
    it on the call. If no meeting was proposed, it is `not stated`.
  - `timing` = the prospect's own buying window (when they could buy or go live), not the next meeting.
- **Qualification bar** (from the SDR role's own definition): *right role* = the person can own or sponsor the decision;
  *real problem* = a current pain, in the area the product solves, that the prospect says they still have; already solved by a signed
  competitor = `not_met`; *second meeting* = a next meeting actually agreed. Status is exactly `met`,
  `not_met` or `unclear`; use `unclear` when the call leaves it open. Be honest: an unqualified meeting marked
  qualified costs AE trust.
- **Handover note:** who, what hurts, what they asked, what was promised, open questions.
- **Insight log:** objections, competitor mentions and product gaps, for AEs, Marketing and Product.
- **What the checker does not do:** it does not check that a quote supports the field it sits under. The SDR does
  that before anything is written to Salesforce.
