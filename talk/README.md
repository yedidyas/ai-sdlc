# Resources from the talk

Everything cited in **"Before You Pick an Agent Orchestrator: What I Learned Wiring a Self-Driving SDLC"**, grouped by the five parts of the talk.

- Want to go deeper on any topic? The [library](../library/README.md) has 21 notes and more than a thousand sources.

"(vendor)" marks an author who sells a related product. Read those as marketing until shown otherwise.

## Part 1: The big picture

| Resource | Why it is here |
|---|---|
| [12-Factor Agents: Patterns of reliable LLM applications](https://www.youtube.com/watch?v=8kMaTybvDUw) by Dex Horthy (HumanLayer), talk | "Own your state and your control flow. Agents are software." The one-line answer to "who decides what happens next". |
| [12-Factor Agents](https://github.com/humanlayer/12-factor-agents), repo | The written version of the twelve factors. |
| [Why Software Factories Fail](https://ai.engineer/talks/Ib5GBkD555M-why-software-factories-fail) by Dex Horthy, talk | The strongest public counterweight to a self-driving SDLC. Watch it before you build one. |

## Part 2: Why I built it

| Resource | Why it is here |
|---|---|
| [Guide to the Figma MCP server](https://help.figma.com/hc/en-us/articles/32132100833559) | How agents read a designer's hand-off instead of guessing from screenshots. |

## Part 3: The hard parts

### Agents that stop early and still say "success"

| Resource | Why it is here |
|---|---|
| [Why Do Multi-Agent LLM Systems Fail? (MAST)](https://arxiv.org/abs/2503.13657), paper | 1,600+ multi-agent traces. Not knowing when to stop is one of the most common failures. |
| [Coding agent behaviors across 8,600 sessions](https://transluce.org/docent/blog/coding-agent-behaviors) by Transluce (Docent) | Agents oversell their own results. Completion has to be decided by code. |
| [Effective harnesses for long-running agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents) by Anthropic (vendor) | Agents declare done too early. A harness has to check. |

### Durable execution and determinism

| Resource | Why it is here |
|---|---|
| [Temporal: Workflow Definition](https://docs.temporal.io/workflow-definition) | Deterministic workflow code, and "LLM/AI invocations" belong in Activities. |
| [Temporal: Detecting Activity failures](https://docs.temporal.io/encyclopedia/detecting-activity-failures) | Heartbeats and timeouts. Long, silent agent steps need them. |
| [Temporal Cloud limits](https://docs.temporal.io/evaluate/cloud/limits) | Payload and history limits: pass pointers, not files. |
| [Demystifying Determinism in Durable Execution](https://jack-vanlightly.com/blog/2025/11/24/demystifying-determinism-in-durable-execution) by Jack Vanlightly | Why control flow must be deterministic and side effects idempotent. |
| [Two Roads to Durable Agents: Replay vs. Snapshot](https://ai.engineer/talks/svCnShDvgQg-two-roads-durable-agents-replay-vs-snapshot) by Eric Allam (Trigger.dev, vendor), talk | Replay versus snapshot, from a vendor that ships the snapshot road. |
| [Langfuse: Temporal integration](https://langfuse.com/integrations/frameworks/temporal) | Join the workflow trace and the model trace with one trace id. |

### The engine and agent comparison

These are the pages behind the "everything I tried, and the limit that ruled it out" slides.

| Option | Source |
|---|---|
| One big prompt: Claude Code as the orchestrator, on a GitHub self-hosted runner | [Agent SDK: the agent loop](https://code.claude.com/docs/en/agent-sdk/agent-loop) |
| LangGraph | [Checkpointers](https://docs.langchain.com/oss/python/langgraph/checkpointers), [Interrupts](https://docs.langchain.com/oss/python/langgraph/interrupts) |
| LangGraph Agent Server (hosted) | [langgraph#7417](https://github.com/langchain-ai/langgraph/issues/7417) |
| Airflow | [Durable execution (common AI provider)](https://airflow.apache.org/docs/apache-airflow-providers-common-ai/stable/durable_execution.html) |
| Prefect | [Detect zombie flows](https://docs.prefect.io/v3/advanced/detect-zombie-flows) |
| AWS Step Functions | [Service quotas](https://docs.aws.amazon.com/step-functions/latest/dg/service-quotas.html) |
| Inngest / Trigger.dev | [Inngest usage limits](https://www.inngest.com/docs/usage-limits/inngest) |
| DBOS | [Workflow recovery](https://docs.dbos.dev/production/workflow-recovery), [Upgrading workflows](https://docs.dbos.dev/python/tutorials/upgrading-workflows) |
| Restate | [Service configuration](https://docs.restate.dev/services/configuration), [docs-restate#410](https://github.com/restatedev/docs-restate/issues/410) |
| Temporal | [Detecting Activity failures](https://docs.temporal.io/encyclopedia/detecting-activity-failures), [sdk-python#1890](https://github.com/temporalio/sdk-python/issues/1890), [sdk-python#1050](https://github.com/temporalio/sdk-python/issues/1050) |
| Hatchet | [Timeouts](https://docs.hatchet.run/home/timeouts) |
| Conductor OSS | [AI failure semantics](https://conductor-oss.github.io/conductor/devguide/ai/failure-semantics.html) |
| Claude Code headless (`claude -p`) | [claude-code#98170](https://github.com/anthropics/claude-code/issues/98170) |

### The hard constraint: it must run inside my network

Hosted coding agents I ruled out because they could not reach a private VPC (or only on an enterprise plan):

| Option | Source |
|---|---|
| GitHub Copilot cloud agent | [About the cloud agent](https://docs.github.com/en/copilot/concepts/agents/cloud-agent/about-cloud-agent) |
| Claude Code on the web | [Self-hosted environments](https://code.claude.com/docs/en/self-hosted-environments) |
| OpenAI Codex Cloud | [Codex Cloud with Tailscale](https://tailscale.com/blog/codex-cloud-tailscale) |
| Google Jules | [FAQ](https://jules.google/docs/faq/) |
| Kiro cloud sessions (AWS) | [GitLab integration](https://kiro.dev/docs/web/gitlab/) |
| Cloudflare Workflows | [Workers VPC](https://developers.cloudflare.com/workers-vpc/) |
| Vercel Workflow | [Function limitations](https://vercel.com/docs/functions/limitations) |
| LangGraph Cloud (Developer and Plus plans) | [Pricing](https://www.langchain.com/pricing) |
| Devin (below Enterprise) | [VPC deployment](https://docs.devin.ai/enterprise/vpc/overview) |

### Worth checking, including paid options

| Option | Source |
|---|---|
| Cursor Cloud Agents, self-hosted pool (can be triggered from Port) | [Self-hosted pool](https://cursor.com/docs/cloud-agent/self-hosted-pool) |
| Claude Managed Agents, self-hosted sandboxes | [Self-hosted sandboxes](https://platform.claude.com/docs/en/managed-agents/self-hosted-sandboxes) |
| Temporal Cloud | [Pricing](https://docs.temporal.io/cloud/pricing) |
| DBOS Conductor | [Pricing](https://www.dbos.dev/dbos-pricing) |
| Restate Cloud / BYOC | [BYOC overview](https://docs.restate.dev/byoc/overview) |
| Coder Agents | [Licensing and usage](https://coder.com/docs/ai-coder/agents/licensing-usage) |
| Flyte 2 / Union.ai | [Claude Agent SDK integration](https://www.union.ai/docs/v2/flyte/integrations/agents/claude-agent-sdk.md) |

## Part 4: How others do it

| Company | Resource |
|---|---|
| Stripe | [Minions: Stripe's one-shot, end-to-end coding agents](https://stripe.dev/blog/minions-stripes-one-shot-end-to-end-coding-agents) |
| Spotify | Honk, the background coding agent: [Part 1](https://engineering.atspotify.com/2025/11/spotifys-background-coding-agent-part-1), [Part 2: context engineering](https://engineering.atspotify.com/2025/11/context-engineering-background-coding-agents-part-2), [Part 3: feedback loops](https://engineering.atspotify.com/2025/12/feedback-loops-background-coding-agents-part-3), [Part 4: dataset migrations](https://engineering.atspotify.com/2026/4/background-coding-agents-dataset-migrations-honk-part-4), [InfoQ on the Honk rewrite](https://infoq.com/news/2026/03/spotify-honk-rewrite) |
| Ramp | [Why Ramp built Inspect](https://newsletter.pragmaticengineer.com/p/why-ramp-built-inspect) (The Pragmatic Engineer) |
| Uber | [Agentic SDLC at Uber: building blocks for Uber's software factory](https://ai.engineer/talks/17-YSUHo6Lk-agentic-sdlc-at-uber-building-blocks-ubers), talk |
| Shopify | [River](https://shopify.engineering/under-the-river) (Shopify Engineering) |
| Block | [Q1 2026 shareholder letter (Builderbot)](https://s29.q4cdn.com/628966176/files/doc_financials/2026/q1/Block_Q1-2026-Shareholder-Letter.pdf), [Actionable CI at Block](https://engineering.block.xyz/blog/actionable-ci) |
| DoorDash | [DoorDash Flux cloud agent](https://infoq.com/news/2026/08/doordash-flux-cloud-agent) (InfoQ) |
| Coinbase | [Over 95% of Coinbase's code is now written with AI](https://cointelegraph.com/news/over-95-of-coinbases-code-is-now-written-with-ai) |
| Intercom | [How Intercom 2x'd their engineering](https://www.lennysnewsletter.com/p/how-intercom-2xd-their-engineering) (Lenny's Newsletter) |

## Part 5: Is it worth it?

### Coding got faster

| Resource | Why it is here |
|---|---|
| [Microsoft FY26 Q4 earnings](https://www.microsoft.com/en-us/investor/events/fy-2026/earnings-fy-2026-q4) | GitHub: about one in three PRs comes from an agent. |
| [Google Cloud Next 2026 keynote](https://blog.google/innovation-and-ai/infrastructure-and-cloud/google-cloud/cloud-next-2026-sundar-pichai/) | Google's share of AI-written code. |
| [Redesigning source control](https://www.anthropic.com/engineering/redesigning-source-control) by Anthropic (vendor) | Merges per day inside Anthropic. |

### Features did not reach production much faster

| Resource | Why it is here |
|---|---|
| [Writing Code vs. Shipping Code (NBER w35275)](https://www.nber.org/papers/w35275) | Commits up 240% with autonomous agents, but only 80% for projects and 30% for releases. |
| [Harvard study of 718 companies](https://fion.ac/jellyfish.pdf) | More code, a smaller gain in shipped work, and longer review times. |
| [Measuring the impact of early-2025 AI on experienced open-source developers](https://metr.org/blog/2025-07-10-early-2025-ai-experienced-os-dev-study/) by METR | A randomized trial: 19% slower, while believing they were faster. |

### The new bottleneck is everything around the code

| Resource | Why it is here |
|---|---|
| [Coding Is No Longer the Constraint](https://engineering.atspotify.com/2026/6/code-with-claude-coding-is-no-longer-the-constraint) by Spotify | 76% more PRs to review. |
| [Employment at Anthropic](https://www.anthropic.com/research/employment-at-anthropic) by Anthropic (vendor) | Time spent waiting for a human, agent instructions and meta-work. |
| [Stack Overflow 2026 developer survey](https://stackoverflow.blog/2026/10/06/the-results-of-the-2026-developer-survey-are-here/) | How few developers use AI to deploy or run production. |

### Every burn became a guardrail

| Resource | Why it is here |
|---|---|
| [Replit's AI coding assistant deleted a database](https://www.eweek.com/news/replit-ai-coding-assistant-failure/) (eWeek) | Why destructive actions need a gate. |
| [My AI adoption journey](https://mitchellh.com/writing/my-ai-adoption-journey) by Mitchell Hashimoto | Every mistake becomes a rule the agent follows next time. |

### Engineers build the factory, not the features

| Resource | Why it is here |
|---|---|
| [Agentic SDLC at Uber](https://ai.engineer/talks/17-YSUHo6Lk-agentic-sdlc-at-uber-building-blocks-ubers) | A context graph, 2,500 skills, 1,000+ tools behind one MCP gateway. |
| [Stripe Minions](https://stripe.dev/blog/minions-stripes-one-shot-end-to-end-coding-agents) | Toolshed: 400+ internal tools. |
| [DoorDash Flux](https://infoq.com/news/2026/08/doordash-flux-cloud-agent) | 300+ playbooks. |
| [When AI writes all the code](https://posthog.com/newsletter/when-ai-writes-all-code.md) by PostHog | "They build the software factory rather than the software." |
