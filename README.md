# agentcents — LLM cost consulting skill for Claude

A [Claude Skill](https://agentskills.io) that turns Claude into a neutral LLM
cost advisor: estimate what any call or agent workflow will cost across
candidate models, get the **cheapest model that clears the task's capability
bar**, and see the comparison as a Mermaid diagram — with honest ranges, never
false point estimates.

Works two ways:

- **Consult mode (no install):** answers from a bundled, hand-verified
  snapshot of current model prices and Intelligence Index scores.
- **Live mode:** if the [agentcents](https://pypi.org/project/agentcents/)
  proxy is running locally, the skill uses your real ledger — calibrated
  forecasts, spend, and accuracy instead of priors.

## Install

**Claude.ai / Desktop:** Settings → Capabilities → Skills → Add → upload this
folder (or the packaged `.skill`).

**Claude Code:** `/plugin marketplace add labham-llc/agentcents-skill`
then `/plugin install agentcents@labham-skills`

Then just ask things like:

> what would it cost to classify ~500 support emails a day, about 200 tokens each?
>
> I'm building a 5-step research agent — which model should each step use?

## What's in the box

```
SKILL.md                 — the consulting method (measure, range, bar, pick, flag instability)
assets/pricing.json      — price + capability snapshot (see _meta.verified_utc)
references/local-api.md  — live-mode reference for a locally running agentcents
```

**Snapshot verified: 2026-07-15** (see `_meta.verified_utc`) — refreshed with each agentcents release.

## The product behind it

[agentcents](https://labham.com/agentcents.html) is a local proxy that meters,
forecasts, and budget-enforces LLM API calls — and grades its own forecasting
accuracy on a calibration page. `pip install agentcents`. The skill is free;
so is the agentcents free tier.

© Labham LLC · skill instructions and snapshot data may be redistributed with
attribution; the agentcents software itself is proprietary (see its LICENSE).
