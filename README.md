# pi-session-tracker

Pi skill that monitors running pi agent sessions and produces structured, quantified reports.

## What it does

- Finds active pi sessions from `~/.pi/agent/sessions/`
- Reads the session JSONL log and categorizes every action (bash commands, HF operations, SQL queries, experiments, file operations, errors)
- Writes a markdown report with chronological phases, experiment results, cumulative totals, and observations
- Optionally loops every 15 minutes to append updates while the session is still running

## Install

```bash
pi install https://github.com/gary149/pi-session-tracker
```

## Usage

```
track the pi session
```

```
report on the pi session in qwen-lora
```

```
follow the session and update every 15 minutes
```

## License

MIT
