# AI SDLC

An open reference library on how to build a software development lifecycle that AI agents can run: orchestrating coding agents, durable execution, verification, human gates, observability, cost, and the evidence on whether it pays off.

More than 1,300 sources are sorted by topic, each with a short summary of what it says and why it matters: docs, papers, talks, engineering blogs, case studies and practitioner threads.

It started as the research behind the Conf42 Platform Engineering 2026 talk **"Before You Pick an Agent Orchestrator: What I Learned Wiring a Self-Driving SDLC"**.

## Where to go

| If you want… | Go to |
|---|---|
| The sources from the talk, part by part | [talk/](talk/README.md) |
| The ten best sources, if you only have an evening | [Start here](#start-here-the-ten-best-sources) below |
| A deep dive on one topic | [The library](library/README.md) |
| To compare orchestration engines | [Engine Profiles](library/engine-profiles.md), then [Comparisons and Benchmarks](library/comparisons-and-benchmarks.md) |
| To learn how agent pipelines fail | [Agent Failure Modes](library/agent-failure-modes.md), then [Reward Hacking and Verification](library/reward-hacking-and-verification.md) |
| To know if it is worth it | [Productivity Evidence](library/productivity-evidence.md), [Cost Economics](library/cost-economics.md), [Engineering Case Studies](library/engineering-case-studies.md) |
| Every tool in the space | [Tools Radar](library/tools-radar.md), 582 entries |

## Start here: the ten best sources

Two sources on engines, three on what happens when deterministic workflows meet LLMs, two on verification, one on cost and two on productivity.

| # | Source | By, date | Why it matters |
|---|---|---|---|
| 1 | [Two Roads to Durable Agents: Replay vs. Snapshot](https://ai.engineer/talks/svCnShDvgQg-two-roads-durable-agents-replay-vs-snapshot) | Eric Allam (Trigger.dev, vendor), AI Engineer, 2026-05-10 | The clearest map of the two durability models, replay and snapshot, and where each breaks as agent sessions get longer. |
| 2 | [The Durable Function Tree - Part 2](https://jack-vanlightly.com/blog/2025/12/4/the-durable-function-tree-part-2) | Jack Vanlightly, 2025-12-04 | The honest answer on when you need durable execution and when you don't: "Large function trees are an anti-pattern". |
| 3 | [Demystifying Determinism in Durable Execution](https://jack-vanlightly.com/blog/2025/11/24/demystifying-determinism-in-durable-execution) | Jack Vanlightly, 2025-11-24 | Why control flow must be deterministic while side effects only need to be idempotent. In agent terms: how you end up paying twice for a model call. |
| 4 | [The immortal life of Pi (Running the Pi coding agent on Temporal)](https://temporal.io/blog/the-immortal-life-of-pi-running-the-pi-coding-agent-on-temporal) | Moe Abadi (Temporal, vendor), 2026-10-08 | A coding agent moved onto a thin controller: model and tool calls are Activities, and the workflow only counts steps and enforces budgets. |
| 5 | [Where Does Exactly-Once Live?](https://arxiv.org/abs/2609.29095) | Jiapeng Li, 2026-09-24 (preprint) | The pay-twice problem measured over 25,930 episodes: an idempotency key cuts duplicate side effects from 28% to 4%. |
| 6 | [Harness design for long-running application development](https://www.anthropic.com/engineering/harness-design-long-running-apps) | Anthropic (vendor), 2026-03-24 | A planner, generator and skeptical evaluator. Agents grading their own work end up "confidently praising the work". |
| 7 | [Failure-Transparent Agents](https://arxiv.org/abs/2609.35732) | Junru Zhu et al., 2026-09-28 (preprint) | False success after a tool failure drops from 22.8% to 0.8% when the agent must show evidence. |
| 8 | [Token Price Is the Wrong Number](https://blog.apac.insight.com/2026/07/06/cost-to-a-merged-feature) | Veli-Matti Vanamo (Insight APAC), 2026-07-06 | The same feature cost about $3 to about $33 to merge, depending on model and harness. |
| 9 | [Writing Code vs. Shipping Code (NBER w35275)](https://www.nber.org/papers/w35275) | Demirer, Musolff, Yang, 2026 | Commits up 240% with autonomous agents, but only 80% for projects and 30% for releases. |
| 10 | [Measuring the Impact of Early-2025 AI on Experienced Open-Source Developer Productivity](https://metr.org/blog/2025-07-10-early-2025-ai-experienced-os-dev-study/) | METR, 2025-07-10 | A randomized trial: developers were 19% slower with AI, while believing they were faster. |

With another hour: [Datadog's 100 Temporal mistakes](https://temporal.io/resources/on-demand/datadog-or-100-temporal-mistakes-and-how-to-avoid-them), [Checkpoints Are Not Durable Execution](https://www.diagrid.io/blog/checkpoints-are-not-durable-execution-why-langgraph-crewai-google-adk-and-others-fall-short-for-production-agent-workflows) (Diagrid, vendor), [We Let an AI Agent Say "I Passed." Was It Actually Good?](https://loop.circleci.com/we-let-an-ai-agent-say-i-passed-was-it-actually-good) (CircleCI, vendor) and Dex Horthy's [Why Software Factories Fail](https://ai.engineer/talks/Ib5GBkD555M-why-software-factories-fail).

## What's in the library

| Group | Notes |
|---|---|
| Engines: what keeps a run alive | [Engine Profiles](library/engine-profiles.md) · [Comparisons and Benchmarks](library/comparisons-and-benchmarks.md) · [Official Docs by Engine](library/official-docs-by-engine.md) · [Tools Radar](library/tools-radar.md) |
| The AI SDLC landscape: what runs inside each phase | [Autonomous Coding Agents](library/autonomous-coding-agents.md) · [Coding Agent Orchestration and Sandboxes](library/coding-agent-orchestration-and-sandboxes.md) · [AI Code Review and Quality Gates](library/ai-code-review-and-quality-gates.md) · [Spec-Driven Development](library/spec-driven-development.md) · [Context Engineering and Memory](library/context-engineering-and-memory.md) |
| Reliability: what goes wrong and how you catch it | [Agent Failure Modes](library/agent-failure-modes.md) · [Reward Hacking and Verification](library/reward-hacking-and-verification.md) · [Evals for Agentic Pipelines](library/evals-for-agentic-pipelines.md) · [Observability for Agent Workflows](library/observability-for-agent-workflows.md) · [Human in the Loop](library/human-in-the-loop.md) |
| Economics and evidence: what it costs and what it returns | [Cost Economics](library/cost-economics.md) · [Productivity Evidence](library/productivity-evidence.md) · [Engineering Case Studies](library/engineering-case-studies.md) |
| Catalogs: every source, by type | [Talks and Videos](library/talks-and-videos.md) · [Articles and Essays](library/articles-and-essays.md) · [Research Papers](library/research-papers.md) · [Reddit Voices](library/reddit-voices.md) |

## How to read it

- **It is a snapshot.** The research cut-off is October 2026. Prices, versions, limits and leaderboards move fast, so check the linked page before you rely on a number.
- **"(vendor)"** marks an author who sells a related product. Read it as marketing until something independent backs it up.
- **Preprints** (most arXiv papers) are not peer reviewed.
- **Reddit Voices** is what practitioners said in public threads. Those quotes are field voices, not fact-checked sources.
- **How it was built.** AI research agents collected the sources. A fact-check pass then opened every link and checked each number, date and title against the page, and corrected or dropped what it could not confirm.

## Contributing

Found a broken link, a wrong number, or a source that belongs here? Open an issue or a pull request. Please link the primary source and say what it adds.
