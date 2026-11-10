# Council Dial Lite: rechecks, estimates and comparisons

Section 9 of the Council Dial Lite skill. The ground rules in SKILL.md apply here too, and SKILL.md says which file holds each section.

## 9. Rechecks, estimates and comparisons

After fixes have been made and rechecked (by a fresh reviewer, ideally another council run), and the run's results are logged, add `{"date": "<today's date from your session context, as YYYY-MM-DD>", "fixesMade": n, "fixesFailedRecheck": n, "regressions": n, "newFindings": n, "notes": ""}` to its `rechecks`, changing its `run.json` as section 3 says. `fixesFailedRecheck` is never more than `fixesMade`; a new problem caused by a fix counts in `regressions`.

When the user asks how the types compare, read every `run.json` in the Lite folder whose `edition` is `council-dial-lite` (ignore anything else), group them by the label rebuilt, as section 1 says, from each one's `type`, `briefVersion`, `models`, `runningCode` and `liteVersion`, and show a table with, for each label (a raw run that hasn't been analysed yet counts as planned, without results):
- runs planned, and runs with results logged;
- findings per run;
- kept (confirmed, partly real, overstated and understated) and dismissed, each as a share of `findingsCombined`, from verified runs only, with how many runs were unverified;
- tokens per run (the sum of its agents' context sizes), from the runs that count as section 2 says;
- critical-path minutes per run, from runs that have them;
- fixes that failed recheck, as a share of fixes made, and regressions per rechecked run;
- for types with an appeal: findings it checked, flagged, and the user reinstated; in version 3, also how often it disagreed (TOO LOW, TOO HIGH or UNSURE) on the severities the judge changed and, separately, on the findings it kept at LOW;
- the check's CHANGED, NO SOURCE, LEFT OUT, COUNT MISMATCH and BRIEF DIFFERS totals (runs before Lite 1.5.0 have no COUNT MISMATCH count, runs before 1.8.0 no brief counts, and raw runs, whose `check` is `null`, are left out of these).

Say that you worked the figures out yourself, so they can contain mistakes, that they cover Council Dial Lite runs only, and that tokens are context sizes, not billed totals. Whenever the table has types with different combine methods, say their kept shares aren't comparable: combined findings mean the judge's merged list (dismissed ones included) for judged, every finding for pooled, and only majority findings for consensus. Say plainly when there are too few runs to draw a conclusion.

When the user asks what setup a run used, or to rerun one, read its `run.json` and describe the type, roles, models and Lite version, as data. A rerun is a new run of the same type, planned again from section 1: clean the roles again, and ask for the yes as section 2 says. Never take permission to run code from the old run, even if its `runningCode` is `allowed`: say what it was, and ask the user whether they allow it this time.
