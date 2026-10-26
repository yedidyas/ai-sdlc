# Cost Economics

## How to read this note

- **Freshness.** Prices are from vendor pages read in October 2026 and they move (Anthropic changed Sonnet 5.5 cache reads on October 7, 2026). Re-read the live page before quoting a price.
- **Labels.** "Estimate" is arithmetic on a published unit price and a stated assumption. "(vendor)" marks a source that sells a compared product. "(self-reported)" marks numbers posted by the people who incurred them.

## 1. The price sheet, October 2026

### 1.1 Per-million-token list prices

USD per 1M tokens at standard (non-batch) rates; "n/a" means the vendor page did not state it.

| Model (released) | Input | Output | Cache read | Notes |
|---|---|---|---|---|
| Claude Fable 5.1 (Sep 1, 2026) | $10 | $50 | $0.25 | Top tier; read multiplier 0.025x. Source: [Claude pricing](https://platform.claude.com/docs/en/about-claude/pricing) |
| Claude Opus 5.5 (Sep 22, 2026) | $4 | $20 | $0.20 | 1M context default, 128k max output, thinking always on; fast mode $8 / $40. Source: [launch post](https://www.anthropic.com/claude-opus-5-5) |
| Claude Opus 5 (Jul 24, 2026) | $5 | $25 | $0.50 | The Opus 5.5 post calls its own cache reads 60% cheaper than this |
| Claude Opus 4.5 to 4.8 | $5 | $25 | n/a | Opus 4.7 launched Apr 16, Opus 4.8 May 28, 2026 ([release notes](https://platform.claude.com/docs/en/release-notes/overview)) |
| Claude Sonnet 5.5 (Sep 28, 2026) | $2 | $10 | $0.10 | Reads were $0.20 at launch, cut to $0.10 on Oct 7, 2026. Source: [launch post](https://www.anthropic.com/claude-sonnet-5-5) |
| Claude Haiku 5.5 (Oct 7, 2026) | $0.10 (above 100k-token prompts: $0.50) | $0.50 ($2.50) | $0.01 ($0.05) | First Haiku with an effort setting; for narrow subagent work. Source: [launch post](https://www.anthropic.com/claude-haiku-5-5) |
| GPT-6 Astra (Sep 3, 2026) | $10 | $50 | $1.00 | Cache writes $12.50; Ultrafast tier $60 / $300. Source: [OpenAI pricing](https://developers.openai.com/api/docs/pricing) |
| GPT-6.1 Sol (Sep 29, 2026) | $2 | $10 | $0.10 | Cache writes $2.50; short-context tier is prompts up to 272K ([changelog](https://developers.openai.com/api/docs/changelog)) |
| GPT-6 Luna (Sep 22, 2026) | $0.10 | $0.50 | $0.01 | Cheapest OpenAI reasoning model ([models](https://developers.openai.com/api/docs/models)) |
| Gemini 3.8 Flash (Sep 2, 2026) | $0.75 through Dec 31, 2026, then $1.50 | $3.75, then $7.50 | $0.075, then $0.15 | Plus explicit-cache storage $0.50 per 1M per hour, rising to $1.00 on Jan 1, 2027 ([Gemini pricing](https://ai.google.dev/gemini-api/docs/pricing)) |
| Grok 4.7 (Sep 21, 2026) | $2 (above 200k prompts: $4) | $6 ($12) | $0.50 ($1) | 500k context ([xAI release notes](https://docs.x.ai/developers/release-notes)) |
| DeepSeek V4.1-Flash (Sep 10, 2026) | $0.30 peak, $0.15 off-peak | $1.20, $0.60 | $0.006, $0.003 | Open weights (MIT per the model card); peak hours 01:00-04:00 and 06:00-10:00 UTC on weekdays ([DeepSeek pricing](https://api-docs.deepseek.com/quick_start/pricing)) |
| GLM-5.3 (Aug 18, 2026) | $1.40 | $4.40 | $0.26 | GLM-5.3-Flash (Aug 26) is $0.15 / $0.50 / $0.03 ([Z.ai notes](https://docs.z.ai/release-notes/new-released)) |

### 1.2 Modifiers that change the sheet

- **Batch.** [Claude's Batch API takes 50% off](https://platform.claude.com/docs/en/about-claude/pricing) input and output: a candidate for any phase that can wait.
- **Tokenizer.** Claude 4.7 and later [produce about 30% more tokens for the same text](https://platform.claude.com/docs/en/about-claude/pricing), so comparing per-token prices across the 4.6 to 4.7 boundary understates the newer model's cost.
- **Thinking.** [Thinking tokens are billed as output tokens even when the thinking text is not returned](https://platform.claude.com/docs/en/models/opus-5-5/migration-guide), and on Opus 5.5 thinking cannot be switched off. Claude Code's guide says the default thinking budget [can be tens of thousands of tokens per request](https://code.claude.com/docs/en/costs).
- **Prompt-size cliffs.** Haiku 5.5 charges 5x more per token above 100,000-token prompts, and that includes cache reads. Gemini 3.8 Flash doubles on January 1, 2027. GPT-5.6 Sol's promotional pricing is available at least through November 21, 2026 ([OpenAI pricing](https://developers.openai.com/api/docs/pricing)).

> **Re-price before you compare**
> Your cost spreadsheet has a half-life. Between April 16 and October 7, 2026 Anthropic shipped nine generally available models, priced Opus 5.5 cache reads 60% below Opus 5, halved Sonnet 5.5 cache reads, and kept Sonnet 5 at $2 / $10 instead of raising it to $3 / $15 ([release notes](https://platform.claude.com/docs/en/release-notes/overview)). Re-price old runs at the current sheet before comparing, and keep the model a one-line config per phase.

### 1.3 What the sheet says about a phase ladder

Opus 5.5 ($4 / $20), Sonnet 5.5 ($2 / $10) and Haiku 5.5 ($0.10 / $0.50) are a 2x step and then a 20x step per token. Moving a narrow, high-volume phase down the ladder is the first thing that pays, and the easiest to over-apply: Anthropic says [Haiku suits narrowly scoped work such as compaction, summarization or subagents](https://www.anthropic.com/claude-haiku-5-5), and the Terminal-Bench 4.0 board shows the gap: GPT-6 Luna 16.36% and Gemini 3.8 Flash 19.09% against 64.85% for Opus 5.5 ([leaderboard](https://www.tbench.ai/)).

## 2. Prompt caching: prices and hit-rate evidence

### 2.1 Pricing models by vendor

| Vendor | Write | Read | Lifetime | Minimum and limits |
|---|---|---|---|---|
| Anthropic | 5-minute write 1.25x base input; 1-hour write 2x | 0.1x base; 0.05x on Opus 5.5 and Sonnet 5.5; 0.025x on Fable 5.1 and Mythos 5.1 | 5 minutes default, refreshed on each use; 1 hour at 2x | 512 tokens on Opus 5.5, Sonnet 5.5, Haiku 5.5; 2,048 on Opus 4.7; 4,096 on Opus 4.6, Opus 4.5 and Haiku 4.5; 20-block lookback; 4 breakpoints ([prompt caching](https://platform.claude.com/docs/en/build-with-claude/prompt-caching)) |
| OpenAI (GPT-5.6 and later) | 1.25x standard input | 0.1x on most, 0.05x on GPT-6.1 Sol | `prompt_cache_options.ttl` supports only `30m`, counted from the latest write or reuse | 1,024 tokens; up to four cache writes per request ([prompt caching guide](https://developers.openai.com/api/docs/guides/prompt-caching)) |
| Google Gemini | Implicit caching on by default for Gemini 2.5 and newer; explicit caches pay a cache price plus storage | See the model row in the price table | Storage billed per hour: $0.50 per 1M tokens per hour through Dec 31, 2026, $1.00 from Jan 1, 2027 | [Gemini pricing](https://ai.google.dev/gemini-api/docs/pricing) |

Break-even arithmetic from the vendors themselves: on Claude [caching pays off after one cache read for the 5-minute duration, or after two for the 1-hour duration](https://platform.claude.com/docs/en/about-claude/pricing). OpenAI's worked example: [across ten requests, one write and nine full reads cost 2.15x the uncached rate, against 10x without caching](https://developers.openai.com/api/docs/guides/prompt-caching) (1.25 + 9 x 0.1). Two behaviors matter for pipelines. Prompts below the minimum are [processed without caching and no error is returned](https://platform.claude.com/docs/en/build-with-claude/prompt-caching). And [a cache entry only becomes available after the first response begins](https://platform.claude.com/docs/en/build-with-claude/prompt-caching), so parallel requests do not share it.

### 2.2 Hit-rate evidence

| Source | What was measured | Number | Caveat |
|---|---|---|---|
| [Terminal-Bench 4.0 leaderboard](https://www.tbench.ai/) (Oct 6, 2026) | Opus 5.5 in Claude Code at max effort, 330 trials | 8.0B tokens, 7.70B of them cached input (about 96%); $4,718.65 total, about $14.30 per trial | Benchmark tasks, not an SDLC pipeline |
| [Copilot production traces](https://arxiv.org/abs/2608.00101) (arXiv, Jul 30, 2026) | June 2026 sampled traces: 3.2M users, 13M sessions, 761M LLM calls | KV-cache hit rates averaging 90% within a turn, falling to 55% across turn boundaries; collapse after model switches or context compaction | Provider-side KV cache, not the billed-price cache, but the shape transfers |
| [RUNTA FrontierHarness](https://runta.com/blog/introducing-frontierharness-eval/) (Sep 1, 2026) | 360 trials, 9 harnesses on one model | Token-weighted cache hit 92.4%; cost per completed task $1.05 to $18.34 | "Cache hit rate is not cost": failures dominate spend |
| [Revefi](https://www.revefi.com/blog/how-revefi-reduced-its-data-agents-spend) (Sep 23, 2026) | Postmortem on a data agent | A timestamp near the top of the system prompt made every call miss; moving it to the end lifted cache reads to 99% of input and saved about $300K a year on one customer agent | Own account |
| [Viktor](https://viktor.com/research/how-we-built-viktor-around-prompt-caching) (Jun 8, 2026) | 40-step agent thread on Opus 4.8 | Transmits about 2.17M input tokens for an 85K-token transcript; caching plus append-only design cut $11.35 to $2.07 (81.8%) | Own worked example |
| [Replay Doctor](https://replay.doctor/) (build 0.8.0, Oct 5, 2026) | One machine, 982 sessions | 148.6M tokens re-read at write prices, $711.26 at list, 3.5% of $20,148.49 | Self-reported method demonstration, not a survey |
| [HN: a $38k Bedrock bill](https://hn.algolia.com/api/v1/items/47933355) (Apr 28, 2026, self-reported) | Coding-agent chain through a proxy to Bedrock and Opus 4.6 | Gross $37,901.73: about 6.47B uncached input tokens (about $35.6k), 1.67B cache reads (about $918), 101M cache writes (about $698), 25M output (about $698) | One anonymous post, not independently verified |

Two readings. In agent workloads cache reads dominate token count (Anthropic's post: cache reads ["make up the majority of agentic and coding work costs"](https://www.anthropic.com/claude-opus-5-5)). And a high hit rate does not prove a low bill (RUNTA), while a missing cache can run for months unnoticed (the Bedrock post: "prompt caching is supported" is not the same as your stack using it, and "budget alerts are configured" is not the same as spend will stop).

### 2.3 How pipelines lose the cache

- **Timestamps and IDs near the top.** A timestamp in a cached block means [a fresh cache write on every request and never a read](https://platform.claude.com/docs/en/build-with-claude/prompt-caching); a date or per-session UUID in the system prompt is [a cache miss every single time](https://www.theregister.com/ai-ml/2026/05/31/netflix-wiz-creates-app-to-slash-ai-bills-then-open-sources-it/5248702) (Headroom, an open-source proxy by a Netflix engineer, not a Netflix project). Revefi's fix: sort prompt content by volatility.
- **Tool and effort edits.** Modifying tool definitions (names, descriptions, parameters) invalidates the entire cache, and changing `output_config.effort` always invalidates message blocks, with an exception for per-message effort on models that support it ([prompt caching](https://platform.claude.com/docs/en/build-with-claude/prompt-caching)).
- **Model switches and fallbacks.** A router or fallback that moves a conversation to another model writes the new model's cache from scratch. Anthropic's refusal docs describe a fallback credit for exactly this ([refusals and fallback](https://platform.claude.com/docs/en/build-with-claude/refusals-and-fallback)).
- **Waits longer than the TTL.** The five-minute default is shorter than many test runs ([Earendil](https://earendil.com/posts/prompt-caching/)); a phase that waits on CI past it pays a full rewrite on resume. OpenAI's floor is 30 minutes; Anthropic's 1-hour TTL costs 2x on the write.
- **Parallel starts.** Twelve children launched at the same instant each write the same prefix, because [none can read until the first response begins](https://platform.claude.com/docs/en/build-with-claude/prompt-caching).
- **Compaction.** Viktor's rule: [never compact a hot thread, compact aggressively in the minutes before the cache goes cold](https://viktor.com/research/how-we-built-viktor-around-prompt-caching).
- **Observability.** Anthropic's [cache diagnostics](https://platform.claude.com/docs/en/build-with-claude/cache-diagnostics) (beta May 13, 2026; generally available September 23) takes `diagnostics.previous_message_id` and reports a `cache_miss_reason`; log it per phase next to cost per call. Claude Code's `/usage` cache line (v2.1.251 or later) covers the main conversation only, not subagents ([cost guide](https://code.claude.com/docs/en/costs)).

### 2.4 Worked examples (estimates)

**A 40-step thread at Opus 5.5 prices.** Take Viktor's token counts (about 2.17M input tokens transmitted, an 85K-token transcript) and the Opus 5.5 sheet ($4 input, $5 cache write, $0.20 cache read per 1M). Uncached: 2.17M x $4 = $8.68 of input. Cached with an append-only prefix: 85K written ($0.43) plus 2.085M read ($0.42) is about $0.84, roughly a tenfold cut on the input line. Now put a timestamp at the top: every request writes the whole prefix at 1.25x, so input is 2.17M x $5 = $10.85, which is 25% worse than not caching at all. Output tokens are extra in all three cases.

**The fan-out cold start.** Assume a 30K-token shared prefix and 12 children on Opus 5.5. Launched together: 12 x 30K x $5 per 1M = $1.80 of writes. Warm one child first: $0.15 + 11 x 30K x $0.20 per 1M = about $0.22. A saving of about $1.58 per fan-out: real, but small beside a full run's model spend, and cents on Haiku. The cache penalty matters where prefix size times step count is large (long agent loops), not in wide cheap fan-outs.

## 3. Published cost-per-task and cost-per-PR numbers

Very few companies publish cost per merged PR: Uber [names the metric without values](https://www.uber.com/us/en/blog/efficient-software-factory/), Ramp gives [under $15 only for integrations](https://builders.ramp.com/post/integrations-that-write-themselves), and no primary Stripe, Spotify or Ramp figure was found. Read the table as orders of magnitude; the units differ.

| Anchor | Number | Date, source | Unit and caveat |
|---|---|---|---|
| Claude Code, average spend | About $13 per developer per active day, $150-250 per developer per month; below $30 per active day for 90% of users | [Claude Code costs](https://code.claude.com/docs/en/costs), Oct 11, 2026 | Enterprise average; interactive use |
| Claude Code Review | $15-25 per review on average, about 20 minutes; 54% of PRs now get substantive comments, up from 16%; large PRs (over 1,000 lines) 84% get findings averaging 7.5 issues, under 1% marked incorrect | [Anthropic](https://claude.com/resources/articles/code-review), Mar 9, 2026 | Vendor figures; capped by monthly organization limits |
| Agents versus chat | About 4x tokens for agents, about 15x for multi-agent systems; agent teams about 7x a standard session in plan mode | [Anthropic research system](https://www.anthropic.com/engineering/multi-agent-research-system) (Jun 13, 2025); [costs doc](https://code.claude.com/docs/en/costs) | Anthropic's own measurement of its research system |
| Same feature, 23 arms | About $3 to about $33 to merge, 26 minutes to nearly three hours; harness alone about 2.5x; one model coded for about five cents but cost $2.81 to merge (97% was the review gate); a deterministic oracle caught that gate merging code passing as few as 12 of 38 unit tests | [Insight APAC](https://blog.apac.insight.com/2026/07/06/cost-to-a-merged-feature), Jul 6, 2026 | 29 runs: "a single measurement is a hypothesis, not a fact" |
| One model, nine harnesses | Cost per completed task $1.05 to $18.34; Claude Code $3.28 per pass versus another harness at $18.34 for the same 19 passes | [RUNTA](https://runta.com/blog/introducing-frontierharness-eval/), Sep 1, 2026 | 360 trials, 58.1% field-wide pass rate |
| Harness effect on cost | Up to 3x across three harnesses; the swap flips as many tasks (13%) as rerunning the same harness | [arXiv 2610.04433](https://arxiv.org/abs/2610.04433), Oct 3, 2026 | SWE-bench Verified, five models |
| Terminal-Bench 4.0 (330 trials each) | Opus 5.5 $14.30 per trial at 64.85%; Sonnet 5.5 $22.22 at 61.82%; GPT-6 Astra $9.90 and GPT-6.1 Sol $1.92 at the same 58.18%; Fable 5.1 about $18.92 at 57.88%; GLM-5.3 $8.27 at 41.82% | [leaderboard](https://www.tbench.ai/), Oct 6, 2026 | Max effort; the Sonnet 5.5 entry predates the Oct 7 cache-read cut; per-trial figures are totals divided by 330 |
| Agent-built C compiler | About $20,000 for nearly 2,000 sessions, 2B input and 140M output tokens, a 100,000-line compiler that builds Linux 6.9; most effort went into tests and verifiers | [Anthropic engineering](https://www.anthropic.com/engineering/building-c-compiler), Feb 5, 2026 | Roughly $0.20 per line (my arithmetic); a precisely checkable spec |
| Org-level spend | Bills "leaping from $20 or $100 to $2,000 to $5,000 per developer per month", extreme cases $20,000; AI coding costs to overtake the average developer salary by 2028 | [The Register on Gartner](https://www.theregister.com/ai-and-ml/2026/06/24/ai-coding-agents-could-soon-cost-more-than-the-developers-using-them/5260864), Jun 24, 2026 | Analyst forecast |
| One company's token bill | A 300-400 engineer company at $128,000 per week (expected $150,000), some developers at up to $20,000 per month; the company says cost per PR is falling | [DX annual insights 2026](https://getdx.com/uploads/dx-annual-insights-2026.pdf) | Company-reported, conference themes |
| Uber | Cost per session down 52% from the June peak, cost per 1,000 requests down almost 34%; weekly active users up 7x and weekly agentic requests up 9.4x from February to August 2026 | [Uber engineering](https://www.uber.com/us/en/blog/efficient-software-factory/), Aug 27, 2026 | Company-reported |
| Token budget gone | Uber burned its entire 2026 AI coding tools budget in four months, per its CTO as relayed by Fortune from The Information | [Fortune](https://fortune.com/2026/05/22/microsoft-ai-cost-problem-tokens-agents/), May 22, 2026 | Secondary; the original is behind The Information |

### 3.1 Cost levers with evidence

- **Fewer, better steps beat fewer tokens.** The strongest result: aggressive compression cut delivered tool-output tokens 38.4% yet [raised billed cost 6.8%](https://arxiv.org/abs/2607.12161), with token reduction weakly correlated with cost reduction (Pearson r = 0.15) because cache behavior and changed trajectories dominate. The paper recommends measuring cost per successful task.
- **Developer-written rules beat agent-written ones.** [Three wasteful behaviors](https://arxiv.org/abs/2609.30725) (subsumed retrieval, similar script generation, test re-execution) touched 79-98% of tasks and up to 22.75% of task cost; developer-designed skills cut cost up to 41.73%, about twice the best agent-synthesized skills.
- **Knowing when to stop.** [Experience-driven early termination](https://arxiv.org/abs/2601.05777) cut SE-agent cost 19-55% (32% on average) with at most 0.2% loss in resolution rate.
- **Cap the loop in code.** [Stripe's blueprints cap CI at two rounds](https://blog.bytebytego.com/p/how-stripes-minions-ship-1300-prs) because more rounds cost tokens and compute without proportional improvement; the branch then goes back to a human. Spotify's early homegrown loop allowed [10 turns per session and three session retries](https://engineering.atspotify.com/2025/11/context-engineering-background-coding-agents-part-2).
- **Default weaker models for subagents.** Uber calls it [the most impactful lever](https://www.uber.com/us/en/blog/efficient-software-factory/); it also compacts at 400K tokens even on 1M-context models, defaults to medium reasoning effort, and found 100+ MCP tools added about 50K-70K tokens of schema overhead.
- **Count CI minutes.** A [CI vendor model](https://tenki.cloud/blog/hidden-ci-tax-ai-coding-agents) (Tenki, Apr 8, 2026, illustrative assumptions) has 5 PRs x 2 pushes a week becoming 15 x 3 with agents: $640 to $2,880 a month for a 20-engineer team (+350%), $2,232 of it one macOS job. GitHub also bills [Copilot code review in Actions minutes](https://github.blog/news-insights/company-news/github-copilot-is-moving-to-usage-based-billing/).

## 4. Retry and replay costs

### 4.1 Where retries are billed

| Layer | What a retry costs | Source |
|---|---|---|
| Provider SDK | The official Anthropic SDK retries transient failures with exponential backoff, twice by default; failed (non-200) requests are not charged on Google Cloud's Agent Platform | [Claude API errors](https://platform.claude.com/docs/en/api/errors); [Agent Platform pricing](https://cloud.google.com/gemini-enterprise-agent-platform/generative-ai/pricing) |
| Provider, structured refusals | A safety-classifier decline arrives as a normal 200 response with `stop_reason: "refusal"`, sometimes mid-stream; a mid-stream refusal bills the input and the output already streamed; a fallback is billed at the fallback model's rates | [Refusals and fallback](https://platform.claude.com/docs/en/build-with-claude/refusals-and-fallback) |
| Temporal Cloud | An Action each time an Activity is started or retried; heartbeats and timers are Actions too | [Temporal Actions](https://docs.temporal.io/evaluate/cloud/actions) |

The retry stack multiplies silently: an SDK that retries twice sits inside an agent SDK inside an orchestrator retry. A failed request costs zero tokens on Google Cloud's page, so those layers cost time; the expensive case is a billed 200 whose result is thrown away (a crash after the model answered, a refusal, a schema failure, a deploy that kills the pod mid-activity). A spend-cap 429 [carries no retry-after and keeps failing until access resumes](https://platform.claude.com/docs/en/api/errors): mark it non-retryable and let the controller escalate.

Temporal's heartbeat meter is small: heartbeats are billable Actions but the SDK throttles them (the smaller of 0.8 x the heartbeat timeout or 30 seconds, and a 60-second maximum), so a long activity adds one or two Actions a minute ([failure detection](https://docs.temporal.io/encyclopedia/detecting-activity-failures)).

### 4.2 Not paying twice: what published systems do

- **Cursor.** Its homegrown runner system reached about 90% success ("essentially building a poor version of Temporal"); on Temporal Cloud activity success [exceeds 99%](https://www.zenml.io/llmops-database/building-and-operating-agentic-ai-coding-products-at-scale-with-temporal). Activities write to content-addressable storage and restart "without replaying expensive model token sequences" (a ZenML summary of a talk, not Cursor's own post).
- **Temporal's pi-temporal.** [A model-call retry returns the saved answer](https://temporal.io/blog/the-immortal-life-of-pi-running-the-pi-coding-agent-on-temporal) without calling the model; an interrupted tool call is recorded as "outcome unknown" and never auto-repeated, because "a timeout does not prove the previous attempt stopped".
- **Shopify Roast.** Session replay "eliminating the need to rerun expensive AI operations over and over again" ([Shopify](https://shopify.engineering/introducing-roast)).
- **Datadog.** Golden-path guidance says a platform should accept an idempotency key so repeated requests [return the original result instead of performing the action again](https://www.datadoghq.com/blog/golden-paths-for-ai-agents/).

### 4.3 Worked example: a replayed phase (estimate)

Take an illustrative $18.55 run whose biggest phases are planning $3.17, review $4.10 and tests $2.71. If each of those phases crashed once after its model call completed and re-ran with no idempotency, the extra spend is up to $9.98, +54% (illustrative arithmetic). Salvage is the other side: pulling stranded commits off a dead worker is free in tokens and expensive in trust, because it bypasses every gate.

## 5. Budgets and kill switches

### 5.1 The ladder, soft to hard

| Rung | What it does | Does it stop spend? | What it misses |
|---|---|---|---|
| Task budget (beta, Apr 16, 2026) | Tells the model how many tokens it has for a whole agentic loop and shows a running countdown (`task_budget` in `output_config`) | No: ["a soft hint, not a hard cap"](https://platform.claude.com/docs/en/build-with-claude/task-budgets); `max_tokens` is the absolute ceiling | The model may overrun mid-action |
| `--max-budget-usd` | Stops the run when estimated spend reaches the cap (print mode only; v2.1.217 or later); also stops background subagents | Yes, against a client-side estimate | Estimate, not the invoice |
| Agent SDK `max_budget_usd` | Per-call cap | Partly: it [counts only the call's own spend](https://code.claude.com/docs/en/agent-sdk/cost-tracking), so totals restored from a resumed session do not count | A retry or resume gets a fresh budget |
| Managed Agents session budget (Aug 7, 2026) | Hard ceiling set at session creation, priced at list rates | Yes: stops new model requests, the in-flight one finishes, and the session [pauses to idle](https://platform.claude.com/docs/en/managed-agents/budgets) (`budget_reached`) and resumes if the budget is raised | Only inside that product |
| Workspace and org limits | Claude Code workspace spend limits and rate limits; Code Review monthly org caps; Copilot budgets at enterprise, cost-center and user level | Yes, per period | They stop everyone in the scope, not one runaway run |
| Tier monthly spend cap | Start $500, Build $1,000, Scale $200,000; Custom none | Yes, brutally: API usage [pauses until 00:00 UTC on the 1st](https://platform.claude.com/docs/en/api/rate-limits) with a 429 `enforced_spend_limit_reached` and no retry-after | Halts every pipeline in the org |
| Gateway budgets | [LiteLLM](https://docs.litellm.ai/docs/proxy/users) global, key, team and user budgets with `budget_duration` | Yes, if configured | The global `max_budget` [fails open](https://docs.litellm.ai/docs/proxy/users) in a deployment without a database |
| Alerts | Email or dashboard when the money is gone | No | Everything |

A secondary roundup of five vendor spend controls shipped June to August 2026 splits them into brakes that [bound a run](https://www.toriihq.com/blog/2637-05-google-shipped-hard-monthly-spend-caps-for-ai-agents-the-5th) (Anthropic, AWS) and brakes that bound a billing period (OpenAI, Google): "each brake stops at the vendor's own wall". Only Anthropic's August 7 session budgets were confirmed in a primary source. Either way, only your controller sees all vendors at once.

### 5.2 Where the budget has to live

Put the run budget in the controller, not in the prompt or the SDK call. Each phase activity returns a small cost record (tokens, estimated dollars, served model, stop reason); the workflow adds it to run state; the controller checks the sum before the next phase or attempt and ends in an escalation, not a crash, at the cap. That survives pod death and retries because the total lives in durable state, which a per-call `max_budget_usd` cannot do. Platform guidance converges: StackGen calls hard iteration caps, per-tool budgets and identical-call loop detection ["pre-flight circuit breakers"](https://www.cncf.io/blog/2026/08/04/you-cant-debug-what-you-cant-see-observability-for-ai-agents/), with alerts when a session exceeds a multiple of the rolling average cost; the Agent Reliability Score names ["a runaway process with a credit card"](https://platformengineering.org/blog/the-agent-reliability-score-what-your-ai-platform-must-guarantee-before-agents-go-live) as the unlimited agent; Datadog bounds correction loops by [attempts, time or cost, with terminal states](https://www.datadoghq.com/blog/golden-paths-for-ai-agents/) including `escalated`.

Three cautions. Cost fields are estimates: `total_cost_usd` is [client-side, not authoritative billing](https://code.claude.com/docs/en/agent-sdk/cost-tracking), `usage` undercounts once subagents nest, and a crashed session may report cost zeroed; reconcile weekly with the [Usage and Cost API](https://platform.claude.com/docs/en/manage-claude/usage-cost-api). Test the kill switch like any gate. And mind the subscription trap: with `ANTHROPIC_API_KEY` set, Claude Code [bills the API instead of the plan](https://support.claude.com/en/articles/11145838-use-claude-code-with-your-pro-or-max-plan).

## 6. FinOps for AI guidance

The [FinOps Foundation's FinOps for AI overview](https://www.finops.org/wg/finops-for-ai-overview/) (Feb 17, 2026) applies the existing method to AI: "pricing may change wildly up or down"; combine quotas, throttling and usage limits with anomaly detection; if token consumption doubles without clear justification, investigate prompts and application logic. The [State of FinOps 2026](https://data.finops.org/) says 98% of respondents now manage AI spend (31% two years earlier). A [FinOps X session](https://x.finops.org/agenda/the-cost-of-cognition-why-your-ai-budget-will-break-in-the-agentic-era/) (Jun 10, 2026) says agents "decide how much to spend and how hard they think". The [State of Tokenomics](https://www.tokeneconomics.com/state-of-tokenomics/) (Sep 23, 2026; 472 large organizations) finds three in four cannot confidently prove AI outcomes to the CFO, 39% cannot connect spend to an outcome the CFO would accept, 86% evaluate or use a model router (router users four times as likely to show CFO value), and only 4% asked vendors for cheaper prices against 23% who wanted more transparency.

| FinOps practice | Pipeline control that implements it | Where it lives |
|---|---|---|
| Usage limits and quotas | Run cap, per-phase cap, attempt cap per phase kind | Controller state |
| Anomaly detection | Alert when a session exceeds a multiple of the rolling average (StackGen) | Observability backend |
| Unit economics | Cost per merged PR including failed and escalated runs, plus revert rate (Uber names both) | Dashboard |
| Routing | Two-model pool decided from your own phase history | Gateway or registry |

Ramp's framing is the cleanest unit: [cost equals tasks attempted times cost per attempt, and value equals successful tasks times value per task](https://builders.ramp.com/post/ai-spend-value). A CNCF post lists ["pre-deployment cost gates"](https://www.cncf.io/blog/2026/07/06/evolving-platform-engineering-for-ai-native-workloads/) under "Embedded FinOps": a default per-run cap is that primitive.

## 7. What orchestrator platforms charge

### 7.1 Billing units

| Engine | Meter | List price | Traps |
|---|---|---|---|
| [Temporal Cloud](https://docs.temporal.io/cloud/pricing) | Actions: workflow starts, every activity start and retry, heartbeats, timers (including implicit ones), signals, queries and updates | $50 per million ($0.00005 each); volume tiers on Business and up: first 5M $50, to 10M $45, to 20M $40, to 50M $35, to 100M $30, to 200M $25 per million. Developer plan 10% of usage; Business the greater of $500 a month or 10%, includes 2.5M Actions | [Viewing a call stack in the UI is a billable query](https://docs.temporal.io/evaluate/cloud/actions); storage billed per GB-hour (Active $0.042, Retained $0.00105) |
| [Inngest](https://www.inngest.com/pricing) | Executions: one per run plus one per `step.run` | Free 50k executions (pauses at quota); Pro from $99 a month with 1M; Business from $499 with 10M; Pro overage $0.000050 down to $0.000015 each | A function with 5 steps is 6 executions; agents often use 10+ per run |
| [Restate Cloud](https://restate.dev/pricing) | Durable actions (invocations, `ctx.run` steps, awakeables, sleeps, promises, state ops); large payloads counted per started 64 KiB | Free tier 100k actions a month; paid tier prices could not be read (rendered client-side) | Journaled model outputs are metered by size |
| [AWS Step Functions](https://aws.amazon.com/step-functions/pricing/) | State transitions (Standard); requests plus GB-seconds (Express) | $0.000025 per transition in US East, 4,000 free a month; Express $1.00 per million requests and $0.00001667 per GB-second | Each retry is another transition; AWS's own example: 9 transitions x 100,000 executions is $22.40 while the five Lambda functions cost $97.62 |
| [DBOS](https://www.dbos.dev/dbos-pricing) | Checkpoints (workflows, steps, transactions) in your own database | Open-source library free; Pro $99 a month with 1M checkpoints, extra $50 per million; Teams $499 with 10M, extra $40 | You run Postgres |
| [Trigger.dev](https://trigger.dev/pricing) | Per-second machine compute plus a per-run fee | $0.0000338 per second on small-1x plus $0.000025 per run | Waits over 5 seconds are checkpointed and free, but waiting on an outbound LLM call keeps the machine billing |

### 7.2 Worked estimate: what the meter adds to a $20 run

| Runs per month | Model spend at $20 | Temporal usage at list (before plan fee) | Inngest plan | DBOS | Step Functions | Trigger.dev |
|---|---|---|---|---|---|---|
| 100 | $2,000 | $5 | Free | $0 (open source) or $99 | about $0.05 | $6-12 |
| 1,000 | $20,000 | $50 | Free at its 50k quota edge, so $99 Pro | $99 | about $1.40 | $61-122 |
| 10,000 | $200,000 | $475 | $99 Pro (500k executions) | $99 | about $15 | $609-1,217 |
| 100,000 | $2,000,000 | $3,425 | $499 Business (5M executions) | $299 (Pro plus 4M extra) | about $150 | $6,087-12,171 |

## 8. Agent product pricing changes

| Date | Vendor | Change |
|---|---|---|
| Jul 4, 2025 | Cursor | [Pro moved from 500 requests a month (Sonnet counted as two) to $20 of frontier-model usage at API pricing](https://cursor.com/blog/june-2025-pricing); "the hardest requests cost an order of magnitude more"; refunds for unexpected charges |
| Apr 27, 2026, effective Jun 1 | GitHub Copilot | [All plans move to AI Credits](https://github.blog/news-insights/company-news/github-copilot-is-moving-to-usage-based-billing/) consumed per input, output and cached token at API rates (1 credit = $0.01); plan prices unchanged (Pro $10, Pro+ $39, Business $19 and Enterprise $39 per user); cheaper-model fallback removed; budgets at enterprise, cost-center and user level ([docs](https://docs.github.com/en/copilot/concepts/billing-and-usage/individuals/billing)). GitHub: "a quick chat question and a multi-hour autonomous coding session can cost the user the same amount" |
| Jun 1, 2026 | Copilot (legacy plans) | Annual Pro and Pro+ keep premium requests: [only the prompts you send count, not autonomous actions](https://docs.github.com/en/copilot/reference/copilot-billing/request-based-billing-legacy/copilot-requests); code review gets a 13x multiplier; the cloud agent costs one premium request per session times the model rate |
| May 22-26, 2026 | Microsoft, Uber | Microsoft [reportedly cancelling most direct Claude Code licenses](https://fortune.com/2026/05/22/microsoft-ai-cost-problem-tokens-agents/) (via The Verge); Uber's COO says [the link to useful features "is not there yet"](https://fortune.com/2026/05/26/uber-coo-ai-spending-tokens-claude-code/) while its CEO says about 10% of committed code comes from autonomous agents |
| May 30 - Jun 2, 2026 | Copilot users | Reports of bills jumping [from about $29 to nearly $750 a month](https://techcrunch.com/2026/05/30/what-a-joke-github-copilots-new-token-based-billing-spurs-consternation-among-devs/) (single Reddit anecdotes) and one requested change costing [more than $6](https://www.theregister.com/ai-and-ml/2026/06/02/github-copilot-users-threaten-exit-as-metered-billing-kicks-in/5249826) |
| Oct 7-8, 2026 | Anthropic | Sonnet 5.5 cache reads halved ([Haiku 5.5 post](https://www.anthropic.com/claude-haiku-5-5)); monthly API credits for Max 5x ($100), Max 20x ($200) and Team (up to $500 pooled), which per the help center [cannot be used in Claude Code or the Claude apps](https://support.claude.com/en/articles/11145838-use-claude-code-with-your-pro-or-max-plan); Max plans have a separate Fable weekly limit, and plan limits are shared across Claude and Claude Code |

## 9. The honest answer: cost per merged feature

Tokens are the wrong unit and merged work is the right one. The evidence on payback is thin and split. Gartner: [no direct relation between token consumption and productivity gains](https://www.theregister.com/ai-and-ml/2026/06/24/ai-coding-agents-could-soon-cost-more-than-the-developers-using-them/5260864); DORA calls token leaderboards a [gameable metric](https://dora.dev/insights/finding-balance-in-the-era-of-tokenmaxxing/); Tokenomics finds most enterprises cannot prove the outcome. On the other side, bounded migrations show measured savings (Spotify reports 60-90% time savings on [fleet migrations](https://engineering.atspotify.com/2025/11/spotifys-background-coding-agent-part-1); see [Engineering Case Studies](engineering-case-studies.md) and [Productivity Evidence](productivity-evidence.md)). The consistent reading: value follows scope.

Metric for the dashboard: dollars per merged PR, including failed, escalated and re-run attempts, plus human commits to merge and revert rate; never dollars per run. [aireceipts](https://github.com/anandgupta42/aireceipts) (an itemized list-price receipt on the PR) and [cc-ledger](https://github.com/delta-hq/cc-ledger) (per-session and per-PR cost from hooks) show how to attach cost to the PR. RUNTA: ["Agents spend the most money on the problems they cannot solve"](https://runta.com/blog/introducing-frontierharness-eval/).

## 10. Checklist: twelve cost controls for a durable agent pipeline

1. Run a hello-world per phase before real spend and record its floor (the SDK startup tax).
2. Seat models by phase: expensive model for planning and critics, cheaper for bulk, and test that the cheap seat can do the work.
3. Keep volatile content (dates, IDs, timestamps) at the end of every prompt.
4. Log cache hit ratio and `cache_miss_reason` per phase next to cost per call.
5. Warm one fan-out child first, and use the 1-hour TTL for phases that wait on tests or CI.
6. Make idempotency check validate artifact content, not existence.
7. Cap CI rounds (Stripe: two), run what you can locally, and count CI minutes in cost per PR.
8. Enforce the run budget in the controller's durable state, not in a per-call SDK flag.
9. Reconcile pipeline-reported cost with the provider's usage and cost API weekly.
10. Test the kill switch, and prefer pause-to-idle over terminate so state survives.
11. Report dollars per merged PR, with failed and escalated runs in the denominator.

## 11. Limits of this note

No real Temporal Cloud bill for an LLM agent pipeline was found, so every orchestrator figure is an estimate from list unit prices. Restate's paid prices could not be read. Summer-2026 vendor spend-control dates (apart from Anthropic's August 7) are secondary. No primary cost-per-PR figure from Stripe, Spotify or Ramp was found, and Copilot code review's cost under AI Credits is not published. Prices will have moved by the time you read this.

## Unverified leads

- [We're going to need default hard budget caps on pretty much everything](https://simonwillison.net/2026/Oct/3/default-hard-budget-caps/) (Simon Willison, Oct 3, 2026): not verified, so no claim in this note rests on it. The title is the argument; read it before you recommend default hard caps.

## Related

- [Productivity Evidence](productivity-evidence.md)
- [Engineering Case Studies](engineering-case-studies.md)
- [Comparisons and Benchmarks](comparisons-and-benchmarks.md)
- [Engine Profiles](engine-profiles.md)
- [Observability for Agent Workflows](observability-for-agent-workflows.md)
- [Autonomous Coding Agents](autonomous-coding-agents.md)
