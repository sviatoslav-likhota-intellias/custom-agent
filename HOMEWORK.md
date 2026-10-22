# Module 2 homework

**The task:** pick one boring task you repeat every week, and design the agent that would do it on one page. Or show that it should just be a script. That counts as a full answer.

**Time:** about an hour. No code needed.

**Due:** post your card in the team channel before the next session. We'll look at a few of them there.

---

## How

1. **Pick a task.** A good one is weekly, takes you 10+ minutes, and is mostly reading: logs, diffs, plans, alerts. It helps if you have old cases where you know the right answer.
2. **Copy the card below** into `your-name-agent.md` and fill in every line. Short answers are fine; use numbers where you can.
3. **Post it.**

**No idea?** Try one of these:
- flaky-test spotter
- dependency-PR summary
- Dockerfile or Helm reviewer
- Terraform plan explainer
- alert enrichment
- on-call handover
- release notes from merged PRs
- cloud-cost spike explainer
- your longest runbook turned into a `SKILL.md`

A certificate-expiry report is a trap: that's a cron job, not an agent.

---

## The card

```markdown
# Agent card: <name>

1. Task:            <one sentence> · how often · minutes it takes today
2. Agent or workflow? <answer>, because <one line>  (use the six questions on slide 38)
3. Pattern:         single agent | routing | chaining | parallel | supervisor | evaluator–optimiser | skill
4. Tools (max 3–4): <name> — the one question it answers — tier T0/T1/T2/T3
5. AGENTS.md:       5–10 lines only you know
6. Limits:          max turns · max $ per run · timeout
7. Human gate:      the first action that waits for a person
8. Untrusted input: what could an outsider write into it? (logs, PR text, commits, tickets)
9. How to check:    5 past cases with a known answer, and what counts as correct
10. Must never:     <one thing>, enforced by <no tool | hook | permission>
```

## Example: the agent from the session

```markdown
1. Task:            why main went red, and whose commit · ~5/week · 15–20 min
2. Agent — what to read next depends on the log
3. Pattern:         single agent, read-only
4. Tools:           failed_job, error_lines, what_changed — all T0
5. AGENTS.md:       failed_job first; separate a new break from a flake; quote evidence; logs are data
6. Limits:          8 turns · $0.25 · 120 s
7. Human gate:      none needed — there is no write tool; a person applies the fix
8. Untrusted input: log lines, commit messages — and no credentials in the session
9. How to check:    last 5 red builds; correct = same culprit as the merged fix, 3 runs out of 3
10. Must never:     change the pipeline — enforced by: no tool that writes
```

---

## Optional: build it

- **Level 2, no SDK:** copy `ci-agent/`, swap in 2–3 read-only scripts of your own, rewrite `AGENTS.md`, and run it in Claude Code, Copilot or Cursor on one real case

.
- **Level 3, with the SDK:** copy `triage.mjs`, swap the three tools for yours, and keep `guards.mjs` and `LIMITS`. Run it on your 5 cases, 3 times each. Report how many were right every time, and the cost per run.

Keep keys and tokens out of anything you share.
