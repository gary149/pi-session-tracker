# autotrain-session-tracker

Pi skill that monitors running [autotrain](https://github.com/gary149/autotrain) sessions and produces structured, quantified reports.

## What it does

- Finds active pi training sessions from `~/.pi/agent/sessions/`
- Reads the session JSONL log and categorizes every action (bash commands, HF operations, SQL queries, experiments, file operations, errors)
- Writes a markdown report with chronological phases, experiment results, cumulative totals, and observations
- Optionally loops every 15 minutes to append updates while training is still running

## Install

```bash
pi install https://github.com/gary149/autotrain-session-tracker
```

## Usage

```
track the pi training session
```

```
report on the autotrain session in qwen-lora
```

```
follow the training and update every 15 minutes
```

## License

MIT
