---
name: pi-session-tracker
description: Monitor and report on active pi agent training sessions. Use when asked to "track a pi session", "report on training", "what is pi doing", "follow the training", "monitor the experiment loop", or to check on a running autotrain/autoresearch session.
---

# Pi Session Tracker

Read pi's session JSONL logs, produce a structured quantified report, and optionally loop for periodic updates.

## Finding sessions

Sessions live at `~/.pi/agent/sessions/`. Folder names are mangled paths (e.g., `--Users-vm-code-myproject--/`).

```bash
# Find recently active sessions
for d in ~/.pi/agent/sessions/*/; do
  f=$(ls -t "$d" 2>/dev/null | head -1)
  if [ -n "$f" ]; then
    mod=$(stat -f '%m' "$d$f")
    age=$(( ($(date +%s) - mod) / 60 ))
    [ $age -lt 60 ] && echo "($age min ago): $d$f"
  fi
done
```

## Session JSONL format

Each line is JSON. Key message structure:

- `type: "session"` — first line: `cwd`, `timestamp`
- `type: "model_change"` — `provider`, `modelId`
- `type: "message"` — `message.role` + `message.content[]` array of blocks:
  - `{type: "text"}` — visible output. User messages with `<skill` prefix have user text after `</skill>`
  - `{type: "thinking"}` — agent reasoning in `.thinking`
  - `{type: "toolCall"}` — `.name` + `.arguments` (bash `.command`, file `.path`, experiment params)
  - `{type: "toolResult"}` — tool output in nested `.content[].text`

## Reading and reporting

Read the JSONL directly with the Read tool. For large files, paginate with `offset`/`limit`. Categorize what you find:

- **bash commands** — count total, note `hf models ls/info` (model search), `hf datasets ls/info/sql` (dataset search/SQL), `hf download/upload`, `hf repos/buckets create`, `git commit`
- **file operations** — `write`, `edit`, `read` tool calls with paths
- **experiments** — `init_experiment`, `run_experiment` (command), `log_experiment` (metric, status, description)
- **errors** — crashed commands, failed experiments, tracebacks
- **agent thinking** — key decisions, what it noticed or missed

## Report format

Write a markdown file with:

1. **Session metadata** — project path, model, timestamp
2. **User prompts** — exact text
3. **Chronological phases** — group actions into logical phases (recon, data inspection, env setup, file creation, experiment loop). Per-phase tables with action counts and details.
4. **Experiment log** — numbered: command, config, result, metric value, status, commit hash
5. **Cumulative totals** — table counting every action type
6. **Observations** — mistakes, missed signals, interesting decisions
7. **Update log** — timestamped entries for each report refresh

**Quantify everything.** "23 SQL queries" not "several". "test_loss 2.988 → 2.495 (-16.5%)" not "loss improved".

## Loop mode

When called during an active session or when asked to "keep tracking":

1. Write initial report, note the session file line count
2. Set a 15-minute background timer (`sleep 900` with `run_in_background`)
3. On timer: read only new lines (use `offset` = previous line count), append update section to report
4. Repeat until user interrupts or no new lines appear
