# Council Dial Lite

## What it is

Council Dial Lite is the lighter, script-free edition of [Council Dial](https://github.com/Ablation-Lab/council-dial). It has a small panel of Claude reviewers check your code, docs or plan. In its standard setup, three reviewers each check the work on their own, a judge checks every finding they report and throws out the ones that don't hold up, and an appeal agent rechecks anything the judge threw out. You make the final call on what to keep.

The agents only read: Claude saves each agent's report itself. Claude copies the findings word for word into a summary, and a fresh check agent compares that summary with the reports and flags anything missing or changed. Every run's setup and results go into a log in your project, so over time you can compare setups on your own material.

It's a research tool. It doesn't claim that any setup, including the judge and the appeal, works better than another.

## Quick start

[Install Lite](#install) first. Then start Claude Code in your project's root folder, in its default (Manual) mode, and:

1. **Set up the save rule.** Paste this prompt:

   ```
   Add the permission rule Edit(/reviews/lite/**) to this project's .claude/settings.local.json, keeping anything already there.
   ```

   Claude Code asks you before Claude writes the file: it never approves changes in `.claude/` by itself in Manual or accept-edits mode. Lite itself never changes your settings. The rule's path is read from the folder you started Claude Code in, so it matches only if you started it in the project root. Claude Code picks the rule up straight away, and from then on every report a run saves in `reviews/lite/`, the default Lite folder, goes through without asking.

2. **Ask for a council.** For example:

   ```
   Run a standard Council Dial Lite review council on src/app.js and README.md. The user did not allow running code.
   ```

   Use your own files in place of these. Claude shows the plan (the type, how many agents it will start, and a token estimate once you've logged runs of that type) and asks before starting any agents. Say yes to start.

3. **Allow the template reads.** Claude then reads Lite's brief templates from the plugin's own folder. On the first read prompt, choose **Yes, allow reading from <Lite's skill folder> during this session**, which covers all the template reads in one go, or choose **Yes** to approve each read one at a time. We've seen this with a copy loaded with `--plugin-dir`; an installed copy's prompt shows its own folder.

Claude then gives you the findings, most serious first, and the check's counts. If the appeal thinks the judge wrongly threw something out, Claude shows you both views and you decide. After a few runs, ask `How do the council types compare in my Lite log?` The rest of this README explains the [permission prompts](#permission-prompts) in more detail.

## Two editions

| | Council Dial | Council Dial Lite |
|---|---|---|
| For | research runs | everyday councils, and the plugin directory |
| Needs | Claude Code, Node.js 18 or later, subagents | subagents and file tools (and a shell tool, if there is one, to check for linked folders); no Node.js |
| Briefs | the same templates, filled in by a script that refuses changed templates | the same templates, filled in by Claude |
| Reports | saved by Claude through the script | saved by Claude, from each agent's reply; the agents can't write |
| Log | `reviews/review-log.jsonl`, checked by the script | one `run.json` per run in `reviews/lite/`, written by Claude |
| Comparison table | worked out by the script | worked out by Claude |
| Final check of the summary | no | yes, by a fresh agent |
| Installed from | see [its README](https://github.com/Ablation-Lab/council-dial) | this repository's marketplace, and the plugin directory once it's listed there |

The trade-off: Lite has no script, so nothing checks its log mechanically. The full edition's script refuses counts that don't add up, works out the comparison table and the critical path itself, refuses log folders that are links, removes invisible characters from saved reports, and records the session's effort level. In Lite, Claude follows the same rules by hand, so its figures can contain mistakes, and it can't read the effort level. Use the full edition for runs you'll report as research; use Lite where you can't run a script.

Lite runs are kept apart from research runs: they go in their own folder, each `run.json` says `"edition": "council-dial-lite"`, labels start with `lite:`, and run ids start with `lite-`, which the full edition's script never reads as one of its runs.

## Install

From the plugin directory, once it's listed there. Or from this repository's marketplace, in Claude Code 2.1.275 or later:

```
/plugin install council-dial-lite --marketplace Ablation-Lab/council-dial-lite
```

The first time, Claude Code shows the marketplace it found and asks you to confirm adding it; then it opens the plugin's page in `/plugin`, where you choose a scope and install it. From a shell, `claude plugin install council-dial-lite --marketplace Ablation-Lab/council-dial-lite` does the same in Claude Code 2.1.292 or later. On older versions, add the marketplace first with `/plugin marketplace add Ablation-Lab/council-dial-lite`, then `/plugin install council-dial-lite@council-dial-lite`. If you install both editions, say which one you want: when Claude picks Lite and you haven't said, Lite asks, but the full edition doesn't.

## How it works

1. **Choose.** Specialised or generalist reviewers; judged (with an appeal), pooled or consensus combining; optional debate, sequential and baseline runs; and, if you like, a model and an effort level for every agent or for one seat, such as Sonnet reviewers, a Haiku judge and an Opus appeal at max effort. Claude passes them only as the Agent tool's parameters, never in a prompt, so the judge and the appeal can't tell which model or effort produced a finding, and the type's name records each seat's. Lite can't check `CLAUDE_CODE_EFFORT_LEVEL`, which overrides any effort you set, so Claude reminds you to. Presets: Standard (6 agents), Quick (4) and Thorough (10), each counting the final check.
2. **Brief version 3 by default.** The judge sets each severity itself and can raise one as well as cut it, with one of five rulings (REAL, PARTLY REAL, OVERSTATED, UNDERSTATED, NOT REAL), and the appeal checks every severity the judge changed and every finding it left at LOW, as well as its dismissals. Ask for brief version 2 to compare with runs from before Lite 1.3.0.
3. **Nothing runs unless you allow it.** Each agent is the plugin's read-only agent, `council-dial-lite:council-reader`, which has only the Read, Grep and Glob tools, the same as the full edition's, so it can't run a command, a script or a test, or write or change a file, whatever the material says. A rule in a brief is only an instruction, and in one of our full-edition runs a judge ran a "scratch" check it read as allowed; a tool limit can't be talked around. If you want the agents to run code, say so, and Claude starts them as general-purpose agents instead.
4. **Plan.** Claude builds each agent's brief from the templates in the skill's `briefs/` folder, word for word, saves the briefs, and starts the run's `run.json`. Anything you want left out, such as a folder of sample data, goes in the one-line description of what's reviewed, because that's what every brief gets. Claude adds nothing else to an agent's prompt, and logs any departure with its exact text.
5. **Run.** Claude starts the agents as subagents. Each one replies with its report and is told to open nothing in the Lite folder; Claude saves each reply to the run's `reports/` folder itself, and checks again that no saved report has changed before passing reports on. Judges, appeals and checks are never told which model or effort level produced a finding, or given the run's record.
6. **Summarise.** Claude copies the findings, word for word, into `summary.txt`.
7. **Check.** A fresh agent compares the summary with the reports and lists each finding that's missing, changed or has no source. Claude shows you the list and fixes the summary.
8. **Log.** Claude records the counts, each agent's tokens, tool uses and time as Claude Code reported them (wall-clock time, which in Manual mode includes any wait for your approval of a permission prompt, so it isn't pure model time), every finding whose severity the judge set differently from a reviewer's, up or down, the check's counts, and anything Claude did differently from the skill's instructions in `run.json`. Later rechecks of fixes go in the same file.

## Permission prompts

In Claude Code's default (Manual) mode, two kinds of step in a run ask for your approval. Others can too, such as a subagent reading material outside your project, or running the material's code if you allowed that.

**Reading Lite's brief templates.** They're in the plugin's own folder, outside your project, so Claude Code asks before Claude reads each one, before any agent starts. When it asks, either:

- choose **Yes, allow reading from <Lite's skill folder> during this session**, if you're happy to trust the templates. The prompt shows the folder's path. In our run, this one answer covered all 5 template reads of a Standard council; or
- choose **Yes** to approve each read on its own. A Standard council reads 5 template files.

A session that can't ask, such as `claude -p`, refuses them and the run stops; a `Read` allow rule for that folder fixes it. An installed copy is outside your project too, so expect the same prompts, though we've only tested a copy loaded with `--plugin-dir`.

**Saving files.** Claude does all the writing, and every file write asks for your approval: each agent's report (up to 22 for the largest council), the briefs, the summary and `run.json`, which it writes several times in a run, so a run can mean two or three times that. Background agents' prompts appear in your main session. If you answer one with a choice that lasts beyond that write, such as allowing it for the rest of the session, Claude Code applies it to the whole session, every later agent included. To avoid the prompts, either:

- **Allow writes to the Lite folder only (recommended):** add this rule to your project's `.claude/settings.json` (shared with your collaborators) or `.claude/settings.local.json` (only you):

  ```json
  {
    "permissions": {
      "allow": ["Edit(/reviews/lite/**)"]
    }
  }
  ```

  `Edit` rules cover every built-in tool that writes files, Write included. In these two files, a path that starts with `/` is read from the folder you started Claude Code in, so start it in the project root (or change the path). Change it too if you chose another Lite folder.

  Claude Code applies a project's allow rules only after you've accepted the trust dialog for the folder, which it shows in interactive sessions; Anthropic's permissions docs describe the exceptions for `.claude/settings.local.json`. `claude -p` and SDK sessions never apply allow rules from `.claude/settings.json`. In our test, with this rule given to Claude Code, a subagent's write to `reviews/lite/` went through without a prompt, and its write to another folder wasn't allowed.

  The rule lets anything in the session that can write, Claude itself and, if you allowed running code, the general-purpose agents, write anywhere under `reviews/lite/`, earlier runs included. The read-only agents can't write at all. That's why Claude compares each saved report with the agent's reply and stops if one changes.
- **Only the Lite folder, and nothing else (strictest):** with the rule above, start Claude Code with `claude --permission-mode dontAsk`. In that mode Claude Code denies, without asking, every call that would otherwise prompt you, and still runs calls your allow rules cover and reads inside your project. So Claude's own writes can go only to `reviews/lite/`, and anything else is refused. We tested this in a scratch project: a read and a write under `reviews/lite/` went through, and a write to another file was denied. Two things to add, because nothing can prompt in that mode: a `Read` allow rule for Lite's skill folder (the template-read prompt in a normal session shows its path), and an allow rule for anything else you want the session to do. Anthropic's permissions docs say why a deny rule can't do this instead: rules are checked deny first, and an allow rule can't carve an exception out of a deny rule. An agent definition can't either: its `tools` field limits which tools an agent has, not where they write.
- **Accept edits:** press `Shift+Tab` from Manual mode to switch to accept-edits mode before the run (or start Claude Code with `--permission-mode acceptEdits`). Claude Code then accepts file edits, and common file commands such as `mkdir`, `mv` and `rm`, inside your working directory without asking, for the whole session and not only the reviews; subagents run in the same mode. Since the agents read material that may contain instructions, this lets a misled agent change other files in your project without asking, so prefer the rule above.

In both our interactive Manual-mode runs with the rule above, nothing asked after the template reads: not the agents' saves (Lite 1.3.0 and earlier, when agents saved their own reports), not Claude's own writes, and not the check for linked folders.

Lite doesn't change your settings; add the rule yourself if you want it. If you deny a write's prompt, that report isn't saved, and Claude says so.

**Auto mode** decides each step without asking you, so a run in auto mode shows nothing about which steps would ask in Manual mode.

## Version 2 vs version 3

Lite 1.3.0 and later build brief version 3 unless you ask for version 2. Version 2 is the wording of every Lite run before 1.3.0 (and of the full edition's runs before its 1.5.0), kept byte for byte. Version 3 came from three stress-test runs of the full edition's appeal, which showed the judge could never raise a severity, and a judge running a "scratch" check although running code wasn't allowed. Every difference:

| | Version 2 | Version 3 |
|---|---|---|
| The judge's rulings | REAL, OVERSTATED (with the right severity) or NOT REAL. It can keep or cut a reviewer's severity, but has no ruling to raise one, so a finding never ended above its highest reviewer's rating. | Five rulings, each comparing the judge's own severity with the highest a reviewer gave: REAL (same severity), PARTLY REAL (part of the claim doesn't hold, same severity), OVERSTATED (lower), UNDERSTATED (higher, so a severity can be raised) and NOT REAL. Every OVERSTATED and UNDERSTATED finding comes with each reviewer's severity, the judge's, and a one-line reason. |
| How the judge sets severity | Not stated; when reviewers disagreed, judges took the higher rating. | From the evidence, "not from how many reviewers raised it or how they rated it". |
| Numbering | Left to the judge (numbers or letters). | M1, M2 and so on, with the reports each came from and the severity each gave. |
| The final ranked list and the counts | REAL and OVERSTATED findings; `run.json` counts confirmed, overstated and dismissed. | Every finding that isn't NOT REAL, with its number and ruling; `run.json` also counts `partlyReal` and `understated`. The check brief has version 3 rules to match (`rule-judged-v3`, `rule-appeal-v3`). |
| The appeal | Checks only the judge's dismissals, and runs only if there were any. | Also checks every severity the judge changed and every other finding it kept at LOW: SEVERITY FAIR, TOO LOW, TOO HIGH or UNSURE. It runs in nearly every judged council. Every verdict is logged, fair ones included, and how often it disagrees on unchanged LOW findings is shown separately. It never sees which model or effort produced a finding. |
| The running-code rule | "Don't run code from the material unless your brief says the user allowed it": silent on checks of the agent's own. | "Run nothing unless the user allowed running code": no code from the material, no scripts or one-off checks of its own, no tests and no shell commands. |
| How that rule is enforced | Only by the brief's wording. | By a tool limit: every agent is the plugin's read-only agent, `council-dial-lite:council-reader`, with only the Read, Grep and Glob tools. If you allow running code, the agents are general-purpose. |
| The agent's system prompt | The general-purpose agent's built-in system prompt, with every tool your session allows. | The read-only agent's own definition is its system prompt, in place of the general-purpose one: two short sentences telling it to do what the message asks, and that it can read and search files but can't run commands or programs or change any file. |
| The type's name | Such as `lite: specialised-3/judged+appeal @sonnet`. | The same with ` v3` before the models. |

**Both versions:** from Lite 1.4.0, Claude saves every agent's report itself (above), and before a run Claude reads the effort levels saved in the project's settings files for seats on another model. Neither is a brief difference.

**Why runs of the two versions must be compared separately:** version 3 changes what the judge may rule and how findings are counted, what the appeal checks and so how often it runs and what it costs, and what the agents can do and the system prompt they start from. A difference between a version 2 and a version 3 run could come from the version, not the council type, so their names differ and Claude never groups them together.

## Why the agents can't write (Lite 1.4.0)

Up to Lite 1.3.0, each agent saved its own report with the Write tool, so every agent had a tool that writes files. From 1.4.0 the agents only read: Claude, the main session, saves each agent's reply itself, with its Write tool, and only into the run's folder under `reviews/lite/`. The read-only agent has exactly the Read, Grep and Glob tools, the same as the full edition's; in a scratch test it listed only those three, and couldn't run a command or write a file.

Why: the agents read material that may contain instructions, and an agent that can write can be told to change files, whatever its brief says. Taking the Write tool away removes that, and it also ends the skipped and differing saves the earlier trials showed, since the saved report is now always the reply. The main session is the only writer left, and the Edit rule above, or `dontAsk` mode for the strictest setup, keeps its writes to the Lite folder.

This applies to both brief versions. Version 2 runs from 1.4.0 on differ from earlier Lite version 2 runs only in that the agents are no longer asked to save; their briefs are the same.

## What it reads and writes

- **Reads:** the material you ask it to review, its own brief templates, and the Lite folder (earlier runs' `run.json` files, for estimates and comparisons).
- **Writes:** only inside `reviews/lite/` in your project, or another folder you choose inside the project (not inside `.git` or `.claude`, and not the full edition's log folder), all written by Claude itself: the read-only agents write nothing (if you allowed running code, the general-purpose agents may write scratch files in temporary folders of their own). Each run gets a folder, `reviews/lite/<run id>/`, with `run.json`, the briefs in `briefs/`, each agent's report (the check's included) in `reports/`, and `summary.txt`. Before the first write, Claude checks the Lite folder and the folders above it aren't links, and stops if they are. Reports, briefs and the summary are plain text files, so links, images and HTML copied from the material never render or load when opened on GitHub or in a Markdown viewer.
- **Runs:** no scripts or programs of its own. Before a run's first write, Claude lists the Lite folder and the folders above it with your session's shell tool, to check none is a link, and uses the shell for nothing else; without a shell tool, it tells you it can't check and asks whether to go on. The subagents are ordinary Claude Code subagents with the tools your session allows, under your permission settings. They run code from the material only if you say they may; otherwise the briefs tell them to read it only.

## Where it works

- **Claude Code:** with subagents (the Agent tool) and file tools. This is where it was written to run.
- **Cowork and claude.ai chat:** not tested. A council needs a tool that starts subagents and tools that read and write files in your project. Anthropic's docs don't say which chat or Cowork sessions have those, so whether a council runs there is untested.

## Tested

Four Standard councils ran on the full edition's small sample module on 8 November 2026, in Claude Code 2.1.319 on Windows 11, with Lite loaded with `--plugin-dir` and the `Edit(/reviews/lite/**)` rule in the scratch project's untracked `.claude/settings.local.json`. The first three ran Lite 1.0.0 with the subagents on Sonnet (the headless one before Lite's save line named the Write tool, the two interactive ones after), and the fourth ran Lite 1.1.0 on Opus.

**Headless** (`claude -p`, with a `Read` rule for Lite's folder as well):
- The headless session waited for all six background agents and finished the run.
- The rule applied: every agent's save with the Write tool went through without a prompt. Four agents first tried to save with shell commands, which were refused, because a background agent can't ask in a headless session; they then used the Write tool. In the two interactive runs, whose save line named the Write tool, no agent used a shell tool at all.
- Two of the six saved files differed from the agent's reply: one dropped its Markdown formatting, and the judge's was worded differently in places. Claude caught both and used the replies, as SKILL.md says.

**Interactive, in auto mode:** nothing asked, as auto mode decides each step itself, so this run shows nothing about prompts. All six agents saved their own reports.

**Interactive, in Manual mode:**
- The only prompts were 8, all for the main session's reads of the brief templates (a listing of `briefs/`, `check-brief.md`, and each file in `briefs/`), before any agent started. Nothing asked after that. Lite now reads only the templates a run needs, by name.
- Two of the three reviewers replied without saving their reports. They weren't refused: they never called the Write tool. Claude saved their replies itself and marked them as saved by the main session, as SKILL.md says. Lite now gives each agent its save line first and again last.
- The judge found nothing to dismiss, so the appeal didn't run.

**In all three Lite 1.0.0 runs:**
- Where an agent saved its report, Claude compared it with the reply. In the interactive runs, the only differences were whitespace at the very end, which Lite now says doesn't count.
- The check found every finding in the summary copied exactly: 9, 13 and 14 MATCHES, with no CHANGED, NO SOURCE or LEFT OUT.
- All three kept all 9 problems in the sample's answer key, but they differed in the rest: 11, 16 and 14 merged findings, of which the judge dismissed 2, 3 and 0.
- The agents used 207,823, 285,366 and 228,059 tokens (headless, auto, Manual), and the critical paths took 2.1, 2.1 and 1.4 minutes.

**Lite 1.1.0, interactive, in Manual mode, on Opus** (no model set, so the subagents inherited the main session's model):
- One prompt only: the first template read. Choosing "Yes, allow reading from …/skills/council-dial-lite during this session" covered the other 4 reads, and nothing asked after that.
- All six agents saved their own reports, each matching its reply apart from whitespace at the end. No deviations were logged, though it skipped two re-reads that Lite 1.1.0 asked for and Lite 1.1.1 now allows. The check found all 12 findings copied exactly.
- It kept all 9 problems in the sample's answer key, as did the full edition's run on the same model and sample, and used 299,204 tokens (25% more than that run, mostly for the check) with a critical path of 4.2 minutes.
- The appeal has now checked 8 dismissals across four runs (three of Lite, one of the full edition) and called all 8 fair, each in 8 to 21 seconds. None of them was one we'd call wrong, so these runs don't show whether it would catch one.
- This run changed both the save line and the model, and the earlier skipped saves were on Sonnet, so it doesn't yet show that the new save line fixes them.

The runs are scored in full, and compared with two runs of the full edition, in [results/20261108-lite-runs.md](https://github.com/Ablation-Lab/council-dial/blob/main/results/20261108-lite-runs.md) in the full edition's repository.

Not tested yet: Lite 1.1.0's changes on Sonnet, where the saves were skipped; Lite 1.1.1's two clarifications (which run ids count when numbering, and when a report counts as read again); Lite 1.2.0's per-seat models and effort, and 1.3.0's brief version 3, read-only agent and severity checks, in a council run (the read-only agent was confirmed in a scratch test to have only its listed tools); an installed copy (rather than `--plugin-dir`), Cowork and chat.

## Privacy

Lite has no server, account or telemetry, and sends nothing anywhere itself. Each run's `run.json` holds: the date it was planned; the type (including any custom role names) and brief version; the one-line description of what was reviewed; the counts (findings per reviewer, combined, confirmed, overstated, dismissed, by severity, the baseline's, fixes and regressions after rechecks); for each finding the appeal flagged, a short title, the appeal's view and your call; the main session's model id and the subagents' model; each subagent's label, tokens, tool uses and time; the check's counts; short notes; and one line for each step Claude did differently from the skill's instructions.

The run folder also holds the full briefs, reports, summary and check report. Reports quote the material they reviewed, including code, file paths and anything sensitive in it, so for a private project keep `reviews/lite/` out of git by adding it to `.gitignore`. Keep the reports of security reviews out of git on public projects too, because they describe each problem in full. What the subagents review reaches Anthropic the way anything in your conversation does.

## Limits

- Nothing checks the log mechanically: Claude checks that counts add up, and works out estimates and the comparison table, by hand, so they can contain mistakes. The full edition's script does this for research runs.
- The check covers the summary against the reports. It doesn't check what Claude gave the judge, the appeal or the debate agents, which Claude copies from the agents' replies.
- The blind and independent agents are told not to open other files in the Lite folder, but Claude Code doesn't enforce that: an agent that ignores it could read other reports or the run's record.
- Claude saves each reply as it came back, so a report can hold invisible characters from the material; the full edition removes them.
- Claude doesn't always follow every rule in the skill. It logs what it did differently in the run's `deviations`, but only what it notices.
- Lite can't read the session's effort level, or `CLAUDE_CODE_SUBAGENT_MODEL`; tell Claude if you set that.
- The check adds one agent to every run, so a Lite run uses more tokens than the same council in the full edition.
- Kept shares aren't comparable across combine methods: compare types within one method, or score runs against an answer key where one exists.

## Support

Report problems at https://github.com/Ablation-Lab/council-dial-lite/issues.

## Licence

MIT. See the `LICENSE` file.

Research by Ablation Lab.
