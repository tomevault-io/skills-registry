---
name: eod-review
description: End-of-day and rolling review of trade outcomes to improve rules and skills. Use when this capability is needed.
metadata:
  author: matiasvillaverde
---

# End Of Day Review

Run once after market close:

```bash
python3 ${OPENCLAW_HOME:-$HOME/.openclaw}/tools/risk/eod_review.py
```

Outputs:
- `trading/learning/eod-summary.json`
- `trading/learning/eod-summary.md`

Behavior:
- reviews today, last 5 days, and all-time tagged journal data
- generates rule/skill update suggestions
- one-run-per-day guard via `trading/learning/eod-state.json`

Grading doctrine: load `knowledge/wiki-highlights/JOURNAL.md` and apply
the 4-dimension A/B/C/D scorecard (setup quality, execution, risk
discipline, psychology) to every trade. PnL is not the grade.

Bias post-mortem: for every losing trade AND for every rule violation
(including A-grade winners that broke a rule), load
`knowledge/wiki-highlights/MUNGER.md` and identify which of the 12
starred tendencies were active. Be specific. Common firings on bad
trades:

- *Loser held too long* → #11 Pain-Avoiding Denial + #14 Deprival
  Superreaction (refusing to take the loss that was already real
  on the books)
- *Doubled down on a loser* → #14 Deprival Superreaction + #5
  Inconsistency-Avoidance (defending the original thesis instead of
  re-evaluating)
- *Chased a breakout late* → #15 Social-Proof + #18 Availability
  (the chart was the most-recent-vivid event in your context)
- *Sized up after a winning streak* → #12 Excessive Self-Regard +
  #13 Overoptimism
- *Followed a tip / guru / chat-room call* → #22 Authority-Misinfluence
  + #15 Social-Proof
- *Two or more of the above firing simultaneously* → **#25
  Lollapalooza**. This is the worst category — the loss was not
  one mistake, it was several reinforcing each other. Flag these
  separately in the eod summary because they decay slowest.

The bias category is the *root cause* layer of the post-mortem. The
A/B/C/D grade is the *symptom* layer. Both belong in the journal.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/matiasvillaverde) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
