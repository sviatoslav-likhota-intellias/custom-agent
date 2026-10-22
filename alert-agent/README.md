# alert-agent

**Draft.** Level 2 of the Module 2 homework: the agent card in `../sviatoslav-likhota-agent.md`, built
the way `m2-demo/ci-agent/` is: an `AGENTS.md` and read-only scripts, run inside a coding agent (Kiro,
Claude Code, Copilot, Cursor). No SDK, no standalone process.

## Try it with no credentials

The sample case in `fixtures/sample/` is **synthetic**: made-up alerts and logs in our alert-name format.
It exists to smoke-test the tools, not to grade the agent.

```bash
export ALERT_FIXTURES=fixtures/sample          # PowerShell: $env:ALERT_FIXTURES = "fixtures/sample"
python scripts/alert_tools.py open_alerts
python scripts/alert_tools.py alert_detail example-errors-prd-connector-integration1
python scripts/alert_tools.py kibana_logs example-errors-prd-connector-integration1
python scripts/alert_tools.py kibana_logs kafka-lag-prd --service example.apps.order-sync
```

The sample covers: auto-close alerts still open, an empty "Log Message" (600 identical errors collapsed
into one line), a Kafka alert with no service, the STG Kafka restart pair with one error that isn't
restart-related, a secret in a log line (redacted), and a log line that tries to instruct the agent.

Then open this folder in your coding agent and ask it to triage. Python 3.8+, standard library only.

## Run it on real alerts

1. `cp .env.example .env` and fill it in. Every credential must be **read-only**: an OpsGenie API key
   with only "Read" ticked, and a Kibana or Elasticsearch role that can only read the log indices.
2. Check the field names (`FIELD_*`) and `KIBANA_INDEX` against a log document in Kibana Discover.
3. Run `python scripts/alert_tools.py open_alerts` by hand first and check the ranking.

## What is enforced, and what is only asked

| Rule | Enforced by |
|---|---|
| Never close, ack or snooze an alert | **Credentials:** the OpsGenie key can only read. The script only sends GET. |
| Logs are only read | **Credentials:** read-only role. The script only sends `_search`. |
| Logs stay scoped to the alert's service, env and ±30 min max | **Tool code:** `kibana_logs` takes those from the alert; `--service` is refused when the alert names its own service. |
| Context budget | **Tool code:** repeats collapsed into counts, 200 lines and 12,000 chars per call at most. |
| Secrets don't reach the model | **Tool code:** redaction on every result. Patterns, not a guarantee. |
| Injection attempts are reported | **Tool code** flags lines that read like instructions; **AGENTS.md** says to report them. Nothing checks the final answer: in Level 2 there is no code after the model. |
| Audit trail | **Tool code:** one line per call in `logs/audit-<date>.jsonl`. |
| 6 calls per alert, 8 alerts per run, the answer format | **AGENTS.md only.** A request, not a guarantee. |
| Agent can't edit files, browse, or read `.env` | `.claude/settings.json` in Claude Code. In Kiro: to check. |

## To check in Kiro

- Does it read `AGENTS.md`? If not, copy it to `.kiro/steering/` or point a steering file at it.
- Can it be limited to running only `scripts/alert_tools.py`, with no file edits and no web access?
- Is `.env` hidden from it?

## Next steps

- Record the 5 past cases from the card's field 9 as fixtures: `alerts.json`, `alerts/<alias>.json`,
  `logs/<service>.json`, the same shape as `fixtures/sample/`. Do this while their logs are still in
  Kibana. Strip customer data before saving them.
- Put the known outcome of each case next to it, out of the agent's reach, and compare `verdict` and
  `owner` with a small script, 3 runs each.
