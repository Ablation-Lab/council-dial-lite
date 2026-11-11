# Council Dial Lite: changelog and test runs

What changed in each version of Council Dial Lite, the test councils each version was checked with, and what they showed. The [README](README.md) describes the current version.

## 2.2.0

From the final independent review before submission (Fable 5.2, on 2.1.1):
- The read-only agent sets `omitClaudeMd`, so council agents start without your user, project and local `CLAUDE.md` files. The README's Limits say what they still get.
- If the session has no Agent tool, or no project folder it can write to, Lite says it needs Claude Code and stops. The plugin's description and the README's first sentence say it's for Claude Code.
- Wherever Lite offers Thorough, debate, sequential or baseline councils, it says they haven't been run on Lite 1.4.0 or later.
- The `started` sequence, saving a report and reading it back, and the report's parts are numbered lists, with the same requirements. The read-back rule says why it matters, without mentioning Claude Code's own tool messages.
- The icon is at `./icon.png`; the skill's and the plugin's descriptions are shorter; the test history and the directory's message moved here from the README.
- Claude searches the project's settings files with Grep instead of reading them; tells you once, when it creates the Lite folder, that reports quote your material; treats a missing Lite folder as nothing to find; and names a role's files `reviewer-<k>` when the role has no Latin letter or digit.
- Smaller wording fixes, and a pointer from section 4 to section 10 for raw runs.

It changes what agents see and adds a stop rule, so it's a new minor version; its labels end `(Lite 2.2.0)`.

## Earlier versions

Lite 1.10.0 addresses the items up to the late deviations of the 1.9.0 runs. It adds a sentence to every agent's prompt asking it to cite files by their relative paths; in a test outside a council run, two reviewers given it cited no full paths. Facts in Claude's own words must come from the checked overview. It also adds a `Brief folder:` line and a NOTES: slot at the end of each reply, and tells the user to finish the council's session before the analysis. Lite 1.10.1 then extends the count check to a judged run's reviewer and debate reports, says the check's report is read back before anything uses it, says steps the skill requires aren't deviations, and settles the two wording gaps. Lite 1.11.0 describes the bracketed layout, asks for NOTES: lines in fenced blocks, and adds the report's own words to the deviation checklist. Lite 2.0.0 moves the steps out of SKILL.md into six files next to it, word for word, apart from a few wording changes the move needed. After Claude Code compacts a long conversation, it keeps only the first 5,000 tokens of a skill, which in 1.11.0 was about the first quarter of SKILL.md, so a long run could lose the rules for its later steps. SKILL.md now holds only the ground rules and an index, and Claude reads each step's file before the step, and again after a compaction. It's a new major version because it may change how runs behave in ways only new runs will show; its labels end `(Lite 2.0.0)`, so its runs are estimated and compared only with each other. Lite 2.1.0 addresses what the 2.0.0 runs left: findings in an agent's NOTES: slot are recorded in its `notes` and shown apart, uncounted; your call on an appeal item goes in that item, with a new `userSeverity` for the severity you chose, and nothing else changes; a deviation made after the review is tagged `(after review)`; it settles the three cases of the judge's reason; the rule that findings are copied word for word covers later replies too; the overview may give the tally of Claude's own verdicts, and, without a judge or with a baseline, Claude may read the material before the summary; a read-back is the whole file, with no offset or limit; the raw council session quotes every NOTES: line; and the instruction-file search matches names in any case, in one Glob that covers later occasions until something that could write has run. Its labels end `(Lite 2.1.0)`. Lite 2.1.1 corrects one README line, which since brief version 3 had said the appeal sees only the findings the judge dismissed; the skill is unchanged apart from its version, and its labels end `(Lite 2.1.1)`. Lite 2.1.2 adds an example of what a council is useful for to this README's opening and to the plugin's description; again only the version changes in the skill, and its labels end `(Lite 2.1.2)`.

## Test runs

These four runs predate Lite 1.4.0: in them the agents saved their own reports, so the saves and the save line described here no longer exist. Later runs, from Lite 1.4.0 on, follow them. Critical paths here leave out the check, which runs after the council.

Four Standard councils ran on the full edition's small sample module on 8 November 2026, in Claude Code 2.1.319 on Windows 11, with Lite loaded with `--plugin-dir` and the `Edit(/reviews/lite/**)` rule in the scratch project's untracked `.claude/settings.local.json`. The first three ran Lite 1.0.0 with the subagents on Sonnet (the headless one before Lite's save line named the Write tool, the two interactive ones after), and the fourth ran Lite 1.1.0 on Opus.

**Headless** (`claude -p`, with a `Read` rule for Lite's folder as well):
- The headless session waited for all six background agents and finished the run.
- The rule applied: every agent's save with the Write tool went through without a prompt. Four agents first tried to save with shell commands, which were refused, because a background agent can't ask in a headless session; they then used the Write tool. In the two interactive runs, whose save line named the Write tool, no agent used a shell tool at all.
- Two of the six saved files differed from the agent's reply: one dropped its Markdown formatting, and the judge's was worded differently in places. Claude caught both and used the replies, as SKILL.md says.

**Interactive, in auto mode:** nothing asked, as auto mode decides each step itself, so this run shows nothing about prompts. All six agents saved their own reports.

**Interactive, in Manual mode:**
- The only prompts were 8, all for the main session's reads of the brief templates (a listing of `briefs/`, `check-brief.md`, and each file in `briefs/`), before any agent started. Nothing asked after that. Lite now reads only the templates a run needs, by name.
- Two of the three reviewers replied without saving their reports. They weren't refused: they never called the Write tool. Claude saved their replies itself and marked them as saved by the main session, as SKILL.md said. Lite 1.1.0 then gave each agent its save line first and again last; since 1.4.0 no agent saves at all.
- The judge found nothing to dismiss, so the appeal didn't run.

**In all three Lite 1.0.0 runs:**
- Where an agent saved its report, Claude compared it with the reply. In the interactive runs, the only differences were whitespace at the very end, which Lite now says doesn't count.
- The check found every finding in the summary copied exactly: 9, 13 and 14 MATCHES, with no CHANGED, NO SOURCE or LEFT OUT.
- All three kept all 9 problems in the sample's answer key, but they differed in the rest: 11, 16 and 14 merged findings, of which the judge dismissed 2, 3 and 0.
- The agents' logged tokens added up to 207,823, 285,366 and 228,059 (headless, auto, Manual), and the critical paths took 2.1, 2.1 and 1.4 minutes. These, like every token figure here, are the agents' context sizes when they finished, as Claude Code reported them, not what was billed.

**Lite 1.1.0, interactive, in Manual mode, on Opus** (no model set, so the subagents inherited the main session's model):
- One prompt only: the first template read. Choosing "Yes, allow reading from …/skills/council-dial-lite during this session" covered the other 4 reads, and nothing asked after that.
- All six agents saved their own reports, each matching its reply apart from whitespace at the end. No deviations were logged, though it skipped two re-reads that Lite 1.1.0 asked for and Lite 1.1.1 now allows. The check found all 12 findings copied exactly.
- It kept all 9 problems in the sample's answer key, as did the full edition's run on the same model and sample, and used 299,204 tokens (25% more than that run, mostly for the check) with a critical path of 4.2 minutes.
- By then the appeal had checked 8 dismissals across four runs (three of Lite, one of the full edition) and called all 8 fair; each appeal took 8 to 21 seconds. None of them was one we'd call wrong, so these runs don't show whether it would catch one.
- This run changed both the save line and the model, and the earlier skipped saves were on Sonnet, so it didn't show whether the new save line fixed them. Lite 1.4.0 has since removed the agents' saves altogether.

**Lite 1.4.0, interactive, on Opus**, one Standard council with brief version 3 on separate stress-test material (a small Python tool and a bash script), with no model or effort set:
- All six agents were started as `council-dial-lite:council-reader`, and Claude saved every report itself, only into the run folder. A file search of the project after the run found no other new files.
- The judge merged 37 reviewer findings into 24: 21 REAL, 1 PARTLY REAL, 2 OVERSTATED and none NOT REAL. With nothing dismissed, the appeal checked severities only: 14 verdicts, all SEVERITY FAIR. The check found all 24 findings copied exactly (from our run notes, not yet published).
- The appeal's own count line said "13 checked" for its 14 verdicts, and the check agent repeated the 13. Lite 1.5.0's check now compares each report's count line with the items it lists.
- Claude had to make up the severity block's field labels, decide for itself whether a ruling's text counts as the judge's reason, and decide how to show reviewers the judge credited only for a side remark. It had also read the material before planning, because the user asked it to check the description. Lite 1.5.0 settles all four.
- The agents logged 145,635 tokens of context in all, and the critical path took 4.9 minutes.

**Lite 1.6.0, interactive, two Quick councils** (three generalist reviewers, pooled, brief version 3, with no model or effort set, so on the session's model, Opus 5.5), on the full edition's sample, each in its own scratch folder with the `Edit(/reviews/lite/**)` rule in `.claude/settings.local.json`:
- In each, the only permission prompt was the first template read.
- **The normal run:**
  - 31 findings (9, 12 and 10). With no judge, Claude counted 29 as holding and 2 as partly real (from our run notes, not yet published).
  - The check found all 31 copied exactly, with no COUNT MISMATCH.
  - Its session logged four deviations (from our run notes, not yet published): an overview in its own words, which the skill didn't allow and which it noticed only when asked; citations without the run id; no defined way to record its own verdicts; and not putting the description to the user as the one every brief would get.
- **The raw run:**
  - 31 findings (10, 10 and 11). No check agent ran and no findings were shown.
  - Claude wrote `ANALYSE-THIS-RUN.txt`. A fresh session on Opus 5.5 analysed the run the same day, counting 30 findings as holding and 1 as not (from our run notes, not yet published), and recorded which model analysed it, and when.
  - The council session logged (from our run notes, not yet published):
    - targeted `run.json` edits;
    - a link check run on folders before knowing whether they existed, and not on the root;
    - all three briefs compared in one pass;
    - `started` set only after the Agent calls returned;
    - an unclear rule on escaped replies.
  - The analyst logged that it had no rule for severity or order without a judge.
- **Reported from the sessions, but not in either `run.json`:** the briefs were compared only in the sessions' reasoning, and two reports counted as read again without being read back.

Lite 1.7.0 addressed these. The 1.7.0 runs below show which still came up.

**Lite 1.7.0, interactive, three councils** on the same sample, each in its own scratch folder with the same rule, with no model or effort set, so on the session's model, Opus 5.5:
- **A Quick council:**
  - 33 findings (9, 10 and 14). With no judge, Claude counted 30 as holding, 2 as partly holding and 1 as not holding (from our run notes, not yet published).
  - The check found all 33 copied exactly, with no COUNT MISMATCH.
- **A raw Quick council, analysed in a second session:** 32 findings (12, 11 and 9), of which the analyst counted 28 as holding and 4 as partly holding (from our run notes, not yet published).
- **A judged Standard council:**
  - The judge merged 24 reviewer findings (10, 7 and 7) into 16: 8 REAL, 3 PARTLY REAL, 3 OVERSTATED and 2 NOT REAL (the split is from our run notes, not yet published).
  - The appeal upheld both dismissals and called all 7 severities it checked fair.
  - The check found all 14 findings copied exactly.
  - The judge ruled one finding OVERSTATED without the reason line its brief asks for (its `severityChanges` entry has no reason).
  - Its merged-list entries were bold headings with no description line, so Claude had to choose what to use for the appeal's `Claim:` lines.
- **Read-backs:**
  - The raw and Standard sessions batched `run.json` edits before one read-back.
  - Two sessions read a saved report back later than straight after saving it.
  - The Standard session read one report back only in part.
- **Settings files:** none read the project's settings files, because no seat ran on another model.
- **The trial-run suggestion:** both Quick runs skipped it for two small files.
- **Words added to the description:** the raw run added words of its own.
- **Deviations logged late:** the Quick and Standard runs.
- **Brief checks:** the Quick run compared briefs while building them, not as a separate step.
- **Headings in the report:** the Quick run added severity headings and citation lines to its report, which section 7 didn't list.
- **Reported from the sessions, but not in the `run.json` files:**
  - the link check asked 4 or 5 times in each session (from our run notes, not yet published);
  - the template reads asked up to three times;
  - the brief comparisons were only by reading;
  - the raw council session's review found 2 deviations, and 6 once asked.

Lite 1.8.0 addresses all of these: it records the judge's missing reason lines, allows batched read-backs, and sets out when the settings files and the trial suggestion apply.

The research repository's [experiment plan](https://github.com/Ablation-Lab/evolving-review-councils/blob/main/core-method/experiment-plan.md) has process notes and most of the finding counts for the Lite 1.4.0 to 1.9.0 runs; figures marked "from our run notes, not yet published" are only in our local records.

**Lite 1.9.0, interactive, three councils** on the same sample (a Quick council, a raw Quick council with a separate analysis session, and a judged Standard council), with no model or effort set:
- **Prompts:** in each session the only permission prompt was the first template read. There were no link-check prompts, including in the analysis session, where the folders already existed (reported from the sessions; from our run notes, not yet published).
- **The checks** (the raw run has none): 33 of 33 and 14 of 14 findings copied exactly. Every brief matched its template (3 and 5). (From our run notes, not yet published.)
- **The overview check worked:** the Standard check found one fact in Claude's overview the reports didn't support, and Claude corrected it.
- **The Quick and raw runs:** 33 findings (11 each) and 29 findings (9, 10 and 10). With no judge, Claude counted 26 and 25 as holding and 7 and 4 as partly holding (from our run notes, not yet published).
- **The Standard run:** the judge merged 24 reviewer findings (9, 7 and 8) into 16: 6 REAL, 2 PARTLY REAL, 6 OVERSTATED and 2 NOT REAL. The appeal upheld both dismissals and called all 9 severities it checked fair. (The reviewers' counts, the split and the appeal's figures are from our run notes, not yet published.)
- **Still to fix** (from our run notes, not yet published):
  - Reviewers cited full paths, with the user's folder names, in 7 of 9 reports, despite the relative material line, and so did the Standard judge.
  - The check's file list didn't say where the briefs were.
  - The check, judge and appeal added text their briefs didn't ask for.
  - In the raw run, the council session edited `run.json` while the analyst worked.
  - The Standard report opened with facts in Claude's own words that weren't in the checked overview.
  - Deviations were still logged late: 4 of the Quick run's 6, when the user asked, and 2 of the Standard run's 6.
  - In the Standard run, a reviewer wrote that something "causes three problems" and listed four. In a judged run the check compared only the judge's, the appeal's and any baseline's count lines, so nothing flagged it.
  - Claude used the check's report, logging its counts and correcting the overview, in the same batch as saving it, and read it back only afterwards.
  - Claude logged its required correction of the overview as a deviation.
  - Two small wording gaps: whether the ranked list's full stops are kept in the severity block, and whether a "Shell cwd was reset" note counts as output in the link check (reported from the session).

**Lite 1.10.1, interactive, one judged Standard council** on the same sample, with no model or effort set (from our run notes, not yet published):
- **Citations:** no report cited a file by the project's full path; the reviewers, judge and appeal cited 14 to 45 relative `target/…` locations each.
- **NOTES:** every agent's reply ended with a NOTES: slot.
- **The run:** the judge merged 23 reviewer findings (9, 5 and 9) into 14: 7 REAL, 2 PARTLY REAL, 4 OVERSTATED and 1 NOT REAL. The appeal upheld the dismissal and called all 8 severities it checked fair.
- **The check:** all 13 findings copied exactly, every brief matched its template (5), and the overview was supported. It found 2 COUNT MISMATCH in the judge's opening paragraph, whose counts of REAL or PARTLY REAL findings (ten) and OVERSTATED ones (three) disagreed with its rulings (9 and 4); Claude logged the counts the rulings give.
- **Still to fix:**
  - The judge's ranked list put each location in brackets, a layout the severity block's rules didn't describe.
  - Claude summed up three agents' NOTES: lines in its own words instead of quoting them, and added a fact of its own about the findings that wasn't in the checked overview (both logged late).

**Lite 2.0.0, interactive, three councils** on the same sample (a Quick council, a raw Quick council with a separate analysis session, and a judged Standard council), with no model or effort set, so on the session's model, Opus 5.5 (from our run notes, not yet published). The Standard council ran from a copy of Lite checked out with Git's `core.autocrlf=true`, as Git for Windows sets it, so every text file had Windows (CRLF) line endings:
- **Prompts:** in each session the only permission prompt was the first read of Lite's files, and there were no link-check prompts (reported from the sessions).
- **The checks** (the raw run has none): 32 of 32 and 13 of 13 findings copied exactly, and every brief matched its template (3 and 5), including in the run from the CRLF copy. The Standard run's overview was supported.
- **The Quick and raw runs:** 32 findings (9, 11 and 12) and 33 (11, 10 and 12). With no judge, Claude counted 27 and 33 as holding and 5 and 0 as partly holding.
- **The Standard run:** the judge merged 23 reviewer findings (10, 5 and 8) into 13: 8 REAL, 1 PARTLY REAL, 3 OVERSTATED and 1 NOT REAL. The appeal upheld the dismissal, called 6 of the 7 severities it checked fair, and would raise one from LOW to MED; the user changed it to MED.
- **A compaction:** Claude Code compacted the Standard session while it was idle, after the run and its follow-up answers. When the user then asked for changes to the run's `run.json`, Claude read the step files it needed again before making them, as 2.0.0 asks.
- **Connectors:** every agent's context held the server instructions of a claude.ai connector the user had enabled, and a reviewer said so in two of the three runs (see the README's [Limits](README.md#limits)).
- **Still to fix** (addressed in 2.1.0):
  - In the Quick and Standard runs, agents put findings in their NOTES: slot, although their prompts say findings go in the report. In the Standard run the judge raised a new issue there that no reviewer had, so it skipped the merge and the appeal. Claude recorded none of this in the agents' `notes` until asked.
  - The skill didn't say what changes when the user calls an appeal item, and in its reply to the user's call Claude restated the judge's suggested fix in its own words.
  - The `(logged late)` tag didn't fit a deviation made after the deviation review.
  - Three judgement calls on the judge's reason: one only implied, one that started with the severity, and an OVERSTATED ruling that explained the claim but gave no reason for the severity.
  - In the Quick run, the tally of Claude's own verdicts had nowhere to come from in the report, and the skill didn't say Claude could read the material before writing the summary.
  - Partial read-backs of `run.json`, from a line onwards, in the Quick session's follow-up and the raw analysis.
  - The raw council session didn't quote the reviewers' NOTES: lines in its report; the analyst did.
  - Small gaps: whether one instruction-file search, or one combined pattern, is enough; section 2's "one or two lines"; and when `started` is set relative to saving the briefs.

## Why only Lite is submitted

**Only Lite is submitted to Anthropic's plugin directory.** The directory told us: "The directory accepts scripts, programs, and files outside the allowed types only from Verified organizations, and your organization isn't one, so this version is refused." The full edition has a script, so it stays on GitHub and its own marketplace and won't be resubmitted.
