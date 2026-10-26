# Talks and Videos

> **How this catalog was built**
> All entries were fact-checked in October 2026 by opening every URL; numbers, versions and dates that could not be found on the page were removed. Links checked 2026-10-12. Videos were confirmed through YouTube oEmbed (title and channel). Lengths appear only where a fetched page stated them (see the Top 10 table); the rest are left blank rather than guessed.

## Top 10 if you only have a weekend

| Title | Speaker, venue | Date | Why this one |
|---|---|---|---|
| [Two Roads to Durable Agents: Replay vs. Snapshot](https://ai.engineer/talks/svCnShDvgQg-two-roads-durable-agents-replay-vs-snapshot) | Eric Allam (Trigger.dev), AI Engineer | 2026-05-10 | The clearest map of the three durability models, from a vendor that ships a snapshot engine. Allam says replay journals every LLM and tool call but meets determinism, versioning and growing-history pressure as sessions lengthen, and his payment example is that if a charge succeeds externally and the record is lost, replay alone cannot establish exactly-once charging. Trigger.dev pairs an append-only context log with Firecracker microVM snapshots (vendor figures: snapshots slightly under a second, restores a few hundred milliseconds). |
| [Every step you take, every call you make: the reliable agent stack - Giselle van Dongen, Restate](https://www.youtube.com/watch?v=cI7zfqusmFU) | Giselle van Dongen (Restate), AI Engineer World's Fair 2026 (YouTube; delivered 2026-07-02, 20-minute slot) | 2026-09-14 | The vendor's best case for an interactive agent: a Slack deep-research agent with journaled LLM and tool steps, a durable approval that suspends for a month, a Virtual Object that signals or cancels a running loop, and an LLM gateway with flow control. YouTube chapters let you jump to the part you need. |
| [Your agent architecture has a half-life of 6 months (AI Engineer World's Fair 2026)](https://ai.engineer/talks/X1kp-ABIIxQ-your-agent-architecture-has-half-life-6) | Dan Farrelly (CTO, Inngest), AI Engineer World's Fair 2026 | 2026-07-21 | Splits an agent system into execution (flow, state, durability, retries), context (models, prompts, tools, memory) and compute (sandboxes, browsers), which change at different speeds, and argues the stable investment is the execution layer because recovery state must outlive the worker. About 19 minutes by the page's transcript timestamps (inferred). An Inngest talk, so read it as advocacy with a useful frame. |
| [Brains vs Hands: How to Run AI Agents Safely in Production - Viren Baraiya (Orkes/Conductor)](https://ai.engineer/talks/NaOkR3VSfR4-brains-vs-hands-run-ai-agents-safely) | Viren Baraiya (creator of Netflix Conductor, Orkes), AI Engineer World's Fair 2026 (16:49) | 2026-07-01 | Draws a hard line: the non-deterministic LLM plans the next step and a deterministic harness executes it, with fixed procedures, approval gates, idempotency and recorded side effects. The schedule abstract adds that agents generating workflows at runtime break the durable-execution assumption that the workflow graph is known when the system is deployed. |
| [Loop Engineering from first principles - Kyle Mistele, HumanLayer](https://ai.engineer/talks/xIt_mTQp6mY-loop-engineering-from-first-principles) | Kyle Mistele (co-founder, HumanLayer), AI Engineer World's Fair 2026 (17:57) | 2026-06-30 | Treats coding-agent loops as control systems: define the desired state, sense violations deterministically, pick small bounded work with a controller, act through reviewable PRs. Recommends GitHub Actions or another CI system for dispatch, secrets and scheduling, with commit, push and PR creation done deterministically. |
| [The Agent Harness: Control Planes, Invariants, and Approval Boundaries for Production AI Agents](https://www.infoq.com/presentations/ai-agent-harness/) | Vinoth Govindarajan (OpenAI), QCon AI Boston 2026 (InfoQ) | 2026-09-21 | Uses real OpenClaw issues to show failures that are not hallucinations: user-visible success with no durable record, concurrent-writer races, stalled runs with no terminal event, capability drift, and tool "success" that never reached the user. Prescribes single-owner state, ordered mutations, bounded work, scoped authority and run receipts. |
| [Your LLM Judge Is a Confident Liar: Building Better Verifiers - Browserbase & Microsoft](https://ai.engineer/talks/xLxhT2ZI7UM-your-llm-judge-is-confident-liar-building) | Miguel González Fernández (Browserbase) and Corby Rosset (Microsoft), AI Engineer World's Fair 2026 | 2026-07-02 | A memorable number: the same web agent scored about 74% under a benchmark's GPT-4o judge and about 38% under a verifier validated against human labels. Their rubric-based verifier checks ground-truth state instead of agent claims, and they released the human labels (CUAVerifierBench). |
| [Rewriting All of Spotify's Code Base, All the Time](https://www.infoq.com/presentations/spotify-ai-codebase-migration-agent/) | Jo Kelly-Fenton and Aleksandar Mitic (Spotify), QCon London 2026 (InfoQ) | 2026-08-07 | Spotify's agents gamed the build (downgrading a Java version because it was the easiest way to pass, removing tests), so Spotify added an LLM judge and then separated the verification runtime from the agent runtime behind a verification service that drives existing CI. A fleet migration of 1,000 merged PRs that took 3 months now takes 10 days, and review is the new bottleneck. |
| [Why Software Factories Fail - Dex Horthy, HumanLayer](https://ai.engineer/talks/Ib5GBkD555M-why-software-factories-fail) | Dex Horthy (HumanLayer), AI Engineer World's Fair 2026 (19:18) | 2026-06-30 | The strongest public counterweight to a self-driving SDLC. Horthy argues review quality falls, incidents and bugs rise, and the bottleneck moves to human review, and says "no amount of harness engineering or loops maxing can solve what is fundamentally a model training issue". Opens with the 1968 NATO history of the term software factory. |

## If you have one more hour

Three more, short and cheap to add. On the stated page lengths they total about 30 minutes (6, 10 and 14:10), which leaves room to rewatch one of the ten.

| Title | Speaker, venue | Date | Why this one |
|---|---|---|---|
| [Don't Let the LLM Drive - Ornella Bahidika & Joel Allou, Microsoft](https://ai.engineer/talks/m24UKZomm7k-dont-let-llm-drive) | Ornella Bahidika and Joel Allou (Microsoft), AI Engineer World's Fair 2026 (six minutes) | 2026 | A voice tutor, Ace, where an external state machine (intro, teach, check, grade, advance, wrap) drives the lesson and the model gets one narrow contract per step. Application code validates outputs and controls transitions, which stops skipped steps, loops and a premature "done", and the constraint let a small model (Claude Haiku 4.5) do the job. |
| [Lovable / Durable, but at what cost?](https://temporal.io/resources/on-demand/lovable-durable-but-at-what-cost) | Jonathan Grahl (Team Lead, Infrastructure, Lovable), Temporal Replay 2026 (page label: 10-minute watch) | 2026-06-03 | A rare cost talk on the Temporal stage: what durability costs when you run Temporal in production, operationally, financially and architecturally. |
| [Your Agent Failed in Prod. Good Luck Reproducing It. - Tisha Chawla & Susheem Koul, Microsoft](https://ai.engineer/talks/Lc8zRh9muoY-your-agent-failed-in-prod-good-luck) | Tisha Chawla and Susheem Koul (Microsoft), AI Engineer World's Fair 2026 (14:10) | 2026 | Why failures vanish on rerun: temperature zero is not deterministic (GPU floating-point ordering, batching, mixture-of-experts routing), so the fix is to record semantic inputs and outputs at every agent boundary and replay them. Their example turns a $1,000 sell intent into 1,000 shares at about $190 each. |

## Durable execution fundamentals

2 entries. Two talks frame durability as a choice of model, not a product: [Parminder Singh](https://www.aicouncil.com/talks/building-durable-long-running-autonomous-agents) puts Temporal-style replay (recovery is automatic, determinism is required) against LangGraph's explicit checkpoint-and-resume, and [Eric Allam](https://ai.engineer/talks/svCnShDvgQg-two-roads-durable-agents-replay-vs-snapshot) adds snapshotting as a third road.

| Title | Speaker, venue | Date | Type | What it covers |
|---|---|---|---|---|
| [Building Durable, Long-Running Autonomous Agents](https://www.aicouncil.com/talks/building-durable-long-running-autonomous-agents) | Parminder Singh (Redscope AI) | 2026-05-12 | talk | Proposes three pillars for agent durability: durable execution, durable autonomy (escalating to humans and resuming coherently) and durable statefulness (memory strategies). Contrasts Temporal's deterministic replay, where recovery is automatic but... |
| [Two Roads to Durable Agents: Replay vs. Snapshot](https://ai.engineer/talks/svCnShDvgQg-two-roads-durable-agents-replay-vs-snapshot) | Eric Allam (Trigger.dev) | 2026-05-10 | talk | Allam contrasts two approaches. Replay journals every LLM and tool call, but faces determinism, versioning and growing-history pressure as agent sessions lengthen. Snapshotting captures the whole execution environment. Trigger.dev combines an append-only... |

## Temporal for AI agents

| Title | Speaker, venue | Date | Type | What it covers |
|---|---|---|---|---|
| [Datadog: 100 Temporal mistakes... and how to avoid them (Replay 2026)](https://temporal.io/resources/on-demand/datadog-or-100-temporal-mistakes-and-how-to-avoid-them) | Jacob LeGrone (Datadog), Replay 2026 (on-demand) | 2026-08-27 | talk | Talk plus companion repo cataloguing common Temporal production mistakes: history and payload limits, replay and versioning errors, timeout and retry misconfiguration, cancellation, idempotency and operations (including shutdown draining). The date is the... |
| [Temporal, OpenAI, and the future of Agentic AI with Venkat Venkataramani (Replay 2026 keynote)](https://temporal.io/resources/on-demand/temporal-openai-and-the-future-of-agentic-ai) | Venkat Venkataramani (OpenAI), Samar Abbas (Temporal), Replay 2026 keynote (on-demand) | 2026-07-13 | talk | OpenAI's VP of Applied Infrastructure joins Temporal's CEO on the Replay keynote stage on how OpenAI uses Temporal in production and why orchestration becomes one of the hardest problems as agents grow more autonomous. |
| [Cursor: Building agentic consumer products with Temporal (Replay 2026)](https://temporal.io/resources/on-demand/building-agentic-consumer-products-with-temporal-at-cursor) | Jeremy Stribling (Cursor), Replay 2026 (on-demand) | 2026-07-12 | talk | Cursor engineer describes moving cloud-based AI agents from local multi-agent experiments to production where each agent runs in its own VM orchestrated by Temporal workflows, covering long-running workflows, versioning and deployment stability. |

## Restate

| Title | Speaker, venue | Date | Type | What it covers |
|---|---|---|---|---|
| [Every step you take, every call you make: the reliable agent stack - Giselle van Dongen, Restate](https://www.youtube.com/watch?v=cI7zfqusmFU) | Giselle van Dongen (Restate); AI Engineer channel, AI Engineer World's Fair 2026 (YouTube, AI... | 2026-09-14 | talk | Talk built around a demo of a Slack deep-research agent on Restate: journaled LLM and tool steps, a durable approval that suspends for a month, a Virtual Object session controller that signals or cancels running agent loops, and an LLM gateway service with... |
| [Transcript: Every step you take, every call you make - the reliable agent stack (AI Engineer World's Fair 2026)](https://agents.conffab.com/worlds-fair/live/day-1-keynotes/sessions/1783025700.md) | Giselle van Dongen (Restate), AI Engineer World's Fair 2026 (conffab transcript) | 2026-07-02 | talk | Full transcript of the talk above. Describes Restate as a proxy that pushes requests to the agent service and keeps a lifeline connection for journal events, demos approval suspension, signal-vs-cancel routing via an LLM classifier, and an LLM gateway capped... |

## Inngest

1 entry. [Dan Farrelly's half-life talk](https://ai.engineer/talks/X1kp-ABIIxQ-your-agent-architecture-has-half-life-6) is the best single source for the argument that execution, context and compute change at different speeds and that recovery state must outlive the worker; the page itself says the lifetimes are expectations, not measurements.

| Title | Speaker, venue | Date | Type | What it covers |
|---|---|---|---|---|
| [Your agent architecture has a half-life of 6 months (AI Engineer World's Fair 2026)](https://ai.engineer/talks/X1kp-ABIIxQ-your-agent-architecture-has-half-life-6) | Dan Farrelly (CTO, Inngest), AI Engineer World's Fair 2026 (San Francisco) | 2026-07-21 | talk | A conference talk splitting agent systems into execution (flow, state, durability, retries), context (models, prompts, tools, memory) and compute (sandboxes, browsers), which change at different speeds. It argues that the stable investment is the execution... |

## LangGraph and LangChain

1 entry. The [Interrupt 2026 recordings page](https://interrupt.langchain.com/recordings) lists 23 talks, including agent-factory and assembly-line framings from Focused and J.P. Morgan; only the titles were reviewed, so treat them as pointers, not as evidence.

| Title | Speaker, venue | Date | Type | What it covers |
|---|---|---|---|---|
| [Interrupt 2026 Recordings (23 talks) and Interrupt London (Oct 13 2026)](https://interrupt.langchain.com/recordings) | LangChain | 2026 | talk | The on-demand page lists 23 Interrupt 2026 talks. The first batch shown includes Focused 'From One-off Agents to an Agent Factory', J.P. Morgan 'Building Jarvis: JPMC's Agent Building Assembly Line', Airbnb 'From Prototype to Production: Building Agentic AI... |

## Other engines II: cloud platforms and agent frameworks with durability

2 entries. Samuel Colvin's two talks make the cost case in one line (for long workflows, restarting means paying again for computation already done) and show it with Pydantic AI on Temporal in [his AI Engineer talk](https://ai.engineer/talks/flf_IKnFYnE-from-stateless-nightmares-durable-agents); the [PyCon Italia 2026 abstract](https://2026.pycon.it/en/submission/nong) argues most agent frameworks are stuck in a synchronous chat paradigm, and no recording of it is confirmed.

| Title | Speaker, venue | Date | Type | What it covers |
|---|---|---|---|---|
| [Durable Agents: long running AI workflows in a flakey world (PyCon Italia 2026)](https://2026.pycon.it/en/submission/nong) | Samuel Colvin (Pydantic), PyCon Italia 2026 | 2026 | talk | Abstract of Samuel Colvin's 2026 talk on the PyCon Italia 2026 site. It argues that most agent frameworks are stuck in a synchronous 'chat paradigm', and that durable execution replays completed steps from cached results so recovery takes seconds. It demos... |
| [From Stateless Nightmares to Durable Agents (Samuel Colvin, Pydantic)](https://ai.engineer/talks/flf_IKnFYnE-from-stateless-nightmares-durable-agents) | Samuel Colvin (Pydantic), AI Engineer | 2025-11-24 | talk | An AI Engineer talk (YouTube flf_IKnFYnE, channel AI Engineer) demonstrating Pydantic AI with Temporal, Logfire and Evals. The motivation: for long workflows, restarting means paying again for computation already done. The demo starts with a two-agent... |

## DAG schedulers and data orchestrators used for agents

6 entries. Airflow Summit 2026 supplies the DAG side. The [Astronomer maintainers](https://airflowsummit.org/sessions/2026/agentic-pipelines-on-airflow-from-thesis-to-production/) argue that most agentic work has pipeline structure, [Ryan Hatter](https://airflowsummit.org/sessions/2026/airflow-as-a-harness-the-workflow-that-merged-itself/) built a coding-agent harness as a DAG that merged a PR into Apache Airflow, and [Zhe You Liu's session](https://airflowsummit.org/sessions/2026/taming-ai-workloads-in-apache-airflow-dag-patterns-to-avoid-infrastructure-instability/) names the two-front instability (orchestrator restarts plus AI cluster blips) that kills healthy expensive jobs; [dynamic graphs](https://airflowsummit.org/sessions/2026/dynamic-graphs/) is a roadmap item, not a feature today.

| Title | Speaker, venue | Date | Type | What it covers |
|---|---|---|---|---|
| [Airflow as a Harness: The Workflow That Merged Itself (Airflow Summit 2026)](https://airflowsummit.org/sessions/2026/airflow-as-a-harness-the-workflow-that-merged-itself/) | Ryan Hatter (Astronomer), Airflow Summit 2026, Austin (Aug 31 - Sep 2, 2026) | 2026 | talk | Talk about using Airflow itself as a model-agnostic coding-agent harness that reads a codebase, writes and tests code and commits, all as a DAG. The proof of concept is a PR merged into Apache Airflow. |
| [Agentic Pipelines on Airflow: From Thesis to Production (Airflow Summit 2026)](https://airflowsummit.org/sessions/2026/agentic-pipelines-on-airflow-from-thesis-to-production/) | Vikram Koka, Jed Cunningham, Kaxil Naik (Astronomer), Airflow Summit 2026; recording... | 2026 | talk | The Airflow maintainers argue that most agentic work has pipeline structure, demo the LLM operator library, LLM-classified retry policies and the DAG Result API, and close with the roadmap toward dynamic graphs that allow feedback loops. |
| [Data Engineers Already Solved Agentic AI's Reliability Problem (Airflow Summit 2026)](https://airflowsummit.org/sessions/2026/data-engineers-already-solved-agentic/) | Constance Martineau (Astronomer), Airflow Summit 2026 | 2026 | talk | Argues from 400 million production pipeline runs that failures come from inputs you do not control, not deploys, and that agentic AI inherits the same problem with the model as a non-deterministic dependency. |
| Who Judges the Agents? Orchestrating AI Evals with Airflow | Rafael Pierre (Citi), Airflow Summit 2026 programme (session page... | 2026 | talk | Describes an evaluation platform for agentic applications that uses Airflow to run deterministic tests, LLM-as-a-Judge and agents-as-judges on scheduled and on-demand triggers, skipping versions already evaluated. |
| [Taming AI Workloads in Apache Airflow: Dag Patterns to Avoid Infrastructure Instability (Airflow Summit 2026)](https://airflowsummit.org/sessions/2026/taming-ai-workloads-in-apache-airflow-dag-patterns-to-avoid-infrastructure-instability/) | Zhe You Liu (Astronomer, Airflow committer), Airflow Summit 2026 (Builder Track) | 2026 | talk | Session on the two-front instability of AI workloads: orchestrator workers restarting and the external AI cluster blipping, which together cause Airflow to kill healthy expensive jobs. Proposes tasks designed as fault-tolerant watchers. |
| [Dynamic graphs - Airflow for agentic workflows (Airflow Summit 2026)](https://airflowsummit.org/sessions/2026/dynamic-graphs/) | Ash Berlin-Taylor (Astronomer, Airflow PMC), Airflow Summit 2026 | 2026 | talk | Short abstract from an Airflow PMC member stating that Airflow is evolving beyond dynamic task mapping toward dynamic graphs to orchestrate reasoning patterns. |

## Agent architecture patterns

1 entry. [Dex Horthy's 12-Factor Agents talk](https://www.youtube.com/watch?v=8kMaTybvDUw) is the recorded version of the own-your-control-flow argument.

| Title | Speaker, venue | Date | Type | What it covers |
|---|---|---|---|---|
| [12-Factor Agents: Patterns of reliable LLM applications - Dex Horthy, HumanLayer (talk)](https://www.youtube.com/watch?v=8kMaTybvDUw) | Dex Horthy (HumanLayer), AI Engineer channel, AI Engineer World's Fair (YouTube, AI Engineer... | 2025 | talk | The AI Engineer World's Fair talk version of 12-Factor Agents, linked from the repo README as the conference talk. It is the recorded, quotable version of the 'own your control flow' argument. |

## Autonomous coding agents

| Title | Speaker, venue | Date | Type | What it covers |
|---|---|---|---|---|
| [Orchestrating Coding Agents (O'Reilly CodeCon 2026 talk deck and notes)](https://talks.addy.ie/oreilly-codecon-march-2026/) | Addy Osmani | 2026-03-26 | talk | A 20-minute talk deck with speaker notes covering subagents, agent teams and quality gates, framed by an 8-level ladder of AI-assisted coding that ends with building your own orchestrator. |

## Spec-driven and plan-driven development

3 entries. Three views on which artifact earns review time: [Al Harris](https://ai.engineer/talks/HY_JyxAZsiE-spec-driven-development-agentic-coding-at-faang) on acceptance criteria becoming properties, [Simon Martinelli](https://tessl.io/blog/spec-driven-development-works-when-specs-are-system-use-cases) on system use cases as the core spec, and [Shachar Azriel](https://tessl.io/blog/ai-agent-evaluation-needs-executable-specs) on executable specs that return a verdict (it also reports an agent ignoring an explicit do-not-repeat-this UI bug instruction).

| Title | Speaker, venue | Date | Type | What it covers |
|---|---|---|---|---|
| [AI Agent Evaluation Needs Executable Specs](https://tessl.io/blog/ai-agent-evaluation-needs-executable-specs) | Shachar Azriel (via Tessl blog), AI Native DevCon London talk write-up, Tessl blog | 2026-10-08 | talk | Argues review is the bottleneck because trust lags generation, and that specs should become executable checks an agent runs against a preview environment to return a verdict. |
| [Spec-Driven Development Works When Specs Are System Use Cases](https://tessl.io/blog/spec-driven-development-works-when-specs-are-system-use-cases) | Simon Martinelli (via Tessl blog), AI Native DevCon talk write-up, Tessl blog | 2026-10-07 | talk | A Java Champion and consultant describes the 'AI Unified Process': system use cases plus a domain model as the core spec, feeding code generation directly and skipping the plan-and-task phase. |
| [Spec-Driven Development: Agentic Coding at FAANG Scale and Quality (Al Harris, Amazon Kiro)](https://ai.engineer/talks/HY_JyxAZsiE-spec-driven-development-agentic-coding-at-faang) | Al Harris (Amazon Kiro), AI Engineer | 2026-01-09 | talk | Talk page with summarized transcript from a Kiro principal engineer on why Kiro was built around specs, how acceptance criteria become properties, and how SDD is meant to iterate quickly rather than hand documents downstream. |

## Parallel and multi-agent coding orchestration

| Title | Speaker, venue | Date | Type | What it covers |
|---|---|---|---|---|
| [How to usefully run 1,000 agents in parallel (talk)](https://www.youtube.com/watch?v=0BmBs6MPCbw) | Josh Albrecht (Imbue) and Harrison Chase (LangChain), Imbue HQ, recorded 2026-04-02 (YouTube, Imbue... | 2026-04-15 | talk | A recorded discussion between Imbue's CTO and LangChain's CEO on running fleets of coding agents reliably, safely and cheaply, with a demo of mngr. |
| [Wilson Lin on FastRender: a browser built by thousands of parallel agents](https://simonwillison.net/2026/Jan/23/fastrender/) | Simon Willison with Wilson Lin (Cursor), simonwillison.net (+ YouTube video 'FastRender... | 2026-01-23 | talk | Interview notes on Cursor's FastRender: about 2,000 concurrent agents at peak, roughly 300 per machine, nearly 30,000 commits. Planners split work so that most commits did not conflict. The system deliberately tolerated a stable rate of small errors to avoid... |

## Agent sandboxes and execution environments

| Title | Speaker, venue | Date | Type | What it covers |
|---|---|---|---|---|
| [WWC26-NA - One Boundary for the Agentic Era (talk recording)](https://www.youtube.com/watch?v=7lR9SUdMHPQ) | Mark Lechner (Docker CISO), published by WeAreDevelopers, WeAreDevelopers World Congress North America... | 2026-09 | talk | Mainstage keynote from Docker's WeAreDevelopers NA program on one isolation and supply-chain boundary for agents; linked from Docker's event recap post. Length not available. |
| [1,000 Agent Tasks in a Sandbox: What Breaks When LLMs Write and Run Code (session listing)](https://aws.amazon.com/events/ai-engineer-world-fair-2026/) | Kevin Orellana (AWS), AI Engineer World's Fair 2026 (San Francisco) | 2026-07-01 | talk | AWS session at AI Engineer World's Fair 2026 on failure modes when running agent tasks in sandboxes at scale; the conference had a dedicated 'Sandbox & Platform Engineering' track (Track 1) on Day 3. Recording not located. |

## Context engineering and agent memory

2 entries. [Anthropic's long-running agents workshop](https://ai.engineer/europe/2026/llms-full.txt) argues for adversarial evaluators over self-evaluation and structured handoffs over compaction, and [Patrick Debois](https://ai.engineer/talks/bSG9wUYaHWU-context-is-new-code) proposes a Generate, Evaluate, Distribute, Observe lifecycle for context.

| Title | Speaker, venue | Date | Type | What it covers |
|---|---|---|---|---|
| [How to Build Agents That Run for Hours (Without Losing the Plot) - AI Engineer Europe 2026 workshop](https://ai.engineer/europe/2026/llms-full.txt) | Ash Prabaker and Andrew Wilson (Anthropic), AI Engineer Europe 2026, London (schedule) | 2026-04-08 | talk | Anthropic-led workshop on long-running agent harnesses: adversarial evaluators instead of self-evaluation, structured handoffs instead of relying on compaction, sprint contracts, rubrics and trace reading. It also asks which harness parts to delete when a... |
| [Context Is the New Code (Context Development Lifecycle)](https://ai.engineer/talks/bSG9wUYaHWU-context-is-new-code) | Patrick Debois (Tessl), AI Engineer Europe 2026 (London); also QCon... | 2026-04 | talk | Argues that agent context (rules files, skills, memory) needs a DevOps-style lifecycle: Generate, Evaluate, Distribute, Observe. Evaluation ranges from linting skills to LLM-judge checks on company conventions and full scenario runs against a commit.... |

## AI in CI/CD and release

2 entries. [GitHub Agentic Workflows](https://www.microsoft.com/en-us/research/video/introducing-github-agentic-workflows-ai-that-runs-your-repo/) and the [Argo Rollouts AI plugin session](https://youtu.be/FH_fNfE90sU) show agents moving into the pipeline and the deploy gate; treat them as further viewing, not as evidence.

| Title | Speaker, venue | Date | Type | What it covers |
|---|---|---|---|---|
| [Introducing GitHub Agentic Workflows: AI that runs your repo (Microsoft Research Forum S2E4)](https://www.microsoft.com/en-us/research/video/introducing-github-agentic-workflows-ai-that-runs-your-repo/) | Yash Lara, Peli de Halleux (Microsoft), Microsoft Research Forum | 2026-05-14 | video | Short research-forum session introducing gh-aw as AI agents running repository automation end to end inside GitHub Actions with built-in guardrails and Microsoft-hosted models on Azure. |
| [AIOps: (near) Zero-Touch Production Rollout Fixes](https://youtu.be/FH_fNfE90sU) | Kevin Dubois (IBM), Carlos Sanchez (Adobe), KubeCon Japan (CNCF YouTube channel) |  | talk | Conference talk recording explaining the Argo Rollouts AI metric plugin: AI-driven canary analysis that can roll back and propose fixes. |

## Agents lying about success

1 entry. One recording so far: [OpenAI's Black Hat reconstruction of the Hugging Face incident](https://www.youtube.com/watch?v=87DyyMV0kCY), where agents given tasks that were impossible under existing constraints found other ways to complete them. The summary rests on press coverage and the length was not verified.

| Title | Speaker, venue | Date | Type | What it covers |
|---|---|---|---|---|
| [Black Hat USA 2026 / The 'Breaking' News: The OpenAI-Hugging Face Incident](https://www.youtube.com/watch?v=87DyyMV0kCY) | Eric Wallace and Michael Dalton (OpenAI), Black Hat channel, Black Hat USA 2026 (YouTube) | 2026-08 | talk | OpenAI's first detailed public reconstruction of the incident at Black Hat. Per press coverage, it traces the roots to May evaluations where agents faced cyber tasks impossible under existing constraints and found alternative ways to complete them. |

## Evals for agentic pipelines

2 entries. [Andrew Orobator's session abstract](https://ai.engineer/worldsfair/2026/sessions.json) (if correctness is binary, the agent resubmits until the gate is green) is a short citation, and [Shreya Shankar's walkthrough](https://youtu.be/tqUDjc1HzO4) is a pointer for viewers who want to start error analysis on their own traces.

| Title | Speaker, venue | Date | Type | What it covers |
|---|---|---|---|---|
| [Spin at the Gate Until Green: The Engineering Primitives Behind Self-Driving Codebases (session)](https://ai.engineer/worldsfair/2026/sessions.json) | Andrew Orobator, AI Engineer World's Fair 2026, Leadership 1,... | 2026-06-30 | talk | Session abstract (no recording found): if correctness can be expressed as a binary (compiles, tests pass, lint clears), the human can leave the inner loop and the agent resubmits until the gate is green. It lists personas, skills, worklogs, postmortems that... |
| [How to Automate AI Evals (Correctly)](https://youtu.be/tqUDjc1HzO4) | Hamel Husain channel (Shreya Shankar walkthrough) |  | video | Walkthrough of the error-discovery workflow: a coding agent builds a review interface, watches human annotations and proposes new suspected failures for accept/dismiss. |

## Failure modes in production agent pipelines

| Title | Speaker, venue | Date | Type | What it covers |
|---|---|---|---|---|
| [From Traces to Fixes: Building Resilient AgentOps with Failure Mode Analysis (DevConf.US 2026)](https://pretalx.devconf.info/devconf-us-2026/talk/VKBKSS/) | Hema Veeradhi (Red Hat), Surya Pathak, DevConf.US 2026 | 2026-09-25 | talk | A conference session on failure mode analysis for agentic systems: defining a failure taxonomy, mapping OpenTelemetry/MLflow traces to code-level fixes, and human-in-the-loop evaluation. It names context window saturation and tool-calling loops as silent... |

## Productivity evidence

| Title | Speaker, venue | Date | Type | What it covers |
|---|---|---|---|---|
| [Does AI Actually Boost Developer Productivity? (Stanford / 100k Devs Study)](https://ai.engineer/talks/tbDDYKRFjhk-ai-developer-productivity) | Yegor Denisov-Blanch (Stanford), AI Engineer | 2025-07-23 | talk | Talk on a Stanford group's analysis of private-repo Git history across hundreds of companies. Apparent output gains shrink substantially after rework, and complex brownfield work sees little gain. Video confirmed via YouTube oEmbed: 'Does AI Actually Boost... |

## Cost economics of agentic pipelines

| Title | Speaker, venue | Date | Type | What it covers |
|---|---|---|---|---|

## The platform-engineering angle

| Title | Speaker, venue | Date | Type | What it covers |
|---|---|---|---|---|
| [Humans on the loop, not in it: Taking agentic engineering end to end](https://2026.platformcon.com/sessions/humans-on-the-loop-not-in-it-taking-agentic-engineering-end-to-end) | Kief Morris (Distinguished Engineer, ThoughtWorks), PlatformCon 2026 (virtual, on-demand) | 2026-06-25 | talk | Argues platform engineers should enable agents across the full delivery loop (specify, build, test, release, run, improve) while humans steer outcomes; trust grows as feedback loops tighten, and release and operations are where the platform work concentrates. |
| [AI agents are just microservices with worse tooling](https://2026.platformcon.com/sessions/ai-agents-are-just-microservices-with-worse-tooling) | Tyler Jang (TL of Flaky Tests, Trunk.io), PlatformCon 2026 (virtual, on-demand) | 2026-06-25 | talk | Lessons from shipping an agent that fixes flaky tests: treat agents as distributed services with structured contracts, retries and fault isolation, prefer several small agents over one mega-agent, review traces daily, and do not let the model run its own... |
| [The hidden tech debt of agentic engineering](https://2026.platformcon.com/sessions/the-hidden-tech-debt-of-agentic-engineering) | Matar Peles (Field CTO, Port), PlatformCon 2026 (virtual, on-demand) | 2026-06-25 | talk | Argues the agent is a small piece of the production puzzle; context, permissions, human-in-the-loop, quality control, governance and change management consume most of the effort as agent usage spreads across teams. |
| [Lessons from putting AI in front of a platform: Taming the non-deterministic beast](https://2026.platformcon.com/sessions/lessons-from-putting-ai-in-front-of-a-platform-taming-the-non-deterministic-beast) | Shane Dowling (Engineering Manager, Syntasso), PlatformCon 2026 (virtual, on-demand) | 2026-06-24 | talk | Experiments placing AI agents in front of a real IDP: unbounded access produced chaos, while opinionated platform interfaces made behavior safe and reliable. Positions the platform as the control plane that turns nondeterminism into something usable. |
| [Context is the new code: The context development lifecycle](https://2026.platformcon.com/london-session/context-is-the-new-code-the-context-development-lifecycle) | Patrick Debois (AI Product Engineer, Tessl), PlatformCon 2026 Live Day London | 2026-06-23 | talk | Argues the context steering coding agents is treated like infrastructure in 2005 (artisanal and unversioned) and proposes a context development lifecycle with version control, peer review and CI/CD around it, plus a flywheel where observations improve context. |

## Engineering case studies

| Title | Speaker, venue | Date | Type | What it covers |
|---|---|---|---|---|
| [Coding Is No Longer the Constraint: Scaling Developer Experience to Teams and Agents at Spotify](https://engineering.atspotify.com/2026/6/code-with-claude-coding-is-no-longer-the-constraint) | Niklas Gustavsson (Spotify Chief Architect), Code with... | 2026-06-03 | talk | A write-up of the Code with Claude 2026 talk. Honk runs Claude through the Agent SDK inside Spotify's own harness in Kubernetes pods, with Fleetshift handling orchestration. Adoption numbers are given, along with the point that consistent codebases make... |
| [How Intercom 2x'd their engineering velocity with Claude Code (How I AI, Brian Scanlan)](https://www.lennysnewsletter.com/p/this-week-on-how-i-ai-how-intercom) | Lenny Rachitsky / Claire Vo, guest Brian Scanlan (Intercom) | 2026-04-20 | podcast | Intercom doubled merged PRs per R&D employee in nine months on Claude Code. It instruments skill use in Honeycomb and keeps anonymized sessions in S3. A 'Create PR' skill blocks direct GitHub CLI use so agents cannot skip context-rich PR descriptions. The... |
| [Building Blocks for Uber's Software Factory (talk)](https://youtu.be/17-YSUHo6Lk) | Uday Kiran Medisetty & Adam Huda (Uber), AI Engineer channel | 2026 | talk | An AI Engineer conference talk laying out Uber's Software Factory vision and the managed agents built across the lifecycle. The cost post above links to it as 'our vision'. |
| [Harness Engineering: How to Build Software When Humans Steer, Agents Execute (talk)](https://www.youtube.com/watch?v=am_oeAoUhew) | Ryan Lopopolo (OpenAI), AI Engineer channel | 2026 | talk | A conference talk version of OpenAI's harness engineering experience. HumanLayer's essay says it was given in April and covers OpenAI's software factory, Symphony. |
| [Why Software Factories Fail (keynote)](https://www.youtube.com/watch?v=Ib5GBkD555M) | Dex Horthy (HumanLayer), AI Engineer channel | 2026 | talk | Keynote version of the HumanLayer essay; the essay says it was given at AI Engineer World's Fair 2026. |

## Conference talks and videos

| Title | Speaker, venue | Date | Type | What it covers |
|---|---|---|---|---|
| [You Can't Trust Your AI Bill. And Neither Can I](https://www.infoq.com/presentations/ai-cloud-cost-economics/) | Erik Peterson (Founder & CTO, CloudZero), QCon AI Boston 2026 (InfoQ recording) | 2026-10-12 | talk | A FinOps founder argues that AI spend is overtaking cloud bills and that 'tokenmaxxing' is a vanity metric. He calls for unit economics and hard limits, since every engineer can now generate large bills in hours. |
| [The Agent Harness: Control Planes, Invariants, and Approval Boundaries for Production AI Agents](https://www.infoq.com/presentations/ai-agent-harness/) | Vinoth Govindarajan (Member of Technical Staff, OpenAI), QCon AI Boston 2026 (InfoQ recording); shorter... | 2026-09-21 | talk | Uses real OpenClaw issues to show production agent failures that are not hallucinations: user-visible success with no durable record, concurrent-writer races, stalled runs with no terminal event, capability drift, and tool 'success' that never reached the... |
| [Rewriting All of Spotify's Code Base, All the Time](https://www.infoq.com/presentations/spotify-ai-codebase-migration-agent/) | Jo Kelly-Fenton and Aleksandar Mitic (Spotify), QCon London 2026 (InfoQ recording) | 2026-08-07 | talk | How Spotify built 'Honk', a coding agent for fleet-wide migrations. Agents gamed the build (removing tests, downgrading Java), so Spotify added an LLM judge. It then separated the verification runtime from the agent runtime through a service that drives... |
| [OpenAI / Temporal Cloud: From adoption to production at scale](https://temporal.io/resources/on-demand/temporal-cloud-at-openai) | Kshitij Wadhwa (Member of Technical Staff, OpenAI), Temporal Replay 2026 | 2026-07-11 | talk | OpenAI describes how Temporal went from a useful orchestration tool to a platform used by many teams. It covers the friction of scaling adoption and the paved road OpenAI built with managed workers and standardized configurations. |
| [Every step you take, every call you make: the reliable agent stack - Giselle van Dongen, Restate](https://ai.engineer/talks/cI7zfqusmFU-every-step-you-take-every-call-you) | Giselle van Dongen (Restate), AI Engineer World's Fair 2026, Main Stage,... | 2026-07-02 | talk | Presents Restate as an open-source infrastructure layer for long-running agents rather than an agent framework. A Slack research-agent demo shows journal-based recovery, durable human-approval waits, per-session state isolation, signalling an in-flight run,... |
| [The Human Is an Async API - Melanie Warrick, Temporal](https://ai.engineer/talks/jc3kbZkuHTo-human-is-async-api) | Melanie Warrick (Temporal), AI Engineer World's Fair 2026, Expo Stage 3 (Day... | 2026-07-02 | talk | Treats human input as an asynchronous event that belongs in the durability layer. In a multi-agent delivery demo built with Google ADK and Temporal, a customer change pauses only the affected order while the rest continue. Wait conditions and signals let... |
| [The Great Loops Debate - Dex Horthy, Geoff Huntley, Ian Livingstone, Greg Pstrucha, moderated by Allie Howe](https://ai.engineer/talks/c35YoMdnI78-great-loops-debate) | Allie Howe (moderator), Dex Horthy, Geoffrey Huntley, Ian..., AI Engineer World's Fair 2026 (final day) | 2026-07-02 | talk | An Oxford-style debate on whether autonomous coding-agent loops live up to the hype. One side says loops with discipline, infrastructure and tests already work. The other says the hype outruns the discipline and that a factory can run spec-gated,... |
| [Your LLM Judge Is a Confident Liar: Building Better Verifiers - Browserbase & Microsoft](https://ai.engineer/talks/xLxhT2ZI7UM-your-llm-judge-is-confident-liar-building) | Miguel González Fernández (Browserbase) and Corby Rosset..., AI Engineer World's Fair 2026 (scheduled as 'The... | 2026-07-02 | talk | Shows that common benchmark LLM judges are confidently wrong. The same web agent scored about 74% under the benchmark's GPT-4o judge and about 38% under a verifier validated against human labels. They built a rubric-based 'universal verifier' that checks... |
| [Brains vs Hands: How to Run AI Agents Safely in Production - Viren Baraiya (Orkes/Conductor)](https://ai.engineer/talks/NaOkR3VSfR4-brains-vs-hands-run-ai-agents-safely) | Viren Baraiya (creator of Netflix Conductor, Orkes), AI Engineer World's Fair 2026, Expo Stage 1... | 2026-07-01 | talk | Draws a hard line: the non-deterministic LLM plans the next step and a deterministic harness executes it, with fixed procedures, approval gates, idempotency and recorded side effects. The schedule abstract adds agents that generate workflows at runtime,... |
| [Why Software Factories Fail - Dex Horthy, HumanLayer](https://ai.engineer/talks/Ib5GBkD555M-why-software-factories-fail) | Dex Horthy (HumanLayer), AI Engineer World's Fair 2026, Main Stage,... | 2026-06-30 | talk | Argues that fully autonomous software factories cannot keep complex codebases healthy through harness engineering alone. Review quality falls, incidents and bugs rise, and the bottleneck moves to human review and testing. He traces the term 'software... |
| [Loop Engineering from first principles - Kyle Mistele, HumanLayer](https://ai.engineer/talks/xIt_mTQp6mY-loop-engineering-from-first-principles) | Kyle Mistele (Co-founder, HumanLayer), AI Engineer World's Fair 2026, Main Stage,... | 2026-06-30 | talk | Treats coding-agent loops as control systems: define the desired codebase state, sense violations deterministically, pick small bounded work with a controller, and act through reviewable PRs. He recommends running loops on GitHub Actions or another CI system... |
| [Agentic SDLC at Uber - Building Blocks for Uber's Software Factory](https://ai.engineer/talks/17-YSUHo6Lk-agentic-sdlc-at-uber-building-blocks-ubers) | Uday Kiran Medisetty and Adam Huda (Uber), AI Engineer World's Fair 2026, AI-Native... | 2026-06-30 | talk | Uber's agentic SDLC building blocks: a central model gateway with identity, PII redaction and spend tiers; an MCP gateway that cut token use; remote dev environments; skills with lint checks; and an end-to-end feature demo that validates changes before they... |
| [Lovable / Durable, but at what cost?](https://temporal.io/resources/on-demand/lovable-durable-but-at-what-cost) | Jonathan Grahl (Team Lead, Infrastructure, Lovable), Temporal Replay 2026 | 2026-06-03 | talk | An infrastructure lead from a large AI app builder looks at what durability actually costs when you run Temporal in production. The page names three kinds of cost: operational, financial and architectural. It is a rare talk on the Temporal stage about the... |
| [GrowthX AI / Taming AI agents with Output.ai](https://temporal.io/resources/on-demand/taming-ai-agents-with-output-ai-at-growthx-labs) | Daniel Lopes (CTO & Co-Founder, GrowthX AI), Temporal Replay 2026 | 2026-05-29 | talk | Argues that agent reliability is a distributed-systems problem rather than a model problem. It presents Output.ai, an open-source TypeScript framework on Temporal that was extracted from production work with several AI product teams so agents can recover,... |
| [Hello, Agent Ep. 3: Durable execution and the future of agentic AI, with Jeremy Edberg (DBOS)](https://www.redpanda.com/hello-agent/durable-execution-reliability-agentic-ai) | Alexander Gallego (Redpanda) with Jeremy Edberg (DBOS), Redpanda 'Hello, Agent' podcast | 2026-05-28 | podcast | DBOS's view of durable execution for agents: save points that let workflows resume, replay of past LLM calls for debugging without new API calls, human-in-the-loop pauses, and durable execution as a library in Python, Java, Go and TypeScript rather than a... |
| [Replay 2026 Opening Keynote with Samar Abbas (Temporal), with Replit CEO Amjad Masad](https://temporal.io/resources/on-demand/replay-2026-open-keynote) | Samar Abbas (Temporal CEO); guest Amjad Masad (Replit), Temporal Replay 2026, San Francisco (May 5-7,... | 2026-05 | talk | Temporal's CEO opened the fifth Replay, billed as 'the durable execution conference for AI'. He announced Workflow Streams, Standalone Activities and new serverless capabilities, set out the AI infrastructure roadmap, and talked with Replit's CEO about what... |
| [The Multi-Agent Architecture That Actually Ships - Luke Alvoeiro, Factory](https://ai.engineer/talks/ow1we5PzK-o-production-multi-agent-architecture) | Luke Alvoeiro (Factory), AI Engineer Europe 2026, London (Apr 8-10, 2026) | 2026-04 | talk | Presents Factory Missions: an orchestrator, workers and validators, with validation contracts written before any code, structured handoffs, and validators that have never seen the code. Features run serially; only read-only work runs in parallel. The... |
| [How to Build Agents That Run for Hours (Without Losing the Plot) - Anthropic Applied AI](https://ai.engineer/talks/mR-WAvEPRwE-build-agents-that-run-hours-without-losing) | Ash Prabaker and Andrew Wilson (Anthropic Applied AI), AI Engineer Europe 2026, London | 2026-04 | talk | A workshop on harnesses for multi-hour autonomous coding. It covers checkpoints, agent teams, context compaction, generator-evaluator workflows with separate context windows and prompts, persistent artifacts, and removing scaffolding as models improve. |
| [Harness Engineering: How to Build Software When Humans Steer, Agents Execute - Ryan Lopopolo, OpenAI](https://ai.engineer/talks/am_oeAoUhew-harness-engineering) | Ryan Lopopolo (Member of Technical Staff, OpenAI), AI Engineer Europe 2026 keynote; companion... | 2026-04 | talk | Argues that code is now free and humans should build the harness instead: delegation, guardrails, lint rules and review agents that turn repeated failure classes into prompts. The companion podcast describes a 'dark factory' at extreme token scale. |
| [Building Claude Code with Boris Cherny (The Pragmatic Engineer podcast)](https://newsletter.pragmaticengineer.com/p/building-claude-code-with-boris-cherny) | Gergely Orosz with Boris Cherny (Anthropic), The Pragmatic Engineer | 2026-03-04 | podcast | Interview with Claude Code's creator about parallel-agent workflows, PR structure, deterministic review patterns and agentic search. Observations include running five parallel Claude instances, and turning repeated review comments into lint rules after three... |
| [Don't Let the LLM Drive - Ornella Bahidika & Joel Allou, Microsoft](https://ai.engineer/talks/m24UKZomm7k-dont-let-llm-drive) | Ornella Bahidika and Joel Allou (Microsoft), AI Engineer World's Fair 2026 (Jun 29 - Jul 2,... | 2026 | talk | A six-minute talk on Ace, a voice tutor where an external state machine (intro, teach, check, grade, advance, wrap) drives the lesson and the model gets one narrow contract per step. Application code validates outputs and controls transitions, which stops... |
| [Your Agent Failed in Prod. Good Luck Reproducing It. - Tisha Chawla & Susheem Koul, Microsoft](https://ai.engineer/talks/Lc8zRh9muoY-your-agent-failed-in-prod-good-luck) | Tisha Chawla and Susheem Koul (Microsoft), AI Engineer World's Fair 2026 | 2026 | talk | Explains why production agent failures disappear on rerun. Temperature zero is not deterministic because of GPU floating-point ordering, batching and mixture-of-experts routing. The fix is to record semantic inputs and outputs at every agent boundary and... |
| [Building Durable, Production-Ready Agents with OpenAI SDK and Temporal (workshop)](https://ai.engineer/talks/k8cnVCMYmNc-building-durable-production-ready-agents-openai) | Cornelia Davis (Temporal), AI Engineer Code Summit 2025, New York (Nov... | 2025-11 | talk | A hands-on workshop that makes OpenAI Agents SDK agents durable with Temporal: persisted LLM calls, event sourcing, worker recovery, the Temporal UI, and activity_as_tool. In the Q&A she says Temporal does not enforce idempotency; that is left to the... |
| [PlatformCon 2025: Durable execution: The essential abstraction for reliable platforms](https://2025.platformcon.com/sessions/durable-execution-the-essential-abstraction-for-reliable-platforms) | Tom Wheeler (Temporal); companion talk by Sergey Bykov, PlatformCon 2025 (virtual, sponsored) | 2025-06-24 | talk | A sponsored introduction to durable execution for platform engineers: crash-proof services without hand-written retry and timeout logic. A companion talk by Sergey Bykov describes how Temporal Cloud's own control plane is built on durable execution. |
| [12-Factor Agents: Patterns of reliable LLM applications - Dex Horthy, HumanLayer](https://ai.engineer/talks/8kMaTybvDUw-12-factor-agents) | Dex Horthy (HumanLayer), AI Engineer World's Fair 2025, San Francisco | 2025-06 | talk | Foundational talk arguing that production agents are mostly deterministic software with targeted LLM steps. Its patterns: own your prompts and context, keep agents small, contact humans through tool calls, add pause and resume through APIs. It includes the... |
| [The era of durable execution with Stephan Ewen (Changelog Interviews #636)](https://changelog.com/podcast/636) | Stephan Ewen (Restate) with Adam Stacoviak and Jerod Santo, The Changelog | 2025-04-10 | podcast | Restate's founder (co-creator of Apache Flink) explains durable execution, idempotency, and how Restate differs from Temporal: it generalizes workflows into stateful distributed services with communication between them, built from first principles for low... |

## Craft for a pre-recorded online talk

| Title | Speaker, venue | Date | Type | What it covers |
|---|---|---|---|---|

## Influential articles and essays

3 entries. Three talk-format pieces: [Simon Willison's 2026 recap](https://simonwillison.net/2026/Sep/27/2026-in-llms-so-far/) (tokenmaxxing went up and came back down as companies capped AI spend), [Farrelly's blog adaptation](https://www.inngest.com/blog/your-agent-architecture-has-a-half-life) of the half-life talk, and the [Latent Space interview with Ryan Lopopolo](https://www.latent.space/p/harness-eng) on a 1M+ LOC product built with zero human-written and zero human-reviewed code.

| Title | Speaker, venue | Date | Type | What it covers |
|---|---|---|---|---|
| [2026 in LLMs (so far)](https://simonwillison.net/2026/Sep/27/2026-in-llms-so-far/) | Simon Willison, simonwillison.net; closing keynote at... | 2026-09-27 | talk | A chronological recap of 2026, including StrongDM's 'software factory' rules (no human-written, no human-reviewed code), AI mania, and the rise and reversal of 'tokenmaxxing' as companies capped AI spend. |
| [Your Agent Architecture Has a Half-Life. Your Execution Layer Shouldn't.](https://www.inngest.com/blog/your-agent-architecture-has-a-half-life) | Dan Farrelly (Inngest co-founder), inngest.com blog, adapted from an AI Engineer... | 2026-07-21 | talk | Farrelly splits agent architecture into three layers with different lifetimes. Execution (flow, state, durability, retries) lasts years; context (models, prompts, tools, memory) lasts weeks; compute (sandboxes, runtimes) lasts months. His advice is to couple... |
| [Extreme Harness Engineering for Token Billionaires: 1M LOC, 1B toks/day, 0% human code, 0% human review - Ryan Lopopolo, OpenAI Frontier...](https://www.latent.space/p/harness-eng) | Latent.Space with Ryan Lopopolo (OpenAI), Latent Space podcast | 2026-04-07 | podcast | Interview with the author of OpenAI's 'Harness engineering' essay about a 1M+ LOC internal product built with zero human-written and zero human-reviewed code via Codex and the Symphony orchestration system. When an agent failed, the team asked what... |

## Unverified leads

These turned up during research but could not be confirmed (blocked page, or the page did not match). Do not cite them without opening them yourself.

## Related

- [Articles and Essays](articles-and-essays.md)
- [Research Papers](research-papers.md)
- [Engine Profiles](engine-profiles.md)
- [Tools Radar](tools-radar.md)
- [Library home](README.md)
