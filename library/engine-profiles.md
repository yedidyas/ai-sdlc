# Engine Profiles

One profile per orchestration or durable-execution engine that matters for hours-long agent pipelines: what it is, what it journals, what it forbids, what it costs and where it hurts. Part 1 covers Temporal through the newer and minimal engines, part 2 covers cloud platforms, agent frameworks and DAG schedulers; facts are as of October 2026 (research cut-off 2026-10-12) and versions carry their release dates.

## Index of engines (both parts)

Every row links to its profile below: the first twelve are part 1, the rest are part 2 (cloud platforms, agent frameworks and DAG schedulers), which begins at the heading "Part 2: cloud platforms, agent frameworks and DAG schedulers". Verdicts: Grab (use it for the job described), Consider (narrower fit), Watch (not yet adoptable), Skip (wrong tool for this job).

| Engine | Category | One-line verdict |
|---|---|---|
| [Temporal](#temporal) | Replay engine, server or Cloud | Grab for hours-long multi-step outer loops if you can pay for Cloud or staff the on-call; budget for replay discipline and versioning. |
| [Restate](#restate) | Journal runtime, single binary | Grab when durable steps sit inside a tight agent loop or you want version pinning by default; BSL server, smaller ecosystem. |
| [Inngest](#inngest) | Step functions memoized by step ID | Grab for lean TypeScript teams that want flow control as config; renaming a step re-runs it. |
| [LangGraph](#langgraph) | Agent runtime library plus LangSmith Deployment | Grab for chat and approval agents with sub-hour steps; skip as the sole outer loop for hours-long side-effecting steps. |
| [DBOS](#dbos) | Library over Postgres, optional Conductor | Grab when ops will not run another stateful service; plan for the deploy and versioning trap. |
| [Hatchet](#hatchet) | Postgres task queue plus durable tasks | Grab when a queue with some durable tasks is enough. |
| [Trigger.dev](#triggerdev) | Managed TypeScript tasks, process snapshots | Grab for TypeScript teams wanting managed long tasks; `run()` reruns from the top on retry. |
| [Vercel Workflow](#vercel-workflow) | Directive library over an event log | Grab on Vercel and Next.js; 240 s replay cap. |
| [Cloudflare Workflows and Agents SDK](#cloudflare-workflows-and-agents-sdk) | Serverless step engine plus actor agents | Grab for many cheap waiting agents on Workers; 1 MiB step results shape the design. |
| [Golem](#golem) | WebAssembly runtime with an operation log | Watch; 1.6 is announced for 2026-10-19, not released. |
| [Resonate](#resonate) | Durable promises, one server on Postgres | Watch; elegant model, small ecosystem. |
| [Newer and minimal engines](#newer-and-minimal-engines) | Trigora, LittleHorse, Absurd, pg_durable, Obelisk, Sayiir, Pi Durable | Watch most; Absurd to own every line; LittleHorse for business governance; pg_durable not for agent pipelines. |
| [AWS Step Functions](#aws-step-functions) | Managed state machine | Grab for AWS-native orchestration; 256 KiB payload cap ([quotas](https://docs.aws.amazon.com/step-functions/latest/dg/service-quotas.html)). |
| [AWS Lambda durable functions](#aws-lambda-durable-functions) | Checkpoint and replay in Lambda | Grab for durability on AWS without a cluster ([docs](https://docs.aws.amazon.com/lambda/latest/dg/durable-functions.html)). |
| [Amazon Bedrock AgentCore](#amazon-bedrock-agentcore) | Managed agent hosting | Hosting, not run memory ([sessions](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/runtime-sessions.html)). |
| [Azure Durable Task Scheduler and Microsoft Agent Framework](#azure-durable-task-scheduler-and-microsoft-agent-framework) | Managed replay backend plus framework | Grab on Azure ([docs](https://learn.microsoft.com/en-us/azure/durable-task/scheduler/durable-task-scheduler)). |
| [Dapr Workflows, Dapr Agents and Diagrid Catalyst](#dapr-workflows-dapr-agents-and-diagrid-catalyst) | Sidecar replay engine | Grab if already Dapr-native ([docs](https://docs.dapr.io/developing-applications/building-blocks/workflow/workflow-features-concepts/)). |
| [Orkes Conductor](#orkes-conductor) | JSON workflow engine compiling agents | Consider for guardrails outside the prompt ([Orkes](https://orkes.io/blog/agents-on-conductor-architecture-for-production-ai)). |
| [Mastra](#mastra) | TypeScript agent framework | Grab with Inngest underneath for real durability ([docs](https://mastra.ai/docs/workflows/suspend-and-resume)). |
| [CrewAI Flows](#crewai-flows) | Multi-agent framework with `@persist` | Prototypes; put an engine under long runs ([docs](https://docs.crewai.com/v1.15.27/en/concepts/flows)). |
| [OpenAI Agents SDK](#openai-agents-sdk) | Agent SDK with sessions | Good API; sessions are not execution durability ([docs](https://openai.github.io/openai-agents-python/)). |
| [Google ADK](#google-adk) | Agent framework with resumability | Fine for Gemini shops; resume is caller-triggered ([ADK 2.0](https://adk.dev/2.0/)). |
| [Pydantic AI](#pydantic-ai) | Agent framework, eight durable backends | Grab to keep the engine swappable ([docs](https://pydantic.dev/docs/ai/capabilities/durable_execution/overview/)). |
| [Apache Airflow 3](#apache-airflow-3) | DAG scheduler | Grab for batch and scheduled evals, not cyclic agent loops ([Airflow](https://airflow.apache.org/)). |
| [Prefect](#prefect) | Python scheduler | A scheduler, not a durable engine ([Prefect](https://www.prefect.io/)). |
| [Dagster](#dagster) | Asset-centric orchestrator | Grab for data assets around agents; skip as a runtime ([Dagster](https://dagster.io/)). |
| [Kestra](#kestra) | Declarative YAML orchestrator | Grab to expose audited workflows as agent tools ([Kestra](https://kestra.io/)). |
| [Windmill](#windmill) | Scripts and flows platform | Grab for internal automations with cheap waits ([Windmill](https://www.windmill.dev/)). |
| [Flyte 2](#flyte-2) | Python durable AI runtime | Consider for GPU-heavy agent fleets ([Flyte](https://flyte.org/)). |
| [Metaflow](#metaflow) | ML framework, resumable steps | Grab for ML-team fleets needing artifact versioning ([Metaflow](https://metaflow.org/)). |
| [Argo Workflows](#argo-workflows) | Kubernetes-native engine | A substrate, not an agent orchestrator ([Argo](https://argoproj.github.io/workflows/)). |

## Temporal

*Replay-based durable execution server (workflows plus activities), self-hosted or on Temporal Cloud. Verdict: Grab for hours-long, multi-step outer loops if you can pay for Cloud or staff the on-call.*

**What it is.** An open-source durable execution platform: workflow code orchestrates, Activities perform side effects, and a Worker rebuilds a workflow's state by re-running its code against the recorded Event History, so replayed code must emit the same Commands in the same order or fail with a non-deterministic error ([Workflow Definition](https://docs.temporal.io/workflow-definition)). Temporal frames an agent as "Deterministic in execution" but "NOT predetermined", because model decisions are recorded as Activity results ([Of course you can build dynamic AI agents with Temporal](https://temporal.io/blog/of-course-you-can-build-dynamic-ai-agents-with-temporal)). It is the momentum incumbent: a $550M Series E at $12.55B on 2026-09-14, 1.9 trillion billable actions in August 2026 and 4,300+ paying customers, all vendor-reported ([Temporal raises $550M](https://temporal.io/blog/temporal-raises-usd550m-series-e-at-usd12-55b-valuation-ai)).

| Field | Detail |
|---|---|
| Durability model | History replay. Activities run outside the replay path and are retried by the server; an Activity is "observed as completed exactly once" but may execute several times and even partially complete ([Activity Definition](https://docs.temporal.io/activity-definition)). |
| Journaled, replayed, checkpointed | Journaled: Commands and results, including "Every Activity input and output" ([External Storage](https://docs.temporal.io/external-storage)). Replayed: workflow code from the first event. Checkpointed: only heartbeat details, which the next attempt can read ([Detecting activity failures](https://docs.temporal.io/encyclopedia/detecting-activity-failures)). |
| Determinism rules | Same Commands in the same order. Adding, removing or reordering timers, Activities, child workflows or signals needs versioning. The Python sandbox "is not completely isolated" ([Python SDK sandbox](https://docs.temporal.io/develop/python/best-practices/python-sdk-sandbox)). |
| Limits | History 51,200 events or 50 MB (warnings at 10,240 events or 10 MB), after which the execution is terminated, not slowed ([Self-hosted defaults](https://docs.temporal.io/self-hosted-guide/defaults)); 2 MB per payload and 4 MB per transaction, fixed on Cloud; 2,000 pending Activities per execution ([Cloud limits](https://docs.temporal.io/evaluate/cloud/limits)). Start-To-Close has no default; the OpenAI plugin defaults model Activities to 60 s ([OpenAI Agents SDK integration](https://docs.temporal.io/develop/python/integrations/openai-agents)). |
| HITL primitives | Signals, Updates (validated before entering history) and Queries ([message passing](https://docs.temporal.io/encyclopedia/workflow-message-passing)); a `wait_condition` "works identically whether the wait is five seconds or five months" ([approval guide](https://docs.temporal.io/guides/reliable-document-approvals.md)). Without a timeout or escalation path approvals "stall or are silently rejected" ([Approval Pattern](https://docs.temporal.io/design-patterns/approval.md)). |
| Versioning and patching | `patched()` and `deprecate_patch()`, or Worker Versioning (GA 2026-03-30, PINNED or AUTO_UPGRADE; Upgrade on Continue-as-New in Public Preview) ([Worker Versioning GA](https://temporal.io/blog/ga-worker-versioning-public-preview-upgrade-on-continue-as-new)); the Kubernetes Worker Controller went GA on 2026-05-04 ([changelog](https://temporal.io/changelog)). Python SDK 1.34.0 moved ADK ids onto the deterministic random stream and warns older workflows "may not replay deterministically across the upgrade" ([CHANGELOG](https://raw.githubusercontent.com/temporalio/sdk-python/HEAD/CHANGELOG.md)). |
| Observability and UI | Web UI and Event History; the Activity `summary` option labels one generic LLM Activity ([Label your agent steps](https://temporal.io/blog/label-your-agent-steps)). |
| Hosting and ops | MIT server (four services plus a database) or Cloud. One benchmark counted 37 tables and about 145 SQL statements for a 3-activity workflow on Postgres ([Under the Hood](https://backend.how/posts/temporal-under-the-hood/)); an HN commenter says self-hosting needs "a minimum of 2-3 engineers to handle on-call" (anecdote) ([HN](https://hn.algolia.com/api/v1/items/49696335)). |
| Pricing | Cloud Actions: $50 per million at the start, falling to $25 for the 100M to 200M band on Business and above; Active storage $0.042 per GB-hour ([pricing](https://docs.temporal.io/cloud/pricing)). Paygo has no base fee and Developer Support adds 10%: 100,000 Actions cost $5 plus $0.50 ([Paygo](https://temporal.io/blog/paygo-developer-support)). Heartbeats bill only if they reach the server ([Actions](https://docs.temporal.io/evaluate/cloud/actions)). Derived: $0.05 per 1,000. |
| SDKs, latest release | Python 1.34.0 (2026-09-30), Go v1.49.0 (2026-09-14), TypeScript 1.24.0 (2026-09-15), Java, .NET and Rust (GA 2026-09-04) ([changelog](https://temporal.io/changelog)); server v1.32.1 (2026-10-07, Go module proxy). |
| AI and agent integrations | OpenAI Agents SDK plugin GA since 2026-03-23; LangGraph, Strands, Google GenAI, LangSmith and MCP in Public Preview; Google ADK and deepagents pre-release ([ai-integrations](https://github.com/temporalio/ai-integrations)). No Claude Agent SDK plugin, only an Anthropic recipe with `AsyncAnthropic(max_retries=0)` so Temporal owns retries ([cookbook](https://docs.temporal.io/ai/cookbook/agentic-loop-tool-call-claude-python)). Agent Harness 0.6.0 is experimental ([harness](https://temporal.io/blog/temporal-agent-harness-durable-agent-infrastructure)). |

**Great at**
- Per-Activity retries, timeouts and heartbeats ([Detecting activity failures](https://docs.temporal.io/encyclopedia/detecting-activity-failures)), and testing: a time-skipping server plus a recipe to replay recent histories in CI and "Fail CI if any error is encountered during replay" ([Testing - Python SDK](https://docs.temporal.io/develop/python/best-practices/testing-suite)).
- A published reference design for coding agents: a session is a Workflow, model and tool calls are Activities, an interrupted tool call is recorded as "outcome unknown" instead of re-run ([The immortal life of Pi](https://temporal.io/blog/the-immortal-life-of-pi-running-the-pi-coding-agent-on-temporal)).

**Painful at**
- Replay discipline meets deploys: "Those LLM calls you already paid for? Gone." ([Safe deployments](https://temporal.io/blog/safe-deployments-with-temporal-worker-versioning-on-kubernetes)). The Python Worker's `graceful_shutdown_timeout` defaults to zero ([worker source](https://raw.githubusercontent.com/temporalio/sdk-python/HEAD/temporalio/worker/_worker.py)); Datadog's mistakes list includes not draining Activities before shutdown ([100 Temporal mistakes](https://temporal.io/resources/on-demand/datadog-or-100-temporal-mistakes-and-how-to-avoid-them)).
- The history ceiling terminates a run, payloads stop at 2 MB, and External Storage (the claim-check fix) is still Public Preview ([Replay 2026 announcements](https://temporal.io/blog/replay-2026-product-announcements)).

**Known criticisms**
- HN: "We eventually migrated everything off of it, and everyone was happier", and fit only if "it is acceptable to add 100+ms latency" per call (anecdotes; no independent per-Activity benchmark was found) ([HN](https://hn.algolia.com/api/v1/items/49696335)).
- Rivals (vendor): Restate says Replit moved its agent from Temporal to Restate ([case study](https://restate.dev/use-cases/replit)), and DBOS claims Temporal "adds tens to hundreds of milliseconds per step" ([comparison](https://dbos.dev/compare/dbos-vs-temporal)). Counter-example: Monk moved 100+ workflows from Inngest to Temporal ([How Monk migrated](https://temporal.io/blog/how-monk-migrated-100-workflows-inngest-to-temporal)).

**Links.** [Docs](https://docs.temporal.io/) · [Cloud pricing](https://docs.temporal.io/cloud/pricing) · [Changelog](https://temporal.io/changelog)

## Restate

*Journal-based durable execution runtime with its own replicated log, shipped as a single binary. Verdict: Grab when durable steps sit inside a tight agent loop, or when you want version pinning by default.*

**What it is.** A Rust runtime that sits in front of your handlers, pushes requests to them and journals each durable step (`ctx.run`, sleeps, RPCs, state, awakeables) in a replicated log with RocksDB indexes and snapshots to object storage ([Building a modern durable execution engine](https://restate.dev/blog/building-a-modern-durable-execution-engine-from-first-principles)). Its 2026 thesis is durability inside the agent loop rather than around it, at a few milliseconds per step (vendor) ([Series A post](https://restate.dev/blog/announcing-series-a)). It raised a $20M Series A on 2026-09-30 and was founded in Berlin in 2022 ([TechCrunch](https://techcrunch.com/2026/09/30/restate-lands-20m-as-the-need-for-durable-infrastructure-increases-with-ai-agents/)).

| Field | Detail |
|---|---|
| Durability model | Journal per invocation: the handler is replayed from the start and journaled steps are skipped ([Restate's Single Binary vs Temporal's Cluster](https://dev.to/andriiboyko/restates-single-binary-vs-temporals-cluster-when-the-lighter-engine-wins-3635), with corrections by Temporal's co-founder). |
| Journaled, replayed, checkpointed | Journaled: step results, timers, RPCs, state, generated ids and timestamps; the Vercel AI `durableCalls` middleware saves every LLM response but "does not auto-journal tool calls" ([Durable Agents](https://docs.restate.dev/ai/patterns/durable-agents)). Replayed: handler code. Snapshotted: server state. |
| Determinism rules | Code outside `ctx.run` must be deterministic; a replay mismatch raises RT0016; `ctx.uuid()`, `ctx.random()` and `ctx.time()` are replay-stable ([Error codes](https://docs.restate.dev/references/errors), [Durable Steps](https://docs.restate.dev/develop/python/durable-steps)). For agent SDKs: no streaming inside `ctx.run`, no SDK-native parallel tool calls, re-raise suspension errors ([integration guide](https://docs.restate.dev/ai/sdk-integrations/integration-guide)). |
| Limits | Ingress requests over 32 MiB by default get a 413 ([v1.7.0 notes](https://github.com/restatedev/restate/blob/main/release-notes/v1.7.0.md)). Default retry policy: factor 2.0, max interval 60 s, 70 attempts, then pause; a handler silent for 1 minute is asked to suspend and is aborted after 10; retention defaults to 24 h ([Service Configuration](https://docs.restate.dev/services/configuration)). |
| HITL primitives | Awakeables (one-shot, ID-addressed, "similar to a task token"), signals that "can arrive before or after the invocation starts waiting", and workflow promises; waiting suspends the invocation ([external events](https://docs.restate.dev/develop/ts/external-events.md)). |
| Versioning and patching | Immutable deployments, with each execution pinned to the deployment it started on; the Kubernetes operator drains old revisions. "Avoid long-running handlers (days or months) - otherwise you need to keep old deployments around" ([Versioning](https://docs.restate.dev/services/versioning)). 1.6 added pause, resume, restart from a journal prefix and moving invocations between deployments ([Restate 1.6](https://restate.dev/blog/announcing-restate-1-6)). |
| Observability and UI | 1.7 added per-attempt spans (start, one per attempt, end) and a redesigned UI ([Restate 1.7](https://restate.dev/blog/announcing-restate-1-7)); Langfuse integration with version-pinned traces ([Restate and Langfuse](https://restate.dev/blog/langfuse-restate-announcement)). |
| Hosting and ops | Server under BSL 1.1: internal platforms and production are allowed, a public Restate platform service is not, each version converts to Apache 2.0 after four years; SDKs are MIT ([LICENSE](https://github.com/restatedev/restate/blob/main/LICENSE)). HA means several copies of one binary plus a bucket, and minor versions must not be skipped ([Upgrading Restate](https://docs.restate.dev/server/upgrading)). Restate Cloud has SOC 2 Type I ([Restate Cloud](https://restate.dev/blog/restate-cloud-soc2-enterprise-sso-and-hipaa)); BYOC deploys into your AWS or GCP account ([BYOC](https://restate.dev/blog/announcing-restate-byoc)). |
| Pricing | Free 100k actions a month; Starter $75 a month with 5M then $25 per million; Business $300 with 20M; Premium $1,000 with 50M then $15 and $10; a payload-carrying action counts once per started 64 KiB ([Restate Pricing](https://restate.dev/pricing)). BYOC example (vendor): about $6-7k a month at 500 actions per second against more than $32k for Temporal ([BYOC](https://restate.dev/blog/announcing-restate-byoc)). Derived: $0.025 per 1,000 actions. |
| SDKs, latest release | TypeScript 1.17.3 (2026-10-09), Python 1.0.5 (2026-09-02; 1.0 on 2026-06-23), Go v1.1.0 (2026-09-22), Java/Kotlin 2.9.4, Rust 0.12.1 ([Python changelog](https://docs.restate.dev/changelog/python-sdk), [Go changelog](https://docs.restate.dev/changelog/go-sdk)). Server 1.7.13 (2026-10-01), 1.8.0-rc.2 on Docker Hub (2026-10-08) ([Server changelog](https://docs.restate.dev/changelog/server)). |
| AI and agent integrations | Vercel AI SDK middleware (0.4.0, 2026-07-22), OpenAI Agents SDK `DurableRunner`, Pydantic AI `RestateAgent`, Google ADK `RestatePlugin`, a LangChain middleware that "retries indefinitely with exponential backoff" by default ([Retries and error handling](https://docs.restate.dev/ai/patterns/error-handling)). No first-party Claude Agent SDK integration was found; wrap calls in `ctx.run`. |

**Great at**
- Durability per model or tool call; Replit's spike of "close to 25,000 durable actions per second per cell" comes from a vendor case study ([Replit case study](https://restate.dev/use-cases/replit)).
- Version pinning by default, so a prompt or tool-schema change cannot silently corrupt an in-flight run ([Updating AI Agents safely](https://restate.dev/blog/dealing-with-versioning-in-long-running-agents)); operator controls that restart from a journal prefix and keep expensive completed steps ([A remote control for your agents](https://restate.dev/blog/a-remote-control-for-your-agents)).

**Painful at**
- Long steps: the docs' tuning example is an LLM call of "up to 3 minutes", so (an inference) a 30 to 90 minute step would need both timers raised ([Durable Steps](https://docs.restate.dev/develop/python/durable-steps)). Retry layers multiply: Vercel AI `maxRetries` 2 times Restate `maxRetryAttempts` 3 gives up to 6 attempts ([Retries and error handling](https://docs.restate.dev/ai/patterns/error-handling)).
- Several 1.7 features are one-way (protocol v7, vqueues), and request/response targets such as Lambda "require a sequence of invocations, often one per durable step" ([Server changelog](https://docs.restate.dev/changelog/server), [Cloud roadmap](https://docs.restate.dev/roadmap/cloud)).

**Known criticisms**
- BSL: a launch-thread commenter called "open source" misleading, and a Restate engineer conceded "individual atomic side effects need to be idempotent" ([Show HN](https://news.ycombinator.com/item?id=40659160)).
- Temporal's co-founder: inlining steps "doesn't protect against programming errors like calling Date.now()"; the article's author concluded the difference is "ergonomic, not contractual" ([DEV article](https://dev.to/andriiboyko/restates-single-binary-vs-temporals-cluster-when-the-lighter-engine-wins-3635)). An analyst: "'Single binary' should not be interpreted as 'no operational burden'", and the 25,000 per second figure "comes from the vendor's customer case study, not an independent benchmark" ([remio](https://www.remio.ai/post/restate-series-a-pits-a-20m-bet-against-database-backed-durable-execution)).

**Links.** [Docs](https://docs.restate.dev/) · [Pricing](https://restate.dev/pricing) · [Restate vs Temporal (vendor)](https://restate.dev/vs/temporal)

## Inngest

*Event-driven step functions memoized by step ID, with declarative flow control; managed cloud or a self-hosted single binary. Verdict: Grab for lean TypeScript teams that want flow control as config; know that renaming a step re-runs it.*

**What it is.** A durable execution platform in which your handler is re-entered and each `step.run` returns a saved result keyed by a hash of the step ID, rather than replaying an event history ([Durable Workflows](https://www.inngest.com/docs/durable-execution/durable-workflows)). TypeScript SDK v4 (GA 2026-03-16) turns checkpointing on by default, so consecutive steps run back to back in one request ([TypeScript SDK v4](https://www.inngest.com/blog/typescript-sdk-v4.0), [Introducing Checkpointing](https://www.inngest.com/blog/introducing-checkpointing)). Its agent pitch is "a harness, not a framework": every LLM call and tool call is a step in a bounded loop ([Utah](https://www.inngest.com/blog/your-agent-needs-a-harness-not-a-framework)). It raised a $21M Series A in September 2025 ([Series A](https://www.inngest.com/blog/announcing-inngest-series-a)).

| Field | Detail |
|---|---|
| Durability model | Memoization by step ID: "Inngest resumes a run by replaying your handler and returning saved step results"; "Code outside a step can run again each time the handler replays". With checkpointing the SDK falls back to standard orchestration on a retry or parallel steps; Python has no checkpointing ([Durable Workflows](https://www.inngest.com/docs/durable-execution/durable-workflows)). |
| Journaled, replayed, checkpointed | Journaled: step outputs with their index. Replayed: the handler body. Checkpointed: outputs saved through an idempotent API, with `bufferedSteps` (default 1) as an opt-in buffer that "may be lost if your server is not gracefully terminated" ([Checkpointing](https://www.inngest.com/docs/durable-execution/guides-and-advanced/checkpointing)). |
| Determinism rules | No strict determinism error; reordering "may log an order warning". But renaming a step ID re-runs it in in-flight runs, and "the change can repeat a payment, email, or resource creation" ([Versioning](https://www.inngest.com/docs/durable-execution/guides-and-advanced/versioning)). Steps are at-least-once: "Make refundOrder idempotent because a refund can succeed before Inngest saves the step result" ([Durable Workflows](https://www.inngest.com/docs/durable-execution/durable-workflows)). |
| Limits | 4 MiB per step return, 32 MiB run state, 1,000 steps per function, step timeout up to 2 hours (within the host's limit), sleep up to 1 year. Max run length including waits: Free 30 days, Pro 90, Business 366. Concurrent steps: Free 5, Pro 100, Business 500. Event size: Free 256 KiB, Pro 3 MiB ([Usage limits](https://www.inngest.com/docs/durable-execution/limits)). Durable Endpoints (beta) are for work "no longer than 60 seconds" without flow control ([Durable Endpoints](https://www.inngest.com/blog/introducing-durable-endpoints)). |
| HITL primitives | `step.waitForEvent` returns null on timeout and "only sees events sent after it starts listening"; a losing wait in `group.parallel()` stays active until timeout; `step.waitForSignal` is experimental and transactional ([step.waitForEvent()](https://www.inngest.com/docs/durable-execution/primitives/step-waitforevent)). Waiting runs hold no concurrency slot ([Concurrency Control](https://www.inngest.com/docs/durable-execution/flow-control/concurrency)). |
| Versioning and patching | By step ID; a major rewrite gets a new function ID routed by `event.ts` or `event.v` ([Versioning](https://www.inngest.com/docs/durable-execution/guides-and-advanced/versioning)). v3 middleware "is not forward-compatible" with v4 ([TypeScript SDK v4](https://www.inngest.com/blog/typescript-sdk-v4.0)). |
| Observability and UI | Dashboard and step traces (history Free 24 h, Pro 7 days, Business 14 days); rerun-from-step on finished runs; AI Overview cost charts "based on uncached token usage"; Scoring and Defer in beta ([Changelog](https://www.inngest.com/changelog), [Pricing](https://www.inngest.com/pricing)). |
| Hosting and ops | Cloud, or one self-hosted binary (event API, queue, executor, state, dashboard) with in-memory Redis and SQLite by default and Postgres plus Redis to scale; `inngest start` is labelled Beta and support "does not guarantee direct support for self-hosted instances" ([Self-Hosting](https://www.inngest.com/docs/self-hosting)). Server license SSPL, Apache 2.0 on the third anniversary of each release; SDKs Apache 2.0 ([repo](https://github.com/inngest/inngest)). |
| Pricing | Free $0 with 50k executions; Pro from $99 a month with 1M and overage from $0.000050 down to $0.000015 per execution; Business from $499 with 10M. Executions = runs x (steps + 1) ([Pricing](https://www.inngest.com/pricing)). Derived: $0.05 per 1,000 at the top overage rate; a 20-turn agent is about 41 executions (ZenML estimate, [Inngest alternatives](https://www.zenml.io/blog/inngest-alternatives)), roughly $0.002. |
| SDKs, latest release | TypeScript 4.23.0 (2026-10-09), Python 0.5.19 (2026-06-23), Go v0.16.5 (2026-10-02); server and CLI v1.46.0 (2026-10-06) ([repo](https://github.com/inngest/inngest)). |
| Maturity | 2026 incidents: a roughly 19-hour scheduling delay on April 15 triggered by a bug in Inngest's own SDK, four in July and a September 18 PgBouncer exhaustion; no events were lost in April and work was "delayed rather than lost" in September ([April](https://www.inngest.com/blog/2026-04-17-incident-report-for-april-15-2026), [July](https://www.inngest.com/blog/2026-07-23-incident-rca-july-2026), [September](https://www.inngest.com/blog/2026-09-18-incident-report-for-september-18-2026)). AgentKit's last stable release is 0.13.2 (2025-11-13), so it looks stalled (inference) ([AgentKit](https://agentkit.inngest.com/concepts/routers)). |
| AI and agent integrations | `step.ai.infer` (openai, gemini, anthropic, grok, azure-openai; provider streaming "coming soon") and `step.ai.wrap` (TypeScript only) ([AI Inference Steps](https://www.inngest.com/docs/features/inngest-functions/steps-workflows/step-ai-orchestration)); Sandboxes as steps in open beta since 2026-10-06 ([Sandboxes are a durability problem](https://www.inngest.com/blog/sandboxes-are-a-durability-problem)). |

**Great at**
- Flow control as config: per-key concurrency, throttling, debounce, priority, batching, and singleton, which locks a whole run: "A concurrency limit of 1 ... does not provide the same run-level rule" ([Singleton Functions](https://www.inngest.com/docs/durable-execution/flow-control/singleton)). Monk found per-tenant concurrency keys had "No native equivalent" in Temporal ([How Monk migrated](https://temporal.io/blog/how-monk-migrated-100-workflows-inngest-to-temporal)).
- Rerun-from-step serves earlier steps from memoized output; the vendor's example cuts ten prompt iterations from $15.00 to $6.00 at placeholder pricing ([Don't pay for the same step twice](https://www.inngest.com/blog/dont-pay-for-the-same-step-twice)). Candid scoping: "Inngest is the wrong pick if you need managed compute (Trigger.dev wins that one)" ([Best job queue alternatives](https://www.inngest.com/blog/best-job-queue-alternatives)).

**Painful at**
- Forgiving is not safe: a renamed step ID re-runs completed work, side effects included. Python trails TypeScript (no checkpointing, no sandboxes). Published numbers disagree across Inngest's own pages (Free sleep 7 days in an October essay against 30 days on the limits page), so use the limits page ([essay](https://www.inngest.com/blog/all-your-workflows-are-about-to-be-long-running)).
- Renting durability moves the failure: in the July 10 incident some `step.fetch()` calls "returned empty responses" ([July RCA](https://www.inngest.com/blog/2026-07-23-incident-rca-july-2026)).

**Known criticisms**
- HN: "not very reliable" (one 2025 comment), "why did you choose Inngest, which sends all your private data to Inngest?" (from a former DBOS CEO, a competitor), and "so much simpler operationally" than Temporal ([HN comments](https://hn.algolia.com/api/v1/items/49699793)).
- Monk, on Temporal's blog: Inngest "started hurting us at scale" ([How Monk migrated](https://temporal.io/blog/how-monk-migrated-100-workflows-inngest-to-temporal)). Inngest's own survey (n=130) puts Temporal users at +22 points of reliability burden against -10 for Inngest users; self-run, self-reported, a correlation (vendor) ([AI in Production](https://www.inngest.com/blog/ai-in-production-report-2026)).

**Links.** [Limits](https://www.inngest.com/docs/durable-execution/limits) · [Pricing](https://www.inngest.com/pricing) · [Changelog](https://www.inngest.com/changelog)

## LangGraph

*Agent runtime library with pluggable checkpointers, plus LangSmith Deployment (Agent Server, formerly LangGraph Platform) as the managed or self-hosted runtime. Verdict: Grab for chat and approval agents with sub-hour steps; skip as the sole outer loop for hours-long, side-effecting steps unless you run Agent Server and design for re-runs.*

**What it is.** LangGraph writes a checkpoint at each super-step boundary and saves per-task writes separately, so completed siblings in a failed super-step are not re-run; replay from an older checkpoint re-executes everything after it, "including any LLM calls, API requests, or interrupts" ([Checkpointers](https://docs.langchain.com/oss/python/langgraph/checkpointers)). 1.0 (October 2025) was a stability release; 1.2.0 (2026-05-12) added per-node timeouts, error handlers, graceful shutdown and DeltaChannel ([changelog](https://docs.langchain.com/oss/python/releases/changelog)). LangGraph Platform was renamed LangSmith Deployment in October 2025 ([GA post](https://www.langchain.com/blog/langgraph-platform-ga)); the vendor reports 65M+ downloads a month ([3 Years of Graph Engineering](https://www.langchain.com/blog/3-years-of-graph-engineering-with-langgraph)).

| Field | Detail |
|---|---|
| Durability model | Super-step checkpoints keyed by `thread_id`. Modes: `exit` ("you cannot recover from system failures (like process crashes) mid-execution"), `async` and `sync` ([Checkpointers](https://docs.langchain.com/oss/python/langgraph/checkpointers)). Agent Server adds a Postgres task queue with leases: "at most 1 run can be executed for a given thread at one time"; default durability `async` ([Agent Server](https://docs.langchain.com/langsmith/agent-server)). |
| Journaled, replayed, checkpointed | Graph API: state per super-step plus pending writes. Functional API: task results go into the entrypoint's checkpoint and resume replays from the entrypoint start, but "A task that started but did not finish may run again" ([Functional API](https://docs.langchain.com/oss/python/langgraph/functional-api)). |
| Determinism rules | Side effects go in tasks. On interrupt resume "the runtime restarts the entire node from the beginning", resume values match by index, side effects before `interrupt()` must be idempotent, and `while True` plus `interrupt()` loops replay every earlier iteration ([Interrupts](https://docs.langchain.com/oss/python/langgraph/interrupts)). |
| Limits | Full-snapshot checkpoints grow at O(N^2): 5.3 GB for a 200-turn coding agent against 129 MB with DeltaChannel (beta) ([Delta Channels](https://www.langchain.com/blog/delta-channels-evolving-agent-runtime)). MongoDB checkpointers have a 16 MB document limit ([Knowledge Base](https://kb.langchain.com/articles/6253531756-understanding-checkpointers-databases-api-memory-and-ttl)). Agent Server waits 180 s by default after a shutdown signal (`BG_JOB_SHUTDOWN_GRACE_PERIOD_SECS`, max 3600) ([env vars](https://docs.langchain.com/langsmith/env-var-self-hosted)), and platform retries cover only transient PostgreSQL errors ([data plane](https://docs.langchain.com/langsmith/data-plane)). |
| HITL primitives | `interrupt()` and `Command(resume=...)`; `HumanInTheLoopMiddleware` with approve, edit, reject and respond, where "respond" is "treated as a successful tool result" and must not deny side-effecting tools ([middleware](https://docs.langchain.com/oss/python/langchain/human-in-the-loop)). Double texting (enqueue, reject, interrupt, rollback) is "not available in the LangGraph open source framework" ([Double texting](https://docs.langchain.com/langsmith/double-texting)). |
| Versioning and patching | "Checkpoints carry no application version" (Restate, vendor-authored) ([critique](https://restate.dev/blog/why-checkpointing-is-not-production-grade-durable-execution)). Deep Agents 0.6: "Rolling back from v0.6.0 is not supported once threads have persisted" ([changelog](https://docs.langchain.com/oss/python/releases/changelog)). An audit of the 100 most-starred repos with executable LangGraph code found 7.4% of codebases with durable workflows binding live semantic resources without an evident immutable binding ([BEGIN AI TRANSACTION](https://arxiv.org/abs/2608.05412)). |
| Observability and UI | LangSmith tracing and SmithDB, a Rust trace store ([Interrupt 2026](https://www.langchain.com/blog/interrupt-2026-overview)); the open-source library has no UI. |
| Hosting and ops | Library plus a checkpointer (PostgresSaver is "used in LangSmith"), or LangSmith Deployment: Cloud (Plus plan or above), hybrid, self-hosted with a control plane (Enterprise), or standalone with your own Postgres, Redis and license ([overview](https://docs.langchain.com/langsmith/deployment)). Kubernetes with the Helm chart is the tested path; non-Kubernetes gaps include "Graceful run draining" ([standalone servers](https://docs.langchain.com/langsmith/deploy-standalone-server)). |
| Pricing | Developer $0; Plus $39 per seat per month; Enterprise custom. Deployments are metered in LSUs (1 LSU = $1): 0.0675 per runtime vCPU-hour, 0.177 per database vCPU-hour ([Plans and Pricing](https://www.langchain.com/pricing)). |
| SDKs, latest release | Python langgraph 1.2.14 (2026-10-06); JS @langchain/langgraph 1.4.21 (2026-10-07), without Python's per-node timeouts and error handlers; langgraph-api 0.16.0 (2026-10-10), whose changelog still fixes erased pending interrupts and threads wedged after a mid-run kill ([changelog](https://docs.langchain.com/oss/python/releases/changelog), [Agent Server changelog](https://docs.langchain.com/langsmith/agent-server-changelog)). |
| AI and agent integrations | LangChain 1.x middleware (call limits, retries, fallbacks, HITL) ([Prebuilt middleware](https://docs.langchain.com/oss/python/langchain/middleware/built-in)); the Claude Agent SDK, Strands, CrewAI and AutoGen deploy on Agent Server wrapped in `@task` and `@entrypoint` ([Deploy other frameworks](https://docs.langchain.com/langsmith/deploy-other-frameworks)); LLM Gateway spend caps return HTTP 402 ([LLM Gateway](https://www.langchain.com/blog/langsmith-llm-gateway-runtime-controls-for-production-agents)). |

**Great at**
- 1.2 fault tolerance: `RetryPolicy`, `TimeoutPolicy` refreshed by `runtime.heartbeat()`, and `error_handler` for Saga-style compensation ([Fault tolerance](https://docs.langchain.com/oss/python/langgraph/fault-tolerance)); code owns the workflow: "These policies belong in code, not in a prompt" ([runtime behind production deep agents](https://www.langchain.com/blog/runtime-behind-production-deep-agents)).
- Run ownership on Agent Server, and cost breakers such as `ModelCallLimitMiddleware(thread_limit=10, run_limit=5, exit_behavior="end")` ([Prebuilt middleware](https://docs.langchain.com/oss/python/langchain/middleware/built-in)).

**Painful at**
- The open-source library has no failure detection or automatic resume: "It is not failed. It is stranded." (Diagrid, a rival) ([Running LangGraph Reliably](https://www.diagrid.io/learn/integrating-diagrid-catalyst-langgraph)).
- Drain takes effect only between super-steps: a node mid-execution "Runs to completion" and `request_drain()` "does not cancel running asyncio tasks or kill threads" ([Fault tolerance](https://docs.langchain.com/oss/python/langgraph/fault-tolerance)). Whole-node re-runs mean one coarse node around a model call repays it.

**Known criticisms**
- Temporal (vendor): "LangGraph checkpoints state, but checkpoints are not durable execution" ([Temporal's LangGraph plugin](https://temporal.io/blog/temporal-langgraph-plugin-durable-execution)). Diagrid (vendor, 2026-02-25, before 1.2): "The fundamental problem is that LangGraph makes you the orchestrator", with "No duplicate execution prevention" if two processes resume one `thread_id` ([Checkpoints Are Not Durable Execution](https://www.diagrid.io/blog/checkpoints-are-not-durable-execution-why-langgraph-crewai-google-adk-and-others-fall-short-for-production-agent-workflows)).
- The other direction (vendor): LangChain said durable engines add "latency between steps", and its comparison page says one Temporal Activity around a whole graph loses replayability while conceding "Many teams run both" ([Building LangGraph](https://www.langchain.com/blog/building-langgraph), [LangGraph vs Temporal](https://www.langchain.com/resources/langgraph-vs-temporal)).

**Links.** [Checkpointers](https://docs.langchain.com/oss/python/langgraph/checkpointers) · [Agent Server](https://docs.langchain.com/langsmith/agent-server) · [Pricing](https://www.langchain.com/pricing)

## DBOS

*Durable execution as a library over Postgres (or SQLite), with an optional Conductor control plane. Verdict: Grab when ops will not run another stateful service; plan for the deploy and versioning trap.*

**What it is.** DBOS Transact is an MIT library that checkpoints workflow inputs and step outputs to a Postgres system database; recovery re-runs the workflow and returns checkpointed outputs until the first step with no checkpoint ([DBOS Architecture](https://docs.dbos.dev/architecture)). Workers dequeue from a Postgres table with locking clauses and detect duplicate work through integrity constraints ([Postgres is all you need](https://www.dbos.dev/blog/postgres-is-all-you-need-for-durable-execution)). There is no orchestrator server; Conductor is a separate control plane that "does not store any workflow data or metadata" ([What is DBOS Conductor?](https://www.dbos.dev/blog/what-is-dbos-conductor)).

| Field | Detail |
|---|---|
| Durability model | One database write per step plus two per workflow. Steps are "tried at least once but are never re-executed after they complete"; transactions commit exactly once ([Workflows tutorial](https://docs.dbos.dev/python/tutorials/workflow-tutorial)). "Durable workflows alone do not solve the idempotency problem", because a step can finish before its checkpoint lands ([Co-locating workflow state](https://www.dbos.dev/blog/co-locating-workflow-state-with-your-data)). |
| Journaled, replayed, checkpointed | Journaled: workflow inputs and step outputs; Python v3.0 and TypeScript v5.0 moved them into separate tables, and "older versions cannot process workflows created by the new versions" ([What's New, September 2026](https://www.dbos.dev/blog/whats-new-in-dbos-september-2026)). Replayed: the workflow function. |
| Determinism rules | The workflow function must be deterministic; a mismatch raises `DBOSStepNondeterminismError`; concurrent async steps are valid only if "started in a deterministic order"; the workflow ID is an idempotency key ([Workflows tutorial](https://docs.dbos.dev/python/tutorials/workflow-tutorial)). |
| Limits | A single Postgres sustains ">40K workflows or steps per second" ([Architecture](https://docs.dbos.dev/architecture)); vendor benchmark: 144K writes or 43K workflows per second on one large RDS instance ([Benchmarking](https://www.dbos.dev/blog/benchmarking-workflow-execution-scalability-on-postgres)). Large files go to blob storage, with pointers returned. |
| HITL primitives | `DBOS.send` and `DBOS.recv`, queued per topic in Postgres; `recv` defaults to a 60 s timeout ([Communicating with Workflows](https://docs.dbos.dev/python/tutorials/workflow-communication)). |
| Versioning and patching | Patching or versioning: workflows are tagged with the application version they started on, by default a hash of workflow source ([Upgrading Workflow Code](https://docs.dbos.dev/python/tutorials/upgrading-workflows)). A workflow can be forked from a step, which the vendor compares to a "git branch" for workflow execution ([Fork it until you make it](https://www.dbos.dev/blog/ai-agents-do-weird-stuff-and-how-to-fix-it)). |
| Hosting and ops | A library on regular Aurora/RDS, Cloud SQL, AlloyDB, Supabase, Neon or CockroachDB ([Show HN: DBOSify](https://hn.algolia.com/api/v1/items/48662988)). Conductor connects over an outbound websocket; on worker failure it "waits a configurable grace period, then signals another worker"; an outage leaves workflows running ([What is DBOS Conductor?](https://www.dbos.dev/blog/what-is-dbos-conductor)). |
| Pricing | Library free. Conductor Pro $99 a month (2 seats, 3 apps, 1M checkpoints, $50 per extra million); Teams $499 (10M, $40 per extra million); Enterprise adds self-hosted Conductor; DBOS Cloud is contact-sales ([DBOS Pricing](https://www.dbos.dev/dbos-pricing)). A commenter reported no reply on the self-hosted license price ([HN](https://hn.algolia.com/api/v1/items/48662988)). Derived: $0.05 per 1,000 extra Pro checkpoints. |
| SDKs, latest release | Python 3.2.0 and TypeScript 5.2.11 (both 2026-09-29), Go v1.4, Java v1.1, Rust v0.5 "under active development" ([What's New](https://www.dbos.dev/blog/whats-new-in-dbos-september-2026), [PyPI](https://pypi.org/project/dbos/)). |
| AI and agent integrations | `@dbos-inc/vercel-ai`: `durableCalls` checkpoint model calls, durable streams with a `data-dbos-superseded` chunk when an interrupted call restarts, durable tools, subagents as child workflows ([What's New](https://www.dbos.dev/blog/whats-new-in-dbos-september-2026)); a Pydantic AI backend ([Pydantic AI](https://pydantic.dev/docs/ai/capabilities/durable_execution/overview/)). Observability is a Conductor dashboard plus SQL over the checkpoint tables. |

**Great at**
- No extra stateful service: Postgres you already run, with a step's database update and its checkpoint committing together ([Co-locating workflow state](https://www.dbos.dev/blog/co-locating-workflow-state-with-your-data)). Fork and rewind: copy checkpoints up to a step and iterate without re-paying the prefix ([Fork it until you make it](https://www.dbos.dev/blog/ai-agents-do-weird-stuff-and-how-to-fix-it)).
- An escape hatch for Temporal Python code: "Take any Temporal example and swap the imports and connection strings" (the DBOSify author) ([Show HN](https://hn.algolia.com/api/v1/items/48662988)).

**Painful at**
- Upgrades: "Don't run old and new Transact versions concurrently under the same application version" ([What's New](https://www.dbos.dev/blog/whats-new-in-dbos-september-2026)). Multi-replica recovery needs Conductor, from $99 a month ([What is DBOS Conductor?](https://www.dbos.dev/blog/what-is-dbos-conductor)).
- An interrupted model call is restarted and paid again, which the vendor's own durable-stream marker makes visible ([What's New](https://www.dbos.dev/blog/whats-new-in-dbos-september-2026)).

**Known criticisms.** DBOS's claims against Temporal ("Sub-2ms step latency", "~20 minutes" to adopt against "~2 weeks") are vendor marketing, and Temporal also runs on Postgres ([DBOS vs. Temporal](https://dbos.dev/compare/dbos-vs-temporal), [Under the Hood](https://backend.how/posts/temporal-under-the-hood/)). Obelisk argues SQLite is enough, with the caveat that Litestream can lose the newest writes ([Obelisk](https://obeli.sk/blog/sqlite-is-all-you-need-for-durable-workflows/)).

**Links.** [Docs](https://docs.dbos.dev/) · [Pricing](https://www.dbos.dev/dbos-pricing) · [September 2026 update](https://www.dbos.dev/blog/whats-new-in-dbos-september-2026)

## Hatchet

*Postgres-backed task queue with durable tasks, DAGs and fan-out; MIT, self-hosted or cloud. Verdict: Grab when a queue with some durable tasks is enough; its docs are candid about when you do not need durable execution.*

**What it is.** A task queue first. Its co-founder writes that "If your task is easily idempotent, and can update application state via a single database transaction, then this is likely all you need", and that durable execution "is not a silver bullet" ([How to think about durable execution](https://hatchet.run/blog/durable-execution)). Durable tasks write a checkpoint to a durable event log on each completion, and a durable task "must only either call methods on the durable context or spawn children" ([Durable Tasks and Durable Execution](https://docs.hatchet.run/v1/durable-tasks)).

| Field | Detail |
|---|---|
| Durability model | Durable event log; replay "doesn't re-execute completed operations, but it does re-run the code path that led to each checkpoint". Retries continue a durable task without resetting its history; replays reset it ([Durable execution, the hard way](https://github.com/hatchet-dev/durable-execution-the-hard-way)). |
| Journaled, replayed, checkpointed | Journaled: durable-context calls (sleeps, event waits) and child spawns, each subtask dispatched "exactly once using an idempotency key". Replayed: the task's code path. |
| Determinism rules | "the code between checkpoints must be deterministic"; the docs' test is "If this task was interrupted and replayed from the last checkpoint, would this code produce the same result?". Go map iteration and reordering two steps both break it ([blog](https://hatchet.run/blog/durable-execution)). |
| Limits | Cloud pay-as-you-go keeps 7 days of retention, and past the free tier "tasks will get dropped" ([Pricing](https://hatchet.run/pricing)). History and payload limits are not stated in the sources reviewed. |
| HITL primitives | Durable event waits from the SDK or a webhook: "Even if the task is interrupted and requeued while waiting, the event will still be processed" ([Durable Event Waits](https://docs.hatchet.run/v1/durable-event-waits)). |
| Versioning, observability | Not stated in the sources reviewed beyond the determinism rule. Trigger-level idempotency keys (TTL-based or status-based, via a CEL expression) shipped in mid-2026 ([Idempotency](https://docs.hatchet.run/v1/idempotency)). |
| Hosting and ops | MIT and self-hostable. Embedded mode runs the engine inside the worker (Go) or as a sidecar with embedded Postgres (TypeScript, Python) for local tests and CI ([Embedded Mode](https://docs.hatchet.run/v1/embedded)). Waiting durable tasks can be evicted from workers (`EvictionPolicy(ttl=..., allow_capacity_eviction=True, priority=0)`) and restored by replaying the log ([Task Eviction](https://docs.hatchet.run/v1/task-eviction)). |
| Pricing | 1M runs and 1M events free each month, then $30 per million runs and $5 per million events; retries are not billed, failed runs count ([Pricing](https://hatchet.run/pricing)). Derived: $0.03 per 1,000 runs. |
| SDKs, maturity | Python (hatchet-sdk 1.42.1), TypeScript (1.37.0, 2026-10-09), Go ([Hatchet](https://hatchet.run)); v1 durable-task docs updated 2026-10-09; no independent production reports or benchmarks were found. The docs list agentic workflows with human-in-the-loop as a fit ([Durable Tasks](https://docs.hatchet.run/v1/durable-tasks)). |

**Great at.** A thin controller by construction: the durable parent may only wait or spawn, so model calls and file writes live in child tasks ([Durable Tasks](https://docs.hatchet.run/v1/durable-tasks)). Free waiting through eviction, and an embedded engine that lets CI test the orchestrator ([Task Eviction](https://docs.hatchet.run/v1/task-eviction), [Embedded Mode](https://docs.hatchet.run/v1/embedded)).

**Painful at.** The determinism tax: persisting program state has overhead "both in terms of developer experience, but also in terms of $$$" ([blog](https://hatchet.run/blog/durable-execution)). Idempotency keys guard the trigger, not the side effects a retry repeats ([Idempotency](https://docs.hatchet.run/v1/idempotency)).

**Known criticisms.** No independent critique or benchmark was found. Its co-founder opens by describing 30 minutes with the Temporal docs that left him with nothing, and positions the project as the simpler queue ([blog](https://hatchet.run/blog/durable-execution)).

**Links.** [Docs](https://docs.hatchet.run/v1/durable-tasks) · [Pricing](https://hatchet.run/pricing) · [Tutorial repo](https://github.com/hatchet-dev/durable-execution-the-hard-way)

## Trigger.dev

*Managed TypeScript background tasks with checkpoint-resume instead of replay. Verdict: Grab for TypeScript teams that want managed long tasks and chat agents; remember that `run()` reruns from the top on retry.*

**What it is.** It does not replay a workflow function. When a task waits on a subtask or a long timer it snapshots the whole process with CRIU (memory, CPU registers, open file descriptors), releases compute and restores it later; multi-step durability comes from subtasks whose outputs are cached by idempotency key ([How Trigger.dev works](https://trigger.dev/docs/how-it-works)). Its co-founder describes a newer combination of an append-only context log with Firecracker microVM snapshots ([Two Roads to Durable Agents](https://ai.engineer/talks/svCnShDvgQg-two-roads-durable-agents-replay-vs-snapshot)).

| Field | Detail |
|---|---|
| Durability model | Process snapshot at waits; no function replay. The idempotency key "does not make the code inside a task's run() function idempotent": "When a run retries, run() executes again from the top" ([Idempotency](https://trigger.dev/docs/idempotency)). |
| Journaled, replayed, checkpointed | Checkpointed: the whole process at a wait. Journaled: subtask outputs by idempotency key. Replayed: nothing. |
| Limits | The docs conflict on the checkpoint threshold: waits under 60 s do not checkpoint ([How it works](https://trigger.dev/docs/how-it-works)), against "a wait longer than 5 seconds" in the Wait docs ([Wait: Overview](https://trigger.dev/docs/wait)). 1,500 API requests per minute on all plans ([Pricing](https://trigger.dev/pricing)); "Free compute isn't the same as freed concurrency". |
| HITL primitives | Waitpoint tokens: `wait.createToken({ timeout: "10m" })`, completed through the SDK or a POST (CORS enabled), with idempotency key and TTL ([Wait for token](https://trigger.dev/docs/wait-for-token)). |
| Versioning and patching | Skew protection (2026-09-23): deploy with an external id such as the commit SHA; a run waits at most an hour for its matching build, then expires; chat sessions pin to their starting deployment ([Atomic deployments](https://trigger.dev/changelog/version-skew-protection)). |
| Observability, hosting | Dashboard with each chat turn as a span; AI Prompts versioned on every deploy ([v4.5.0](https://trigger.dev/changelog/v4-5-0)). Managed Cloud, or open source under Apache 2.0 ([Pricing](https://trigger.dev/pricing)). |
| Pricing | Free $0 ($5 credits, 20 concurrent runs), Hobby $10 a month (50), Pro $50 (200+); compute $0.0000338 per second on a 0.5 vCPU machine; $0.25 per 10,000 runs; checkpointed waits are not billed ([Pricing](https://trigger.dev/pricing)). Derived: $0.025 per 1,000 runs plus compute. |
| SDKs, releases | TypeScript (`@trigger.dev/sdk` 4.7.3, 2026-10-06). v4.5.0 (2026-07-02) made `chat.agent`, Sessions and AI Prompts generally available; v4.6.0 (2026-09-14) had 2 breaking changes; v4.7.0 (2026-10-01) separated concurrency, with named limits such as `concurrencyLimit({ name: "openai", total: 25 })` ([v4.6.0](https://trigger.dev/changelog/v4-6-0), [v4.7.0](https://trigger.dev/changelog/v4-7-0)). |
| AI and agent integrations | A chat conversation runs as one long-lived task keyed on `chatId` that survives refreshes, redeploys and crashes ([v4.5.0](https://trigger.dev/changelog/v4-5-0)). |

**Great at.** Long tasks without slicing them into steps; Inngest itself says "Trigger.dev wins" on managed compute ([Best job queue alternatives](https://www.inngest.com/blog/best-job-queue-alternatives)). Cheap waiting, and provider-wide concurrency caps that are named and adjustable at runtime ([v4.7.0](https://trigger.dev/changelog/v4-7-0)).

**Painful at.** A retry restarts `run()`, so every unguarded call, write or email repeats ([Idempotency](https://trigger.dev/docs/idempotency)). Silent platform behaviour: before 4.6, omitting a spread "silently switched off steering, compaction, and injected context" ([v4.6.0](https://trigger.dev/changelog/v4-6-0)). An October 6 sync bug overwrote variables in 121 customer organizations over 2 hours 49 minutes, and tests passed because "Our examples didn't contain Sensitive variables" ([Incident report](https://trigger.dev/blog/incident-report-oct-6-2026)).

**Known criticisms.** Beyond its own incident report and the threshold inconsistency, no independent critique was found. Snapshot engines avoid determinism rules but not idempotency, as the vendor's own talk says.

**Links.** [How it works](https://trigger.dev/docs/how-it-works) · [Pricing](https://trigger.dev/pricing) · [Changelog v4.7.0](https://trigger.dev/changelog/v4-7-0)

## Vercel Workflow

*Directive-based durable functions (`'use workflow'`, `'use step'`) over an event log, with pluggable "Worlds". Verdict: Grab on Vercel and Next.js; watch the 240 s replay cap and the Postgres World's missing version routing.*

**What it is.** Vercel Workflows went GA on 2026-04-16: a `'use workflow'` orchestrator and `'use step'` units backed by an event log, Fluid compute functions and Vercel Queues; "Workflows eliminates the orchestrator entirely". Since the October 2025 beta it reports over 100 million runs and 500 million steps across more than 1,500 customers (vendor) ([A new programming model for durable execution](https://vercel.com/blog/a-new-programming-model-for-durable-execution)). The SDK author spent about six months on a fork of Temporal before building this library-first engine ([The best workflow engine is a programming language](https://vercel.com/blog/the-best-workflow-engine-is-a-programming-language)).

| Field | Detail |
|---|---|
| Durability model | Event-log replay: "the workflow code gets re-run multiple times during its lifecycle, each time using the event log"; step results are persisted ([Workflows and Steps](https://workflow-sdk.dev/docs/foundations/workflows-and-steps)). Every run is "pegged to a specific deployment". |
| Journaled, replayed, checkpointed | Journaled: step inputs and outputs, sleeps, hooks, errors. Sources disagree on streams: the GA post says the log records stream chunks, the Streaming docs say streams "bypass the event log" ([Streaming](https://workflow-sdk.dev/docs/foundations/streaming)). |
| Determinism rules | A sandbox without full Node.js; `Math.random` and `Date` are fixed; steps retry 3 times by default. 5.0.0 changed the RNG seed, so "runs started before this change must not be replayed across the upgrade" ([@workflow/core CHANGELOG](https://raw.githubusercontent.com/vercel/workflow/HEAD/packages/core/CHANGELOG.md)). |
| Limits | Max replay duration 240 s; runs over 2,000 events or 1 GB of entity storage replay slower, so use child workflows; 50 MB per payload, 2 GB per run ([Pricing and Limits](https://vercel.com/docs/workflows/pricing)). |
| HITL primitives | Hooks: a suspension point that yields a token an external system uses to resume the run; "knowing the token does not authorize the caller" ([Hooks](https://workflow-sdk.dev/docs/foundations/hooks)). |
| Versioning and patching | Runs pin to their starting deployment; "The official Postgres world doesn't track and route versions this way yet" ([best workflow engine](https://vercel.com/blog/the-best-workflow-engine-is-a-programming-language)). Workflows 5 plans a snapshot-based runtime to cut replay overhead ([GA post](https://vercel.com/blog/a-new-programming-model-for-durable-execution)). |
| Hosting and ops | Worlds: Managed on Vercel, Self-hosted (Postgres reference) or Embedded (community MongoDB, Redis, Turso, Jazz Cloud, Cloudflare); step data is encrypted by default ([GA post](https://vercel.com/blog/a-new-programming-model-for-durable-execution)). Observability and UI details were not captured in the sources reviewed. |
| Pricing | Hobby includes 50,000 events a month; $0.02 per 1K events; 1 GB written included then $0.50 per GB; $0.50 per GB-month retained ([Pricing and Limits](https://vercel.com/docs/workflows/pricing)); step compute is billed as function time. Derived: $20 per million events. |
| SDKs, maturity | TypeScript (`workflow` 5.2.0, 2026-10-08, Apache-2.0) ([npm](https://www.npmjs.com/package/workflow)); Python SDK in beta (`vercel-workflow` 0.10.2 on 2026-09-15) ([PyPI](https://pypi.org/project/vercel-workflow/)). |
| AI and agent integrations | `DurableAgent` and `WorkflowAgent` for the AI SDK, durable streams ([GA post](https://vercel.com/blog/a-new-programming-model-for-durable-execution)). |

**Great at.** No separate orchestration service, with deployment pinning and encryption on by default ([GA post](https://vercel.com/blog/a-new-programming-model-for-durable-execution)); a sandbox that freezes time and randomness, a stricter guard than convention-based rules.

**Painful at.** The 240 s replay ceiling and the 2,000-event slowdown make long agent turns a poor fit for controller events ([Pricing and Limits](https://vercel.com/docs/workflows/pricing)); the self-hosted Postgres World lacks version routing and the Python SDK is beta ([best workflow engine](https://vercel.com/blog/the-best-workflow-engine-is-a-programming-language)).

**Known criticisms.** Inngest (a rival): "With explicit IDs, workflows survive code changes", and implicit IDs were "a real production issue we hit ourselves"; it also claimed "You must use Vercel as your compute layer", a statement from before Worlds that was not rechecked ([Explicit APIs vs Magic Directives](https://www.inngest.com/blog/explicit-apis-vs-magic-directives)). No independent benchmark was found.

**Links.** [Docs](https://workflow-sdk.dev/docs/foundations/workflows-and-steps) · [Pricing and limits](https://vercel.com/docs/workflows/pricing) · [GA post](https://vercel.com/blog/a-new-programming-model-for-durable-execution)

## Cloudflare Workflows and Agents SDK

*Serverless step-memoized workflows on Workers, plus an actor-per-agent runtime on Durable Objects. Verdict: Grab for serverless fan-out of many cheap waiting agents; 1 MiB step results and Durable Object eviction shape the design.*

**What it is.** Workflows memoizes each step result and treats the step name as the key: "Step names act as the 'cache key' in your Workflow", and instances "may hibernate and lose all in-memory state" ([Rules of Workflows](https://developers.cloudflare.com/workflows/build/rules-of-workflows/)). Workflows V2 (2026-04-15) replaced a single account-level Durable Object bottleneck: 50,000 concurrent instances (from 4,500), 300 instances per second per account (from 100), 2 million queued instances per workflow ([Workflows V2](https://blog.cloudflare.com/workflows-v2/)). The Agents SDK ("Project Think") makes each agent an addressable entity with its own SQLite database that "consumes zero compute when hibernated" ([Project Think](https://blog.cloudflare.com/project-think/)).

| Field | Detail |
|---|---|
| Durability model | Step memoization; "logic outside of the steps may be duplicated", and a CPU-heavy step restarts "from the beginning of that step" ([Rules of Workflows](https://developers.cloudflare.com/workflows/build/rules-of-workflows/)). Agents SDK: `runFiber` registers work in the agent's SQLite, keeps the object alive and calls `onFiberRecovered` after eviction ([fibers](https://developers.cloudflare.com/agents/runtime/execution/durable-execution/)). |
| Journaled, replayed, checkpointed | Journaled: step results by name. Checkpointed: fiber checkpoints in the agent's SQLite. Replayed: Workflow code outside steps. |
| Determinism rules | Name steps deterministically (no dates or randomness), branch only on the event payload or step outputs, keep steps idempotent and granular, hold no state outside steps ([Rules of Workflows](https://developers.cloudflare.com/workflows/build/rules-of-workflows/)). |
| Limits | Per step: 30 s CPU by default (up to 5 minutes), unlimited wall clock, 1 MiB result. Per instance: 1 GB state, 10,000 steps by default (up to 25,000), sleep up to 365 days, 30 days of retention (Paid) ([Limits](https://developers.cloudflare.com/workflows/reference/limits/)). Durable Object eviction: about 70-140 s idle, "Code updates / runtime restarts" 1-2 times a day ([fibers](https://developers.cloudflare.com/agents/runtime/execution/durable-execution/)). |
| HITL primitives | `waitForApproval()` is "a durable gate backed by Cloudflare Workflows, so the wait can continue for months or longer"; docs also show MCP elicitation, escalate-or-reject timeouts and reminders ([HITL patterns](https://developers.cloudflare.com/agents/concepts/agentic-patterns/human-in-the-loop/)). |
| Hosting, versioning, observability | Fully managed on Workers, no cluster to run; versioning and observability are not stated in the sources reviewed beyond deterministic step names. |
| Pricing | Requests 10M included then $0.30 per million; CPU 30M ms included then $0.02 per million ms; storage 1 GB-month then $0.20; steps 500,000 included then $0.80 per 100,000; waiting or sleeping uses no CPU. Step and storage billing starts on a date the research could not find ([Pricing](https://developers.cloudflare.com/workflows/reference/pricing/)). Derived: $0.008 per 1,000 steps. |
| SDKs, releases | TypeScript is the only language shown in the sources reviewed. No version numbers; docs last changed 2026-09-21 (limits, pricing) and 2026-10-08 (rules). |
| AI and agent integrations | The official tutorial makes every LLM call and tool call a retryable step, so a crash "resumes from the last successful step" ([Build a Durable AI Agent](https://developers.cloudflare.com/workflows/get-started/durable-agents/)); a CI SDK on Workflows can run an AI review agent that pushes fixes ([CI/CD blog](https://blog.cloudflare.com/ci-workflows/)). |

**Great at.** Idle cost: waiting or sleeping incurs no CPU time, and waiting instances do not count toward concurrency ([Pricing](https://developers.cloudflare.com/workflows/reference/pricing/), [Limits](https://developers.cloudflare.com/workflows/reference/limits/)). Fan-out of many small agents: "workflows themselves are being used to implement agent loops" ([Workflows V2](https://blog.cloudflare.com/workflows-v2/)).

**Painful at.** A 1 MiB step result and 30 s default CPU force artifacts out of band ([Limits](https://developers.cloudflare.com/workflows/reference/limits/)). Eviction is routine, including on code updates: "keepAlive() reduces the chance of eviction. runFiber() makes eviction survivable." ([fibers](https://developers.cloudflare.com/agents/runtime/execution/durable-execution/)).

**Links.** [Workflows docs](https://developers.cloudflare.com/workflows/) · [Limits](https://developers.cloudflare.com/workflows/reference/limits/) · [Agents SDK](https://developers.cloudflare.com/agents/)

## Golem

*Open-source WebAssembly durable agent runtime that journals every effect in an operation log. Verdict: Watch; a very different stack, and 1.6 is announced but not yet released.*

**What it is.** In Golem "every effect they have on the world is journaled in an operation log, the oplog", so the developer does not hand-place step boundaries. The 1.6 announcement (dated 2026-10-09) schedules the release for Monday, October 19, 2026, so its features are pre-release claims at the research cut-off ([Golem 1.6](https://golem.cloud/blog/golem-1-6-agents-tools-and-artifacts)).

| Field | Detail |
|---|---|
| Durability and recovery | Oplog of host effects; 1.6 targets snapshot-based recovery: "Long-lived agents now update and recover without replaying their setup" ([Golem 1.6](https://golem.cloud/blog/golem-1-6-agents-tools-and-artifacts)). |
| Determinism and tools | Host-enforced tool middleware is recorded in the oplog and "reproduces under replay"; the stated guarantees are "trust no text, possession-based authority, and replay-deterministic authorization". The homepage claims exactly-once tool calls, a vendor claim not tested here ([Golem](https://golem.cloud)). |
| Retries and quotas | 1.5 (2026-04-24) added named retry policies (`periodic`, `exponential`, `fibonacci`, `immediate`, `never`) with combinators (`countBox`, `timeBox`, `jitter`, `union`, `intersect`), and quotas of type Rate, Capacity or Concurrency that reject, throttle or terminate ([Golem 1.5](https://golem.cloud/blog/golem-1-5-features-part-17-semantic-retry-policies)). |
| SDKs, maturity, price | Six SDKs across five languages in 1.6, with a new Go SDK 0.1.0; the stated bar is that "it must be possible to build a complete coding agent entirely in Golem". The cloud pricing page returned 404. One digest reads the repo LICENSE as BSL 1.1 with a use grant that was not captured; read it before building on it. |

**Great at.** Durability below your code, with no per-call wrapping. **Painful at.** A WebAssembly toolchain that is a heavy adoption cost and a poor fit for existing Python agents, and no published price. **Known criticisms.** None independent found; ["The Rise of the Agent Runtime"](https://golem.cloud/blog/the-rise-of-the-agent-runtime) is vendor positioning. **Links.** [Golem](https://golem.cloud) · [1.6 announcement](https://golem.cloud/blog/golem-1-6-agents-tools-and-artifacts) · [1.5 quotas](https://golem.cloud/blog/golem-1-5-features-part-16-quotas)

## Resonate

*Distributed async/await built on durable promises: an SDK in your app and one Apache 2.0 server binary that keeps state in Postgres. Verdict: Watch; elegant model, small ecosystem.*

"The primitive is a durable promise", and "The core server is a single binary" with state "in your own Postgres" (NATS and ScyllaDB implementations also exist); the docs show a recursive sub-agent loop and position the product as agent-native ([Resonate docs](https://docs.resonatehq.io/)). The vendor claims workloads that "cost ~$80K/year on hosted alternatives can run for under $100/year" (vendor, marketing). Jack Vanlightly's series on the theory of durable execution names it alongside Temporal, Restate and DBOS ([Demystifying Determinism in Durable Execution](https://jack-vanlightly.com/blog/2025/11/24/demystifying-determinism-in-durable-execution)). Limits, versioning, HITL, SDK languages and release dates were not captured in the sources reviewed.

**Great at.** A small, clean primitive. **Painful at.** A small ecosystem and an unverified cost claim. **Links.** [Docs](https://docs.resonatehq.io/) · [Home](https://www.resonatehq.io)

## Newer and minimal engines

Seven engines that matter mostly as signals of where the field is going. None is a recommendation for an hours-long agent pipeline today.

- **Trigora** (continuation checkpointing). Captures the live continuation at durable boundaries instead of replaying history. A research prototype recovered in "approximately 0.6 and 0.9 milliseconds" against a Temporal fresh-worker replay of "approximately 61 milliseconds to 1.7 seconds" as depth went from 10 to 1,000, and the author says it "should not be interpreted as a general production-speedup claim" ([Durable execution without history replay](https://trigora.dev/blog/durable-execution-without-history-replay/)). The 1.0 SDK and CLI shipped in early October 2026 for declared TypeScript, Python and Rust subsets (MIT). On HN a builder asked the question no engine answers: "a publish succeeding remotely, then the worker dying before committing its continuation" ([HN](https://hn.algolia.com/api/v1/items/49635621)). Verdict: Watch.
- **LittleHorse** (server-side workflow specs). Argues "In 2026, however, durability is becoming a commodity" and contrasts replay of function calls with versioned, visualized WfSpecs that emit business events to Kafka, pitching the graph for "reviewing code written by agents" ([Business-as-Code](https://littlehorse.io/blog/beyond-durable-execution)). Server license not confirmed. Verdict: Consider for business-process governance; skip for an SDLC agent loop.
- **Absurd** (one SQL file on Postgres). "a single SQL file (absurd.sql)" with a TypeScript SDK of "about 1,400 lines" and Python "about 1,900"; after five months in production its author reports "You can call Math.random() or datetime.now() in between steps and things still work" ([Absurd In Production](https://lucumr.pocoo.org/2026/4/4/absurd-in-production/)). One benchmark found about 32 SQL statements against about 145 for Temporal on a 3-step job and about 20x the throughput on one Mac mini VM ([Under the Hood](https://backend.how/posts/temporal-under-the-hood/)); `absurd-sdk` 0.5.0 (2026-08-04), Apache-2.0. Verdict: Grab if you want to own every line.
- **pg_durable** (Microsoft, a Postgres extension). Its PGXN changelog runs from 0.2.4 (2026-07-02) to 0.2.8 (2026-09-11) and repeats upgrade warnings: in-flight instances can fail with a nondeterminism error after a binary upgrade, so "Drain in-flight work before upgrading when continuity is required" ([CHANGELOG](https://www.pgxn.org/dist/pg_durable/CHANGELOG.html)). Verdict: Skip for agent pipelines: its changelog says to drain before upgrading, and its README (an unverified lead, see [Unverified leads](#unverified-leads)) says not to use it when workflows span many systems.
- **Obelisk** (deterministic replay, SQLite). 0.42 (2026-10-04) replays workflow code "from that history, using recorded activity results" and adds layered policy files where "The effective permission is the intersection" of three files, with secrets substituted at the network edge ([Obelisk 0.42](https://obeli.sk/blog/announcing-obelisk-0-42/)). Verdict: Watch for the policy-file approach to agent-written code.
- **Sayiir** (Rust-core, continuation-driven). 1.0.0 on PyPI (2026-06-07, MIT); its README claims "no replay" and "no determinism constraints" ([Sayiir](https://github.com/sayiir/sayiir)). Verdict: Watch.
- **Pi Durable** (an agent harness, not an engine). Every step is a task that checkpoints first; a cut-off model request "is sent again" with the partial answer marked aborted; "a tool reruns only if it says that is safe"; about 15,000 lines; `@earendil-works/pi-durable` 1.1.0 (2026-10-07), "experimental" ([Pi Durable](https://earendil.com/posts/pi-durable/)). Verdict: read it for the effect-sandwich pattern.

kassette, an embeddable journal-only engine, is covered only as an unverified lead below. Also seen, not evaluated beyond the repository: [Durable Actors](https://github.com/TerseAI/durable-actors), an open-source alternative to Cloudflare Durable Objects (Show HN 2026-10-06), and [Orch8](https://github.com/orch8-io/engine), a Rust durable workflow engine (Show HN 2026-05-05).

## Part 1 at a glance

List prices per 1,000 billable units; the units are not comparable (an Action, an execution, a checkpoint, a run, an event and a step each count different things). The point is the scale: even at the top rate, 5,000 units a run is about $0.25 (an assumption; units per run were not measured), against $15 to $30 of model spend per feature run.

| Engine | Billable unit and list price | Per 1,000 (derived) | Also billed |
|---|---|---|---|
| Temporal Cloud | Action, $50 per million at the start ([pricing](https://docs.temporal.io/cloud/pricing)) | $0.05 | Storage per GB-hour |
| Restate Cloud | Durable action, $25 per million on Starter and Business ([pricing](https://restate.dev/pricing)) | $0.025 | One action per started 64 KiB payload |
| Inngest | Execution, top Pro overage $0.000050 ([pricing](https://www.inngest.com/pricing)) | $0.05 | Plan base from $99 (Pro) |
| DBOS Conductor | Extra checkpoint, $50 per million on Pro ([pricing](https://www.dbos.dev/dbos-pricing)) | $0.05 | Plan base ($99 Pro) |
| Hatchet | Run, $30 per million; events $5 per million ([pricing](https://hatchet.run/pricing)) | $0.03 | Retries free |
| Trigger.dev | Run invocation, $0.25 per 10,000 ([pricing](https://trigger.dev/pricing)) | $0.025 | Compute per second |
| Vercel Workflow | Event, $0.02 per 1K ([limits and pricing](https://vercel.com/docs/workflows/pricing)) | $0.02 | Data written and retained |
| Cloudflare Workflows | Step, $0.80 per 100,000 ([pricing](https://developers.cloudflare.com/workflows/reference/pricing/)) | $0.008 | Requests, CPU ms, storage |

## Gap-fill additions (2026-10-12)

- **The Replit case study, in full (vendor).** [Replit Agent switched to Restate for faster durable execution, cost efficiency, and BYOC](https://restate.dev/use-cases/replit) (Restate's page quoting Replit, undated; listed on the Restate blog index as of 2026-10-12) says: "Replit initially used Temporal for this, but switched to Restate for three main reasons": lower latency and cost efficiency; stateful and concurrent agent building blocks (Virtual Objects, Durable Coroutines); and BYOC. It reports that Replit Agent "executes thousands of fast, inline durable steps per turn", each adding "only a few milliseconds of latency overhead", and that "weeks after migration" a promotional traffic spike reached "close to 25,000 durable actions per second per cell".

## Unverified leads

Pages that loaded but did not match their title in one digest, or that could not be opened. Claims below are leads: where one is mentioned above, it is labelled as unverified.

- **kassette** ([lostinpatterns/kassette](https://github.com/lostinpatterns/kassette)): described as embeddable, journal-only durable execution for agent workflows, with JSONL journals on the filesystem or S3 and fencing, and a note that a step function may run more than once. The page did not match its title and no other source was found.
- **DBOSify** ([dbosify-py](https://github.com/dbos-inc/dbosify-py)) and **pg_durable** ([microsoft/pg_durable](https://github.com/microsoft/pg_durable)): flagged for a title mismatch in one digest, used as verified in another. README claims to check: DBOSify has "No wire protocol compatibility" and no Temporal Web UI or CLI (0.4.0, 2026-06-24); pg_durable lists 0.2.9 (2026-10-07), ships a Docker image for evaluation only, and says not to use it when "The workflow mostly lives outside Postgres and spans many heterogeneous systems".
- **LangGraph issues** [#9185](https://github.com/langchain-ai/langgraph/issues/9185), [#8039](https://github.com/langchain-ai/langgraph/issues/8039), [#9276](https://github.com/langchain-ai/langgraph/issues/9276), [#7589](https://github.com/langchain-ai/langgraph/issues/7589), [#8464](https://github.com/langchain-ai/langgraph/issues/8464) and [#9270](https://github.com/langchain-ai/langgraph/issues/9270) (resume re-running a tool node, write-ordering after a crash, a cached error write, an idempotency proposal, a DeltaChannel hang): github.com was blocked, so only the repository's existence was confirmed. Read them before quoting.
- **StepKit** (inngest/stepkit), whose npm packages have not passed 0.1.x since 2025-12-01, and four **Mastra issues** on the Inngest runner ([24731](https://github.com/mastra-ai/mastra/issues/24731), [24710](https://github.com/mastra-ai/mastra/issues/24710), [20671](https://github.com/mastra-ai/mastra/issues/20671), [25977](https://github.com/mastra-ai/mastra/issues/25977)) reporting step and state bloat: seen only in search snippets or not fetchable.

## AWS Step Functions

*Managed state-machine service (Amazon States Language or CDK, visual console), Standard and Express workflows. Verdict: Grab for AWS-native, cross-service orchestration with an audit trail; skip it as the inner loop for large LLM payloads.*

**What it is.** A graph service where you declare states and AWS runs them: "Graph-based, Amazon States Language DSL or AWS CDK", with 220+ integrations ([Durable functions or Step Functions](https://docs.aws.amazon.com/lambda/latest/dg/durable-step-functions.html)). On 2026-03-26 AWS added 28 integrations, among them AgentCore ("invoke AI agent runtimes with built-in retries, run multiple agents in parallel using Map states") and the Lambda durable execution APIs, where an execution name makes invocations idempotent ([announcement](https://aws.amazon.com/about-aws/whats-new/2026/03/aws-step-functions-sdk-integrations/)).

| Field | Detail |
|---|---|
| Durability model | Each state transition goes into an execution history capped at 25,000 events ("the execution will fail") and kept 90 days after close. A failed Standard execution can be redriven within 14 days from the failed step with the same input and execution ARN, and "the successful steps ... are not rerun" ([Quotas](https://docs.aws.amazon.com/step-functions/latest/dg/service-quotas.html), [Redrive](https://docs.aws.amazon.com/step-functions/latest/dg/redrive-executions.html)). None of your code replays, since control flow is declared (an inference). |
| Limits | Standard: 1 year, 256 KiB per payload, dispatch up to 100 TPS. Express: 5 minutes, up to 1,000 TPS. A Bedrock `InvokeModel` body above 256 KiB goes through an `S3Uri` in `Input`, and `Output.S3Uri` swaps the response for a reference ([Quotas](https://docs.aws.amazon.com/step-functions/latest/dg/service-quotas.html), [Bedrock integration](https://docs.aws.amazon.com/step-functions/latest/dg/connect-bedrock.html)). |
| HITL | `.waitForTaskToken` pauses until `SendTaskSuccess` or `SendTaskFailure`, for "until the execution reaches the one year service quota" unless `HeartbeatSeconds` is set; Standard only ([patterns](https://docs.aws.amazon.com/step-functions/latest/dg/connect-to-resource.html)). |
| Pricing | Standard $0.000025 per state transition (US East), 4,000 free a month, and "each retry will be charged as an additional state transition"; Express $1.00 per million requests plus $0.00001667 per GB-second. AWS's example: 4 transitions x 100,000 executions = $9.90 a month ([Pricing](https://aws.amazon.com/step-functions/pricing/), 2026-10-08). Derived: $0.025 per 1,000 transitions. |

## AWS Lambda durable functions

*Checkpoint-and-replay durable execution inside ordinary Lambda handlers. Verdict: Grab for durability on AWS without a cluster; mind the 15-minute cap on each invocation.*

**What it is.** Generally available since 2025-12-02 in US East (Ohio): a handler whose steps are checkpointed and retried and whose waits "suspend execution without incurring compute charges" for up to a year; on recovery Lambda "invokes your function again from the beginning and replays the checkpoint log, substituting stored values for completed operations" ([launch](https://aws.amazon.com/about-aws/whats-new/2025/12/lambda-durable-multi-step-applications-ai-workflows/), [Basic concepts](https://docs.aws.amazon.com/lambda/latest/dg/durable-basic-concepts.html)).

| Field | Detail |
|---|---|
| Determinism | "Your code must be deterministic during replay"; keep random numbers and the current time inside steps ([Basic concepts](https://docs.aws.amazon.com/lambda/latest/dg/durable-basic-concepts.html)). |
| Limits | Executions up to one year, each invocation capped at 15 minutes ([Serverless Workers on AgentCore](https://docs.temporal.io/serverless-workers/agentcore)); retention 1 to 90 days, default 14 ([Lambda pricing](https://aws.amazon.com/lambda/pricing/)). Checkpoint size limits were not retrieved. |
| HITL | Waits suspend with no compute charge; AWS's pricing example is a 30-second LLM fraud step followed by a 7-day human-review wait ([Lambda pricing](https://aws.amazon.com/lambda/pricing/)). |
| Pricing | "Existing Lambda compute charges apply, including for sub-invocations from replays", plus $8.00 per million durable operations and $0.25 per GB written (retention rate not captured) ([Lambda pricing](https://aws.amazon.com/lambda/pricing/)). Derived: $0.008 per 1,000 operations. |
| SDKs | `aws-durable-execution-sdk-python` 2.0.1 (2026-09-18), `@aws/durable-execution-sdk-js` 2.6.0 (2026-10-02); the docs list JavaScript, TypeScript, Python and Java ([Lambda durable functions](https://docs.aws.amazon.com/lambda/latest/dg/durable-functions.html)). Pydantic AI co-maintains a Lambda backend ([overview](https://pydantic.dev/docs/ai/capabilities/durable_execution/overview/)). |

## Amazon Bedrock AgentCore

*Managed agent hosting: a microVM per session, plus Memory, Gateway and a managed harness. Verdict: Grab as hosting and tooling; it is session compute, not run memory, so pair it with an engine.*

**What it is.** Each Runtime session runs in its own microVM with ephemeral compute: memory and disk vanish when it stops (15 minutes idle or 8 hours of lifetime by default), while the session ID stays valid and gets fresh compute on the next call ([Use isolated sessions](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/runtime-sessions.html)). The harness makes the agent configuration while AgentCore supplies "a loop that calls the model, picks tools, passes results back" ([harness](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/harness.html)). Temporal's guide to hosting workers there says "Memory does not replace Workflow state or Event History" ([Serverless Workers on AgentCore](https://docs.temporal.io/serverless-workers/agentcore)).

| Field | Detail |
|---|---|
| Durability model | None for the run (a reading of the docs). Persisted: session storage (1 GB max) and Memory events (7 to 365 days); the harness keeps memories and files after a microVM is replaced ([Quotas](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/bedrock-agentcore-limits.html), [harness](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/harness.html)). |
| Limits | Synchronous requests time out at 15 minutes (payload 100 MB); async jobs and sessions up to 8 hours; idle timeout 15 minutes (`idleRuntimeSessionTimeout`), `maxLifetime` 8 hours; 2 vCPU and 8 GB per session; 5,000 active sessions in us-east-1 and us-west-2; Gateway invocations 15 minutes ([Quotas](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/bedrock-agentcore-limits.html)). The Instances type (August 2026) allows sessions up to 14 days and GPUs ([Release notes](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/release-notes.html)). |
| Liveness contract | `/ping` answers `Healthy` or `HealthyBusy`; 15 minutes of `Healthy` ends the session, and if `time_of_last_update` advances on every ping the idle timeout never fires, so sessions run to `MaxLifetime` and can exhaust quota ([Long-running agents](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/runtime-long-run.html)). |
| Pricing | Runtime v1 $0.0895 per vCPU-hour and $0.00945 per GB-hour, with "CPU scales to zero during I/O wait" such as waiting for an LLM; Gateway $0.005 per 1,000 invocations ([Pricing](https://aws.amazon.com/bedrock/agentcore/pricing/), 2026-10-08). Harness hooks (September 2026) let a Lambda allow or deny a tool call, while the Gateway rate-limit system "fails open on transient errors" ([Release notes](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/release-notes.html)). |
| SDKs | `bedrock-agentcore` 1.24.1 (2026-10-07); GA since October 2025; Strands Agents 1.59.0 (2026-10-08) is the AWS-originated fit ([AgentCore](https://aws.amazon.com/bedrock/agentcore/), [Strands](https://strandsagents.com)). |

## Azure Durable Task Scheduler and Microsoft Agent Framework

*A managed replay backend (Durable Task Scheduler, DTS) for Durable Functions and the Durable Task SDKs, plus Microsoft's agent framework whose workflows checkpoint by superstep. Verdict: Grab on Azure; plain framework workflows recover manually, so add the Durable Extension.*

**What it is.** DTS is the "recommended storage provider for Durable Functions and the Durable Task SDKs": gRPC push dispatch, managed state, a built-in dashboard, multi-agent orchestration as a target ([Durable Task Scheduler](https://learn.microsoft.com/en-us/azure/durable-task/scheduler/durable-task-scheduler), 2026-05-05). Microsoft's pitch is token cost: the runtime "checkpoints every state transition (LLM responses, tool call results, control flow decisions)" and completed LLM calls are not repeated ([Durable Task for AI agents](https://learn.microsoft.com/en-us/azure/durable-task/sdks/durable-task-for-ai-agents)). Agent Framework 1.0 shipped 2026-04-03, unifying Semantic Kernel and AutoGen ([Version 1.0](https://devblogs.microsoft.com/agent-framework/microsoft-agent-framework-version-1-0/)).

| Field | Detail |
|---|---|
| Durability model | Two layers. Durable Task replays orchestrations (Astronomer groups it with "replayed orchestration code"). Framework workflows alone checkpoint "at the end of each superstep" into memory, file or Cosmos storage, need the same topology and executor identities on resume, and Microsoft's own table rates their failure recovery "Manual" against "Automatic" for Durable Task ([Checkpoints](https://learn.microsoft.com/en-us/agent-framework/workflows/checkpoints), [Durable Task for AI agents](https://learn.microsoft.com/en-us/azure/durable-task/sdks/durable-task-for-ai-agents)). |
| Limits | Dedicated: 2,000 actions per second per Capacity Unit (up to 3), 50 GB per unit, retention up to 90 days, three units for HA. Consumption: 500 actions per second, retention up to 30 days. Task hubs share a scheduler, so a heavily loaded one "can affect the performance of other task hubs" ([Billing](https://learn.microsoft.com/en-us/azure/durable-task/scheduler/durable-task-scheduler-billing), [DTS](https://learn.microsoft.com/en-us/azure/durable-task/scheduler/durable-task-scheduler)). |
| HITL | The Durable Extension can "Pause for human input or external events without consuming compute or model tokens"; it runs on Azure Functions or your own compute, installs with `--pre`, and has Go support "coming soon" ([Durable Extension](https://learn.microsoft.com/en-us/agent-framework/hosting/azure-functions), 2026-08-31). |
| Pricing | Dedicated is a fixed price per Capacity Unit; Consumption bills each dispatch of an orchestrator, activity or entity as an action; the per-action price was not captured ([Billing](https://learn.microsoft.com/en-us/azure/durable-task/scheduler/durable-task-scheduler-billing)). |
| SDKs | `durabletask` (Python) 1.11.0 (2026-09-29); `agent-framework` 1.21.0 (2026-10-08), MIT; no GA date was found for DTS or the Durable Extension ([DTS](https://learn.microsoft.com/en-us/azure/durable-task/scheduler/durable-task-scheduler), [overview](https://learn.microsoft.com/en-us/agent-framework/overview/)). |
| Harness | Released 2026-07-22, it saves "chat history ... after every model call for crash recovery", cheap protection against paying twice but not an engine ([Harness](https://devblogs.microsoft.com/agent-framework/the-microsoft-agent-framework-harness-is-now-released/)). |

## Dapr Workflows, Dapr Agents and Diagrid Catalyst

*A CNCF sidecar with an event-sourced replay engine (Dapr Workflows), a Python agent framework on it (Dapr Agents) and a managed Dapr (Diagrid Catalyst) that wraps other frameworks. Verdict: Grab if you are already Kubernetes- and Dapr-native and want tamper-evident histories; the determinism discipline is Temporal's.*

**What it is.** Workflows keep "an append-only log of history events"; the function re-runs from the start with completed tasks returning stored results, which "requires that workflow function code be deterministic", and the docs warn against infinite loops and scheduling thousands of tasks ([Workflow features](https://docs.dapr.io/developing-applications/building-blocks/workflow/workflow-features-concepts/)). Dapr Agents 1.0 was announced GA on 2026-03-23 with "persistent state across more than 30 databases" and SPIFFE identity ([CNCF](https://www.cncf.io/announcements/2026/03/23/general-availability-of-dapr-agents-delivers-production-reliability-for-enterprise-ai/)). The plain `Agent` class is ephemeral and "deprecated as of v1.0.0-rc.1"; `DurableAgent` is backed by a workflow, so its code replays and its logging is replay-aware ([Core concepts](https://docs.dapr.io/developing-ai/dapr-agents/dapr-agents-core-concepts/)).

| Field | Detail |
|---|---|
| Durability model | Replay in the sidecar; Diagrid (the vendor) says "Before executing any workflow step, the runtime creates a durable reminder" ([Diagrid](https://www.diagrid.io/blog/checkpoints-are-not-durable-execution-why-langgraph-crewai-google-adk-and-others-fall-short-for-production-agent-workflows)). Since 1.18 a parent can pass its history to children and activities (`PropagateLineage` or `PropagateOwnHistory`), so agents "carry context (tool calls, model outputs, intermediate decisions) forward without re-prompting expensive calls" ([History propagation](https://docs.dapr.io/developing-applications/building-blocks/workflow/workflow-history-propagation/)). |
| Limits | Deep lineage can approach the default gRPC message limit of 4 MB; Cosmos DB as a state store has "payload and workflow complexity limitations" ([Workflow overview](https://docs.dapr.io/developing-applications/building-blocks/workflow/workflow-overview/)). |
| Verifiable execution (1.18, 2026-06-11) | "Observability tells you what happened. Verifiable Execution helps you prove it" ([CNCF](https://www.cncf.io/blog/2026/06/11/introducing-verifiable-execution-in-dapr-1-18/)). With `WorkflowHistorySigning` each event is signed with the sidecar's SPIFFE identity and verified on load; a tampered history fails with `DAPR_WORKFLOW_HISTORY_TAMPERED`, and "Signing is a one-way commitment" ([Workflow overview](https://docs.dapr.io/developing-applications/building-blocks/workflow/workflow-overview/)). |
| Hosting, releases | Apache 2.0 sidecar; "Catalyst Cloud is free". `daprd` 1.18.7 and `dapr-agents` 1.0.9 (both 2026-10-09) ([Catalyst](https://docs.diagrid.io/getting-started/quickstarts/ai-agents/), [Workflow overview](https://docs.dapr.io/developing-applications/building-blocks/workflow/workflow-overview/)). |
| Integrations | Catalyst lists 13 frameworks, Claude Agents among them, and claims it can "coordinate resumption across instances to avoid duplicate runs" ([Catalyst](https://docs.diagrid.io/getting-started/quickstarts/ai-agents/)); for LangGraph its runner makes each node a durable activity ([vendor post](https://www.diagrid.io/learn/integrating-diagrid-catalyst-langgraph)). |

HITL primitives and versioning are not stated in the sources reviewed.

## Orkes Conductor

*A workflow engine whose workflows are JSON definitions, now compiling agents into durable workflows. Verdict: Consider for guardrails and tool whitelists enforced outside the prompt; every source here is Orkes's own.*

**What it is.** "An agent running on Conductor is now a workflow", accepting agents from LangChain, LangGraph, OpenAI, Vercel and Google ADK ([Conductor Now Runs Agents](https://orkes.io/blog/conductor-now-runs-agents-alongside-your-workflows), 2026-09-01). Its CTO's rule: "the LLM reasons; it does not execute", and "A guardrail that runs because the model chose to invoke it is not a guardrail". An agent compiles to a ReAct loop of `LLM_CHAT_COMPLETE` and `FORK_JOIN_DYNAMIC` tasks, an undeclared tool is rejected, and "LLM-written plans compile or they don't run" ([Agents on Conductor](https://orkes.io/blog/agents-on-conductor-architecture-for-production-ai), 2026-08-26).

| Field | Detail |
|---|---|
| Durability model | Server-side definitions; the loop is a `DO_WHILE` with a `loopCondition`. Four properties of a durable loop: the runtime owns the counter, each iteration is checkpointed, boundaries are idempotent, termination is explicit. Restarting a while loop "doubles the token usage, and is unsafe wherever an earlier step already had an effect"; "durability is not continuity" ([Durable Loops, Part 1](https://orkes.io/blog/building-durable-loops-with-conductor-part-1), 2026-06-30). Workflows are data, so no replayed user code (an inference). |
| HITL | `HUMAN` tasks, and the `AGENT` task (2026-09-07) calls an external agent over A2A by default: "your agent is just another step", with retries, durable state and approvals ([AGENT task](https://orkes.io/blog/introducing-agent-task)). Approvals can wait for days ([Conductor Now Runs Agents](https://orkes.io/blog/conductor-now-runs-agents-alongside-your-workflows)). |
| Hosting, releases | Conductor OSS is Apache 2.0; Orkes sells a free Developer Edition and an Enterprise edition, prices not captured. Docker Hub: newest non-rc tag 3.32.5 (2026-09-25), 3.33.0-rc11 pushed 2026-10-05, and `latest` points at 3.33.0-rc10 ([Conductor Now Runs Agents](https://orkes.io/blog/conductor-now-runs-agents-alongside-your-workflows)). |

Limits, versioning and observability are not stated in the sources reviewed; the vendor cites "a billion workflows a month".

## Mastra

*TypeScript agent and workflow framework with suspend and resume snapshots, durability delegated to Inngest or Temporal. Verdict: Grab with Inngest underneath for real durability; pin dependencies.*

| Field | Detail |
|---|---|
| Durability model | Snapshot on suspend, not a journal of every step; whether a run that was never suspended survives a crash on the default runner was not confirmed. Under the Inngest runner Inngest's rules apply, including that a renamed step re-runs (see [Inngest](#inngest)). |
| Supply chain | On 2026-06-17 over 140 `@mastra/*` packages were republished with an injected typosquatted dependency that installed an infostealer; the packages' own code was unmodified ([Socket](https://socket.dev/blog/mastra-npm-packages-compromised)). |

Determinism, limits and versioning are not stated in the sources reviewed.

## CrewAI Flows

*Role-based multi-agent framework whose Flows persist state with `@persist` and whose crews checkpoint by event. Verdict: Prototypes; put an engine under hours-long runs, because restore can start fresh silently.*

**What it is.** `@persist` saves Flow state (SQLite by default) at class or method level so a flow can reload after a restart or fork a new run ([Flows](https://docs.crewai.com/v1.15.27/en/concepts/flows), 2026-10-09). Checkpoints are event-driven: one per completed task by default (`task_completed`), finer events such as `llm_call_completed` available, restoring skips completed tasks, forks start a new lineage, `JsonProvider` and `SqliteProvider`, `max_checkpoints` prunes the oldest ([Checkpointing](https://docs.crewai.com/v1.15.27/en/concepts/checkpointing)).

| Field | Detail |
|---|---|
| Silent failure modes | "If the supplied restore_from_state_id does not match any persisted state, the kickoff falls back silently" ([Flows](https://docs.crewai.com/v1.15.27/en/concepts/flows)); automatic checkpoint writes are best-effort: "a failed write is logged and the run continues" ([Checkpointing](https://docs.crewai.com/v1.15.27/en/concepts/checkpointing)). |
| Hosting, releases | MIT; a CrewAI Enterprise plan exists, its restart behaviour known only from Diagrid's claim. `crewai` 1.15.27 (2026-10-09); registry data in the research digest shows 1.15.24 on 2026-10-07, four stable releases in three days ([Flows](https://docs.crewai.com/v1.15.27/en/concepts/flows)). Agent defaults cited by Diagrid: `max_retry_limit` 2, `max_iter` 20. |

Limits, HITL and versioning are not stated in the sources reviewed.

## OpenAI Agents SDK

*A lightweight agent SDK (agents, handoffs, guardrails, sessions) with `RunState` for pauses and four documented durable-engine integrations. Verdict: A good agent API; its sessions are not execution durability, so add an engine.*

**What it is.** The docs list four durable integrations: Dapr, Temporal, Restate and DBOS. Restate "requires Restate's single-binary runtime", DBOS "requires only a SQLite or Postgres database", and a paused run resumes through `RunState` ([Running agents](https://openai.github.io/openai-agents-python/running_agents/)). `openai-agents` 0.23.1 (2026-10-02), `@openai/agents` 0.20.0 (2026-10-08), MIT.

| Field | Detail |
|---|---|
| Durability model | None in the loop. Sessions keep client-side history (`SQLiteSession`, `SQLAlchemySession`, `RedisSession`, `MongoDBSession`, `DaprSession`, `EncryptedSession`, `OpenAIConversationsSession`); a session "cannot be combined with" `conversation_id` or `previous_response_id` ([Sessions](https://openai.github.io/openai-agents-python/sessions/)). |
| HITL | `needs_approval` (bool or async predicate), pending approvals in `RunResult.interruptions`, resume with `state.approve(...)` ([HITL](https://openai.github.io/openai-agents-python/human_in_the_loop/)). In a September 2026 paper on approval hijacking, SDK 0.22.0 and 0.22.2 were the negative control with exact per-call binding ([Loopjacking](https://arxiv.org/abs/2609.21081)). |
| Engines | Temporal GA 2026-03-23: "model invocations are automatically routed through Temporal activities, while the logic that coordinates them lives in the workflow"; `temporalio-openai-agents` 1.1.0 (2026-10-01) ([README](https://raw.githubusercontent.com/temporalio/ai-integrations/HEAD/python/openai_agents/README.md)); model Activities default to a 60 s timeout and `SQLiteSession` is unsupported because it keeps history in a local file ([guide](https://docs.temporal.io/develop/python/integrations/openai-agents)). Restate offers `DurableRunner` ([Restate](https://restate.dev/blog/durable-orchestration-for-ai-agents-with-restate-and-openai-sdk)); `dbos-openai-agents` 0.3.0 (2026-02-02) ([DBOS](https://docs.dbos.dev/integrations/openai-agents)). |

Limits, versioning and observability are not stated in the sources reviewed.

## Google ADK

*Google's Agent Development Kit: 2.0 added a graph Workflow Runtime, resumability by `invocation_id` and pluggable session services. Verdict: Fine for Gemini and Vertex shops; resume is caller-triggered and at-least-once, and behaviour changed in a minor release, so pin and test.*

**What it is.** ADK 2.0 moves "from a hierarchical agent executor to a graph-based execution engine"; Python 2.0 GA 2026-05-19, Go 2026-06-30, TypeScript 2026-08-21 ([ADK 2.0](https://adk.dev/2.0/)). With `ResumabilityConfig(is_resumable=True)` ADK records completed events and resumes by `invocation_id`; Loop agents restore via `times_looped`. Tools "are run at least once, and may run more than once when resuming"; "Do not modify a stopped agent workflow before resuming it"; resuming from the Web UI or CLI "is not currently supported" ([Resume](https://adk.dev/runtime/resume/)).

| Field | Detail |
|---|---|
| Behaviour change | 2.9.0 (2026-09-10): "A node that failed now runs again when the workflow resumes, where before it replayed as though it had completed"; "Make node bodies idempotent". `InMemorySessionService` previously "accepting the event and discarding it" now raises; 2.11.0 (2026-10-01) adds tool confirmation ([CHANGELOG](https://raw.githubusercontent.com/google/adk-python/HEAD/CHANGELOG.md)). Its persistence is "None. All conversation data is lost if the application restarts." ([Session](https://adk.dev/sessions/session/)). |
| Releases | Apache 2.0; `google-adk` 2.11.0 (2026-10-02), `@google/adk` 2.2.1 (2026-10-06) ([ADK 2.0](https://adk.dev/2.0/)). |
| Engines | Restate `RestatePlugin()` ([Restate](https://restate.dev/blog/build-resilient-ai-agents-with-restate-and-google-adk)); Temporal's ADK plugin is pre-release 0.0.1 ([ai-integrations](https://github.com/temporalio/ai-integrations)). |
| Version conflict | The docs say resume support landed in Python v1.16.0 ([Resume](https://adk.dev/runtime/resume/)); Diagrid says 1.14.0 ([Diagrid](https://www.diagrid.io/blog/checkpoints-are-not-durable-execution-why-langgraph-crewai-google-adk-and-others-fall-short-for-production-agent-workflows)). Prefer the docs. |

## Pydantic AI

*Typed agent framework with eight durable-execution backends and a builder for others. Verdict: Grab to keep the engine swappable; probably the cleanest thin-controller abstraction available today.*

**What it is.** "Pydantic AI supports eight durable execution solutions, plus a builder for any other engine": Temporal, DBOS, Prefect, Restate and AWS Lambda are co-maintained with the vendors; Kitaru, Apache Airflow and Absurd are external. `pydantic-ai` 1.0.0 (2025-09-05), 2.0.0 (2026-06-23), 2.55.0 (2026-10-09), MIT ([overview](https://pydantic.dev/docs/ai/capabilities/durable_execution/overview/)). Samuel Colvin's pitch: "restarting means paying for completed computation again" ([AI Engineer talk](https://ai.engineer/talks/flf_IKnFYnE-from-stateless-nightmares-durable-agents), posted 2025-11-24).

| Field | Detail |
|---|---|
| Temporal | "model requests, tool calls that may require I/O, and MCP server communication all need to be offloaded to Temporal activities"; an activity that fails part-way "is restarted from the beginning"; payloads are limited to 2 MB by default and over-limit failures are non-retryable ([Temporal](https://pydantic.dev/docs/ai/capabilities/durable_execution/temporal/)). |
| DBOS, Prefect | DBOS stores "Every workflow input and step output" in Postgres or SQLite ([DBOS](https://pydantic.dev/docs/ai/capabilities/durable_execution/dbos/)). Prefect: "If the task runs again under an identical context, it will not re-execute but instead load its previous result"; disable provider-client retries because "The layers multiply" ([Prefect](https://pydantic.dev/docs/ai/capabilities/durable_execution/prefect/)). |
| Harness | `pydantic-ai-harness` 0.55.0: Coder, Shell, FileSystem and RepoContext run under Temporal, DBOS and Prefect, and "every activity or step reattaches to the environment it names, whichever worker runs it" ([Harness](https://pydantic.dev/docs/ai/harness/durable-execution/)). |

Limits, versioning and observability are whatever the engine provides.

## DAG schedulers

DAG schedulers were built for batch data work: a graph known in advance, tasks that run for minutes. Agent pipelines break both (the next step is unknown, one step can run for hours), yet in 2026 every major scheduler grew a durability story inside the task. The useful comparison is where each puts the boundary: a task attempt (Airflow), a cached task result (Prefect), a named replayed action (Flyte 2), a checkpointed script (Windmill), a declared flow (Kestra) ([Astronomer](https://www.astronomer.io/blog/durable-execution-with-apache-airflow/), [Inside Union](https://www.union.ai/blog-post/inside-union-the-replay-log-that-makes-flyte-2-durable)). Anthropic's quick test: "Workflows are systems where LLMs and tools are orchestrated through predefined code paths", agents are "systems where LLMs dynamically direct their own processes and tool usage"; a DAG fits the first ([Building Effective AI Agents](https://www.anthropic.com/engineering/building-effective-agents)).

| Case | Evidence |
|---|---|
| Fits: bounded fan-out | Airflow's survey DAG has seven tasks, four in parallel, two LLM call types and one human review; "Only run_query[2] retries; other three results preserved", where in an agent harness the "Entire run restarts from the top, or fails" ([Agentic Workloads on Airflow](https://airflow.apache.org/blog/agentic-workloads-airflow-3/)). |
| Fits: deterministic first, LLM for the residue | Dagster's classifier: rules handle "95%+ of matches" ([case](https://dagster.io/blog/classifying-a-million-snowflake-columns-in-9-days-solo-with-dagster)); Kestra's triage: "Five of those six steps" are deterministic ([Kestra](https://kestra.io/blogs/ai-workflows-in-production)). |
| Does not: unknown path | Dragonfly dropped static DAGs for about 190 steps and about 95 LLM calls per product, "but the path is different every time" (vendor case) ([Dragonfly](https://www.union.ai/case-study/how-dragonfly-scales-agentic-research-across-250k-products)). |
| Does not: a critic that loops back | "Airflow DAGs are acyclic": a task retry is "automatic but blunt", a branch is "wired in at authoring time" ([Airflow](https://airflow.apache.org/blog/agentic-workloads-airflow-3/)). |

The category is consolidating: Anaconda announced buying Outerbounds (Metaflow) on 2026-04-29, Prefect announced buying Dagster Labs on 2026-07-13, `airflow-ai-sdk` was discontinued in favour of the Apache `common.ai` provider (final 0.1.8 on 2026-07-01), and ControlFlow was archived into Marvin ([Anaconda](https://www.anaconda.com/press/anaconda-acquires-outerbounds), [Prefect](https://www.prefect.io/prefect-acquires-dagster), [PyPI](https://pypi.org/project/airflow-ai-sdk/), [ControlFlow](https://github.com/PrefectHQ/ControlFlow)).

| Engine | Durability boundary | Loops | Human gate | Latest release |
|---|---|---|---|---|
| [Apache Airflow 3](#apache-airflow-3) | Task attempt plus a state store | Acyclic | `HITLOperator` | 3.3.2 (2026-09-17) |
| [Prefect](#prefect) | Cached result at a key | Dynamic Python flows | Review pauses | 3.8.8 (2026-10-06) |
| [Dagster](#dagster) | Assets and runs | Not an agent runtime | Not stated | 1.13.26 (2026-10-09) |
| [Kestra](#kestra) | Flow and task retries | `Loop` task | `Pause`, gated steps | v2.0.5 (2026-10-05) |
| [Windmill](#windmill) | Checkpoint and replay | Not stated | `waitForApproval` | 1.830.0 (2026-10-09) |
| [Flyte 2](#flyte-2) | Replay log of named actions | Plain async Python | Not stated | 2.11.2 (2026-10-09) |
| [Metaflow](#metaflow) | Step boundary, artifacts | Recursive steps | Not stated | 2.19.39 (2026-09-02) |
| [Argo Workflows](#argo-workflows) | ConfigMap memoization | DAG or steps | Not stated | v4.1.5 (2026-10-09) |

Each cell is sourced in its profile below.

### Apache Airflow 3

*Python DAG scheduler; 3.x added the Task SDK, event-driven scheduling, HITL operators and, in 3.3, a task state store and resumable operators. Verdict: Grab for batch fan-out, scheduled evals and bounded LLM steps in known pipelines; not the outer loop of a cyclic, hours-long agent run.*

**What it is.** 3.0 went GA on 2025-04-22 with event-driven scheduling and DAG versioning (a run finishes "based on the version at start") ([Airflow 3](https://airflow.apache.org/blog/airflow-three-point-oh-is-here/)); 3.1.0 (2025-09-25) added HITL operators ([3.1.0](https://airflow.apache.org/blog/airflow-3.1.0/)); 3.3.0 (2026-07-06) added a task and asset state store (AIP-103), resumable operators, pluggable retry policies (AIP-105) and an `awaiting_input` state ([3.3.0](https://airflow.apache.org/blog/airflow-3.3.0/)). The `common.ai` provider (0.1.0, 2026-04-14) is built on Pydantic AI, with "breaking changes ... possible between minor versions" ([launch](https://airflow.apache.org/blog/common-ai-provider/)).

| Field | Detail |
|---|---|
| Durability model | Task attempts. A dead worker is caught after `task_instance_heartbeat_timeout` (300 s), `default_task_retries` is 0, and XComs are cleared on every retry, "which is why XCom was never a crash-recovery store" ([Tasks](https://airflow.apache.org/docs/apache-airflow/stable/core-concepts/tasks.html), [Configuration](https://airflow.apache.org/docs/apache-airflow/stable/configurations-ref.html), [XComs](https://airflow.apache.org/docs/apache-airflow/stable/core-concepts/xcoms.html), [Astronomer](https://www.astronomer.io/blog/durable-execution-with-apache-airflow/)). |
| Resumable operators | Tier 1 operators (Spark, Databricks, BigQuery, Snowflake, Glue, KubernetesPodOperator) default to `durable=True`, so a retry reconnects to the running job; for agents it "defaults to False, so this is opt-in"; the state store keeps 30 days by default ([3.3.0](https://airflow.apache.org/blog/airflow-3.3.0/), [Astronomer](https://www.astronomer.io/blog/durable-execution-with-apache-airflow/)). |
| Agent replay (`durable=True`) | Caches each model response and tool result under a fingerprint of the request (model, history, settings, tools) and re-runs on any mismatch: "A changed agent costs a re-run; it never replays responses that belong to a different conversation." But "Durable execution caches return values, not side effects", it "only helps when the task has retries configured", and a tool that sent an email then raised "may produce the side effect a second time" ([Durable execution](https://airflow.apache.org/docs/apache-airflow-providers-common-ai/stable/durable_execution.html)). Worker writes above 64 KB "are stored anyway", so a large tool result can land in the metadata database ([Pydantic AI on Airflow](https://pydantic.dev/docs/ai/capabilities/durable_execution/airflow/)). |
| HITL | `HITLOperator` pauses a task with a form and a REST API answers it; timeouts are enforced by the scheduler, so a task parked under `airflow dags test` waits indefinitely ([tutorial](https://airflow.apache.org/docs/apache-airflow/stable/tutorial/hitl.html)). |
| `common.ai` 0.10.0 | Per-run cost limits, a decision policy that asks a person when the model is unsure, provider failover, and a refusal of durable replay when an agent holds a `SandboxToolset` ([changelog](https://airflow.apache.org/docs/apache-airflow-providers-common-ai/stable/changelog.html)). The classifier retry policy's model call "is not subject to an operator's usage_limits" ([Retry policies](https://airflow.apache.org/docs/apache-airflow-providers-common-ai/stable/retry_policies.html)). |
| Releases | Airflow 3.3.2 (2026-09-17), Apache 2.0 ([Airflow](https://airflow.apache.org/)); `common-ai` 0.10.0 (2026-09-29), 0.11.0rc1 (2026-10-08). |

### Prefect

*Python orchestrator with dynamic flows, transactional tasks and results addressed by cache key; open source with a paid Cloud. Verdict: A scheduler, not a durable-execution engine.*

**What it is.** Prefect argues durability should be decomposed from workflow identity: "Results are addressable by cache key, not by which workflow produced them", with a lock manager, `on_rollback` hooks and transactions that "don't commit their cache records until the entire transaction succeeds" ([Decomposed Durability](https://www.prefect.io/blog/decomposed-durability-a-different-approach-to-durable-execution), 2025-12-15). Its 2026-07-13 deal for Dagster Labs ("Dagster defines outcomes. Prefect executes the work. FastMCP governs access") "has yet to formally close" per The New Stack, and whether it has since was not confirmed ([Prefect](https://www.prefect.io/prefect-acquires-dagster), [The New Stack](https://thenewstack.io/prefect-acquires-dagster-orchestrator/)).

| Field | Detail |
|---|---|
| Durability model | "Caching requires result persistence, which is off by default"; the default key hashes inputs, task code and "the prevailing flow run ID" ([Caching](https://docs.prefect.io/v3/concepts/caching)). On Kubernetes local results do not survive a retry in a new pod ([Results](https://docs.prefect.io/v3/advanced/results)). No replay, so no non-determinism errors. |
| Timestamp trap | The default key broke on Pydantic AI because "each model call and tool invocation includes a timestamp"; the fix is a custom policy on prompt, model name and tool parameters. Their example: a GPT-5 call of about 1,000 tokens costs roughly $0.006 ([Prefect and Pydantic AI](https://www.prefect.io/blog/prefect-pydantic-integration), 2025-11-04). |
| Failure detection | Heartbeats default to 180 s (30 s is the minimum); the self-hosted zombie automation marks a run Crashed after 90 s of silence at 30 s heartbeats, Cloud's managed detection takes 9 minutes, and the automation only marks the run Crashed ([Zombie flows](https://docs.prefect.io/v3/advanced/detect-zombie-flows)). |
| Releases | `prefect` 3.8.8 (2026-10-06), whose fix "Don't reschedule a flow run the server already finished on SIGTERM" ([3.8 notes](https://docs.prefect.io/v3/release-notes/oss/version-3-8)); Apache 2.0, Cloud tiers not priced in the sources reviewed. |

HITL, limits and versioning are not stated; Prefect's own questionnaire flow uses human review pauses at "less than $1 per assessment" ([case](https://www.prefect.io/blog/running-agentic-security-questionnaires-with-prefect-cloud)).

**Great at.** Plain decorators with no determinism rules, a cache key that is a lambda you control, and a one-command retry. **Painful at.** The defaults (persistence off, a key that includes the flow-run id), no detection of a dead run unless you write the automation, and a deploy is a crash. **Known criticisms.** No independent critique found; Prefect Horizon's pitch on tool-call visibility, "leaving the agent to make something up and hope nobody checks", is a vendor's description of the problem ([Horizon](https://www.prefect.io/blog/ai-agent-representation-comes-to-horizon)). **Links.** [Caching](https://docs.prefect.io/v3/concepts/caching) · [Zombie flows](https://docs.prefect.io/v3/advanced/detect-zombie-flows)

### Dagster

*Asset-centric data orchestrator; its 2026 AI work helps coding agents write Dagster, not run agents. Verdict: Grab for data assets and batch LLM classification around agents; skip as a runtime for agent loops.*

### Kestra

*Declarative YAML orchestrator with an AI Agent task; 2.0 rebuilt the engine and makes any flow an MCP tool. Verdict: Grab to expose audited, deterministic workflows as agent tools and for retry and fallback around LLM steps; replay of model calls inside an agent task was not confirmed.*

**What it is.** Kestra 2.0 (2026-09-08): workers connect "over a single outbound gRPC stream", "The queue is independent of the database now", an MCP Tool Trigger and Server (OSS, Enterprise and Cloud), an agentic Copilot and action-based RBAC with `REPLAY` and `RESUME` permissions (Enterprise and Cloud only); PyPI `kestra` 2.0.0 (2026-09-07), Docker Hub v2.0.5 (2026-10-05) ([2.0](https://kestra.io/blogs/release-2-0)). A flow with `McpToolTrigger` becomes a tool, each agent call is an ordinary audited execution labelled `system.from: mcp`, "An agent calling Kestra has no more privilege than a person clicking Run", and behind a gated step "The agent is told the work is pending rather than done" ([flows as tools](https://kestra.io/blogs/2026-09-04-kestra-2-0-flows-as-agent-tools)).

| Field | Detail |
|---|---|
| Failure handling | A triage flow where five of six steps are deterministic added a chain of three models (`runIf`, `allowFailure: true`), a `PT1S` retry and concurrency 10 after its preview model "goes UNAVAILABLE about once every other day"; "The fix took 15 minutes" ([Kestra](https://kestra.io/blogs/ai-workflows-in-production)). |
| HITL | The `Pause` task: "execution pauses here until a human resumes it from the UI or API"; "A tool failure is not a workflow failure" ([agents](https://kestra.io/blogs/orchestrate-ai-agents-kestra)). |
| Hosting, pricing | Apache 2.0 open source; Enterprise and Cloud paid, prices not in the sources reviewed ([2.0](https://kestra.io/blogs/release-2-0)). |

**The comparable that matters.** Kestra's plugin team automated its own SDLC (plan, implement, review, QA, PR) with Claude Code skills, sub-agents and approval gates: a medium feature in about 30 minutes against about 4 hours (about 8x), at $2.70 per issue unoptimized and $1.65 optimized, planning on Opus and implementation on Sonnet, "A five-cycle retry limit prevents infinite loops", over 100 real issues. The loop is driven by skills, not by a Kestra flow ([Context Engineering in Practice](https://kestra.io/blogs/context-engineering-plugins-squad)). Vendor-reported, narrow scope.

### Windmill

*Self-hostable scripts and flows platform with AI agent steps and, since April 2026, checkpoint and replay workflows as code. Verdict: Grab for internal automations with cheap waits and approvals; evaluate replay semantics before long agent loops.*

### Flyte 2

*A "durable, open-source AI runtime": plain async Python, a replay log of named actions, `@flyte.trace` for model calls and infrastructure-aware retries. Verdict: Consider for GPU-heavy or massively parallel agent fleets on Kubernetes, if you accept Union.ai's backend or wait for the open-source one.*

**What it is.** GA on 2026-08-04, a ground-up rebuild that drops the static graph; "most of what actually breaks a long running AI workload isn't a bug, it's an infrastructure failure", and retries change resources after an OOM or preemption. It is a Linux Foundation project, but the repository README says "The open source backend for Flyte 2 is coming soon", with the enterprise backend from Union.ai and the SDK under Apache 2.0 ([Flyte 2 GA](https://www.union.ai/blog-post/flyte-2-is-generally-available-the-durable-open-source-ai-runtime)).

| Field | Detail |
|---|---|
| Durability model | A write-ahead log: action name = `base36(md5(parent_action_name + hash(canonical_inputs) + task_identity + call_sequence + group))`, where task identity is the qualified name, interface and a hash of the function body, and excludes the image, resources and environment. On recovery "the start time is pinned to a constant rather than now()", and editing a function body re-runs the task ([Inside Union](https://www.union.ai/blog-post/inside-union-the-replay-log-that-makes-flyte-2-durable), 2026-09-26). |
| Model calls | `@flyte.trace`: "the log records what the model said the first time, and the second attempt reads the log instead of asking again". |
| Claude adapter | `flyteplugins-agents-claude` (announced 2026-10-08): `@tool` on `@env.task(cache='auto', retries=3)` makes each tool a containerized durable task, and "the adapter mirrors the Claude session onto a Flyte checkpoint, so a retried task resumes the conversation instead of restarting it"; `report=True` includes "the SDK's cost estimate". The same surface covers ten frameworks, kept identical by a CI conformance test; the package first appeared on PyPI 2026-07-13 ([Claude adapter](https://www.union.ai/blog-post/run-claude-agent-sdk-agents-on-flyte-with-every-tool-as-a-durable-task)). |
| Scale case (vendor) | Dragonfly: 250,000 products, a spot interruption at step 187 of 190 resumes from 187, redundant calls cost $2 to $3 per research run ([Dragonfly](https://www.union.ai/case-study/how-dragonfly-scales-agentic-research-across-250k-products)). |
| Releases | `flyte` 2.0.0 (2026-02-18), 2.11.2 (2026-10-09); Flyte 1's `flytekit` 1.16.29 is static, so skip it for new agent work ([flytekit](https://pypi.org/project/flytekit/)). |

### Metaflow

*Netflix-born ML framework; since 2.18 it supports conditional and recursive steps so agent loops can live in flows. Verdict: Grab for ML-team agent fleets that need artifact versioning; resume is at step boundaries.*

### Argo Workflows

*Kubernetes-native workflow engine (DAG and steps) with ConfigMap memoization and nothing agent-specific. Verdict: A substrate, not an agent orchestrator.*

## Part 2 at a glance

Prices where the sources reviewed have them, per 1,000 billable units (units are not comparable, as in part 1). The open-source schedulers and frameworks have free cores, and the prices of their paid tiers are not in the sources reviewed; nor is Azure's per-action price.

| Product | Billable unit and list price | Per 1,000 (derived) | Also billed |
|---|---|---|---|
| Step Functions Standard | State transition, $0.000025 ([pricing](https://aws.amazon.com/step-functions/pricing/)) | $0.025 | Each retry; 4,000 free a month |
| Step Functions Express | Request, $1.00 per million ([pricing](https://aws.amazon.com/step-functions/pricing/)) | $0.001 | $0.00001667 per GB-second |
| Lambda durable functions | Durable operation, $8.00 per million ([pricing](https://aws.amazon.com/lambda/pricing/)) | $0.008 | Compute including replays; $0.25 per GB written |
| AgentCore Gateway | Invocation, $0.005 per 1,000 ([pricing](https://aws.amazon.com/bedrock/agentcore/pricing/)) | $0.005 (stated) | Runtime per vCPU-hour; CPU idle during I/O wait is free |

## Cross-engine notes

### One bug, three engines: the cache key

| Engine | What identifies a step | What goes wrong |
|---|---|---|
| Airflow batch | Model, messages, token cap | A wall-clock timestamp in the input pays "for a new batch each time" ([docs](https://airflow.apache.org/docs/apache-airflow-providers-common-ai/stable/operators/llm_batch.html)) |
| Flyte 2 | Parent, inputs, function-body hash | Start time pinned on recovery; an edited body re-runs ([Inside Union](https://www.union.ai/blog-post/inside-union-the-replay-log-that-makes-flyte-2-durable)) |

### Human gates

Waiting for a person is nearly free in compute: Lambda durable waits incur no compute charge, the Durable Extension pauses "without consuming compute or model tokens", Windmill's `waitForApproval` frees the worker, Conductor approvals can wait days. Leaving gates unbuilt is a design choice, not a cost constraint. The traps are in defaults: a Step Functions task token waits up to a year without `HeartbeatSeconds`, and an Airflow timeout exists only while the scheduler that enforces it runs ([Step Functions](https://docs.aws.amazon.com/step-functions/latest/dg/connect-to-resource.html), [Airflow](https://airflow.apache.org/docs/apache-airflow/stable/tutorial/hitl.html)). See [Human in the Loop](human-in-the-loop.md).

### What the sources reviewed do not settle

No independent benchmark compares recovery across these products; the strongest comparative critiques are vendor posts and every scheduler cost figure is vendor or self-reported. Unconfirmed: whether the Prefect and Dagster deal has closed; whether Kestra's or Windmill's agent steps replay completed model calls; whether Mastra's default runner survives a crash of a never-suspended run; the Durable Task Scheduler Consumption price; Lambda durable checkpoint limits; AgentCore harness cost controls; the Durable Extension's GA date.

## Related

- [Official Docs by Engine](official-docs-by-engine.md)
- [Comparisons and Benchmarks](comparisons-and-benchmarks.md)
- [Tools Radar](tools-radar.md)
- [Human in the Loop](human-in-the-loop.md)
- [Cost Economics](cost-economics.md)
