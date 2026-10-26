# Spec-Driven Development

## 1. What the term covers

- **The canonical statement.** GitHub's launch post (2025-09-02) says to treat coding agents ["more like literal-minded pair programmers"](https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/) than search engines, with specs as "living, executable artifacts that evolve with the project" and four phases: Specify, Plan, Tasks, Implement.
- **Three levels.** Birgitta Böckeler's comparison of Kiro, Spec Kit and Tessl ([Understanding Spec-Driven-Development](https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html), 2025-10-15) separates spec-first, spec-anchored and spec-as-source, and compares spec-as-source to model-driven development, warning it might inherit "Inflexibility and non-determinism". Most 2026 tools sit at spec-first or spec-anchored.
- **Not waterfall, say the builders.** Kiro's principal engineer argues SDD "uses fast generation to shorten the feedback loop" and that "Requirements are discovered through building and reviewing, not only through upfront discussion" ([Al Harris, AI Engineer](https://ai.engineer/talks/HY_JyxAZsiE-spec-driven-development-agentic-coding-at-faang), 2026-01-09). OpenSpec's philosophy lines include "iterative not waterfall" and "built for brownfield not just greenfield".
- **Plan-driven is the lighter cousin.** Plan modes in Claude Code, Cursor, Gemini CLI and Kiro keep a plan in conversation context or one markdown file instead of a three-file spec set (section 3.8).

## 2. Comparison tables

### 2.1 Structure: what the spec, plan and tasks look like

| Tool | Latest, as of October 2026 | License or price | Spec, plan and tasks structure | Human gates |
|---|---|---|---|---|
| [GitHub Spec Kit](https://github.com/github/spec-kit) (specify-cli) | 1.1.3, 2026-10-09 | MIT, free | `/speckit.constitution`, specify, clarify, plan, checklist, tasks, analyze, implement, converge; extensions, presets, bundles | `gate` steps in the workflow engine pause for human approval; maintainers gate handoffs by label in the project's own SDLC |
| [Kiro](https://kiro.dev/docs/specs/) (AWS) | IDE 1.2.56 (2026-10-08), CLI 2.29.0 (2026-10-09) | Free $0 (50 credits), Pro $20 (1,000), Pro+ $40 (2,000), Pro Max $100 (5,000) | `.kiro/specs/<name>/` with `requirements.md` (or `bugfix.md`), `design.md`, `tasks.md`; EARS requirements; parallel task waves | Approval checkpoint per phase; Quick Spec skips gates |
| [Tessl](https://docs.tessl.io/overview/readme.md) | `@tessl/cli` 0.114.0, 2026-10-09 | Free $0 (1,000 credits), Team $100 per month, Enterprise custom | 2025: spec files with `@generate`, `@describe`, `@test`, `@use` tags and an API block; 2026: skills registry, governance, evals, verifiers | Verifiers and code review in CI |
| [BMAD Method](https://github.com/bmad-code-org/BMAD-METHOD) | 6.12.2, 2026-10-11; v7 in preview alpha, README ETA 2026-10-16 | MIT, free | PRD, architecture and spec skills, sprint planning, Build and Build Auto; spec frontmatter carries status | Multi-layer review (blind, edge-case, verification-gap, intent alignment) |
| [OpenSpec](https://github.com/Fission-AI/OpenSpec) (`@fission-ai/openspec`) | 1.14.1, 2026-10-05 (1.0.0 on 2026-01-26) | MIT, free | Change folders with propose, apply, archive; delta specs; an artifact dependency graph; `/opsx:verify` in the expanded profile | Review or verify before archive |
| [Agent OS](https://github.com/buildermethods/agent-os) | 3.0, 2026-01-20 | MIT, free | `/discover-standards`, `/inject-standards`, `/shape-spec`; "Spec creation now defers to Plan Mode" | Whatever the host agent's plan mode provides |
| [OpenAI Symphony](https://github.com/openai/symphony) | Draft v1 spec, 2026-03 | Apache 2.0 | Language-agnostic `SPEC.md` plus an Elixir reference; policy in a repo-owned `WORKFLOW.md`; one isolated workspace per tracker issue | Runs can end at a handoff state such as "Human Review" |
| Plan modes: [Claude Code](https://code.claude.com/docs/en/common-workflows) 2.1.296 (2026-10-09), [Gemini CLI](https://developers.googleblog.com/plan-mode-now-available-in-gemini-cli/) 0.63.0 (2026-10-06), [Cursor](https://cursor.com/blog/plan-mode) (2025-10-07), [Kiro](https://kiro.dev/docs/specs/plan/) | See left | Bundled with each product | One read-only planning pass, a markdown plan, then execution | The approval of the plan |
| [HumanLayer RPI](https://github.com/humanlayer/advanced-context-engineering-for-coding-agents/blob/main/ace-fca.md) | Sept 2025, updated [July 2026](https://raw.githubusercontent.com/humanlayer/advanced-context-engineering-for-coding-agents/HEAD/wsff.md) | Methodology; free for teams of up to 3 | Research, plan, implement; later four phases: product requirements, system architecture, program design, vertical slices | Human review concentrated on research and plans |

### 2.2 Orchestration: who owns state, and what the docs say survives a crash mid-step

| Tool | Orchestration | State lives in | Verification step | What the docs say about a crash mid-step |
|---|---|---|---|---|
| Spec Kit | YAML workflow engine (0.7.0, 2026-04-14): command, prompt, shell, gate, if, switch, while, do-while, fan-out, fan-in | `.specify/workflows/runs/<run_id>/` (`state.json`, `inputs.json`, `log.jsonl`) | `/speckit.converge`; bug workflow verdicts verified, partial or failed | `specify workflow resume <run_id>` resumes "from the exact step where it stopped" and re-runs the blocked step ([workflows](https://github.github.io/spec-kit/reference/workflows.html)) |
| Kiro | Workflows (2026-09-30): a model proposes a graph, the runtime executes it, each step in a fresh session | Run checkpoints at node boundaries | Loops stop on evidence (`fileCheck`) or a capped `maxIterations`; `/goal` | "Retry does not undo workspace files"; watch delivery is at least once; connection failures retry the same step session in place up to 24 hours ([workflows](https://kiro.dev/docs/workflows/), [manage](https://kiro.dev/docs/workflows/manage/)) |
| BMAD | `bmad-loop` unattended orchestrator (6.10.0, 2026-07-03) polls spec frontmatter status | Spec frontmatter, tested Python scripts for bookkeeping | Test audit, intent alignment auditor, verification-gap reviewer | Intent-gap halts preserve the attempted change as a patch before the tree is reverted |
| OpenSpec | None; skills query the CLI for state | Filesystem existence of artifacts | `/opsx:verify` | 1.14.1: "an interrupted run keeps the files it already wrote" |
| Agent OS | Retired in v3: "Frontier models manage task delegation on their own" | n/a | n/a | n/a |
| Symphony | Poll-and-dispatch daemon | Issue tracker and filesystem; no persistent database | Workflow-defined handoff | In-memory scheduler state "is not restored"; a general workflow engine is an explicit non-goal |
| Claude Code plan mode | The conversation | A markdown plan file | Plan approval | Fixes shipped for a plan lost after compaction (2.1.47) and when a cloud container restarted before the plan was presented (2.1.290) |

## 3. Tool notes

### 3.1 GitHub Spec Kit

- **Timeline** ([changelog](https://github.com/github/spec-kit/blob/main/CHANGELOG.md), [history](https://github.github.io/spec-kit/history.html)): initial commit 2025-08-21, 0.0.1 on 2025-08-22, lead maintainer handover 2026-01-22, workflow engine 0.7.0 (2026-04-14), bundled bug extension 0.9.5 (June 5), `/speckit.converge` in 0.11.2 (2026-06-18), assess extension 0.13.0 (July 17), 1.0.0 on 2026-08-21, 1.1.3 on 2026-10-09. At 1.0.0 the docs reported 38 coding-agent integrations, 157 community extensions, 33 presets and 270+ contributors; the docs landing page claims "130K+" stars (self-reported).
- **Converge** is an append-only audit of the codebase against spec, plan and tasks: it "never edits or deletes code", its only write is adding tasks to `tasks.md`, and its outcomes are "Converged" (file unchanged) or "Tasks appended". "Each pass finds fewer items; repeat until it reports converged" ([agentic SDD reference](https://github.github.io/spec-kit/reference/agentic-sdd.html)). The README's bug workflow adds: "Missing verification is not a successful fix." 1.0.9 (2026-09-21) clarified how converge assesses completion claims.
- **The workflow engine grew the failures any engine meets.** Fixes include a TOCTOU race in `RunState.load` (0.16.4, 2026-08-14), rejecting a non-integer or out-of-range `current_step_index` on resume (1.0.9) and "an interrupted gate prompt must not approve the gate" (1.1.2, 2026-10-07). The docs warn that a prompt step's output is "text produced by the AI agent" and to treat it as untrusted when it flows into a shell step: "There is no shell-escaping filter in the expression language and no sandbox around a shell step."
- **How the project develops itself** ([guide](https://github.github.io/spec-kit/guides/agentic-sdlc.html), 2026-09): bug-assess, bug-fix and bug-test run as separate agentic workflows with human-controlled handoffs and a draft PR for maintainer review; release, PyPI publishing and docs deploy stay conventional Actions "without an agent or a Spec Kit command". "Running an agent on GitHub Actions does not make its reasoning a deterministic script." "A new issue is untrusted input, not permission to run an agent." And honestly: "the timeline shows adoption rather than measured time savings."

### 3.2 Kiro

- **Timeline.** Public preview 2025-07-14, general availability 2025-11-17, property-based testing arrived with GA ([Al Harris](https://ai.engineer/talks/HY_JyxAZsiE-spec-driven-development-agentic-coding-at-faang)).
- **Specs** ([docs](https://kiro.dev/docs/specs/), 2026-10-02): three files per spec, approval checkpoints, Quick Spec without gates, and a parallel run that builds a dependency graph and groups independent tasks into waves ("Waves execute sequentially; tasks within a wave execute concurrently"); when complete the agent opens a PR or MR.
- **Requirements analysis** ([post](https://kiro.dev/blog/deep-spec-analysis/), 2026-05-12): LLMs encode the specification into formal logic and SMT solvers generate findings in four bug classes (wrong level of detail, ambiguity, inconsistency, incompleteness), each surfaced as a two-option question. It cites prompt-mutation research (Larbi et al., 2025) of 20-40% Pass@1 drops, with 60-90% of syntactically valid code semantically wrong. A non-LLM checker next to the LLM is the pattern to note.
- **Correctness.** Properties are extracted from EARS requirements ("THE System SHALL ...") and turned into Hypothesis tests; PBT "provides evidence of correctness, not a proof" ([docs](https://kiro.dev/docs/specs/correctness/)).
- **Plan mode and `/goal`** ([plan](https://kiro.dev/docs/specs/plan/)): read-only planning, recommended for tasks of "15-60 minutes of implementation"; the CLI's [`/goal`](https://kiro.dev/docs/cli/chat/goal/) loops implement-and-verify, defaulting to 5 iterations (`--max` to raise), "only marking the task complete when acceptance criteria are met", and stopping if it exhausts its context or hits a repeated failure pattern.
- **Workflows** ([docs](https://kiro.dev/docs/workflows/), [launch](https://kiro.dev/changelog/web/introducing-workflows/), 2026-09-30 with IDE 1.2 and CLI 2.26.0): opt-in (`kiroAgent.workflows.enabled`, off by default); recipes as `.workflow.json`, `.workflow.yaml` or `.workflow.yml` in `.kiro/workflows/`, which the IDE asks before an agent edits; "Each step runs in its own session with fresh context, so a reviewer, for example, evaluates the work without inheriting the coder's reasoning"; captured outputs are wrapped in tamper-resistant delimiters; repeat nodes need `maxIterations` and `onMaxIterations`; a `fileCheck` stop condition compares a JSON path value and a missing or unreadable file "does not match, so the repeat continues"; the docs warn a workflow "uses more tokens than a single session".
- **Kiro Crew 0.7.0** ([2026-09-24](https://kiro.dev/changelog/crew/0-7/)) added a durable queue ("A Gateway restart restores queued work instead of dropping it") and an optional append-only session ledger across context compaction. Everyone eventually adds a durable queue.
- **Vendor claim.** An AWS customer story says a travel company delivered a 100,000-line ticketing system "through development, testing, to go-live within five weeks" and cut prototype validation "from months to days" ([PKFARE](https://www.amazonaws.cn/en/customer-stories/pkfare/), vendor-published).

### 3.3 Tessl

[Tessl launched](https://tessl.io/blog/tessl-launches-spec-driven-framework-and-registry) a Spec Registry in open beta ("more than 10,000 pre-built specs") and a closed-beta Framework on 2025-09-23, where specs with `@generate` links could generate code. The [current docs overview](https://docs.tessl.io/overview/readme.md) lists six components (registry and package manager, governance, evals, observability, context and findings, Tessl Agent in open beta), and its index lists verifier and code-review pages but no spec-framework pages. That is an observation about the docs and homepage, not a vendor statement about the Framework's status. Tessl's recent writing is about verification: Patrick Debois argues three boundaries need enforcing, application behavior, workflow progression and agent permissions, with "The harness checks the required evidence before allowing the next workflow state" ([post](https://tessl.io/blog/engineering-a-context-driven-software-factory), 2026-10-08); a talk argues for executable specs an agent runs against a preview environment to return a verdict ([Azriel](https://tessl.io/blog/ai-agent-evaluation-needs-executable-specs)); and a Java Champion describes an "AI Unified Process" where system use cases plus a domain model feed code generation directly: "I skip the plan-and-task phase" ([Martinelli](https://tessl.io/blog/spec-driven-development-works-when-specs-are-system-use-cases)). Its cost study is in [AI Code Review and Quality Gates](ai-code-review-and-quality-gates.md).

### 3.4 BMAD Method

The 2026 changelog ([6.10 to 6.12.2](https://github.com/bmad-code-org/BMAD-METHOD/blob/main/CHANGELOG.md)) reads like a list of silent-failure fixes:

- **6.10.0 (2026-07-03):** `bmad-loop` is "BMad's unattended-dev orchestrator", and `bmad-dev-auto` is "driven entirely off spec-frontmatter status so an orchestrator like bmad-loop can poll it".
- **6.11.0 (2026-08-09):** sprint planning "rebuilt around a deterministic script core" (`sprint_plan.py`, 37 tests): "Judgment stays with the LLM." A matrix test audit treats a test "that exists but was never registered, filtered in, or enabled" as missing; an Intent Alignment Auditor arrived and "The post-implementation acceptance-criteria checkbox ritual is gone"; a verification-gap reviewer asks "if this behavior broke, would any test fail?"
- **6.0.4:** removed a zero-findings halt condition from the edge-case hunter because it was "pressuring the LLM to hallucinate findings when none legitimately exist".
- **6.12:** decides how much ceremony a change needs "after investigating it, not before".
- v7 is in preview alpha with a README ETA of 2026-10-16, so treat the shape as in flux. Heavy for small teams.

### 3.5 OpenSpec

[OpenSpec](https://github.com/Fission-AI/OpenSpec) 1.0 (2026-01-26) replaced phase-locked commands with an artifact graph the AI queries through the CLI ([OPSX docs](https://github.com/Fission-AI/OpenSpec/blob/main/docs/opsx.md)): topological ordering, state detected by filesystem existence, and "Dependencies are enablers, not gates." The graph tracks state; it is not described as running agents. 2026 adds cross-repo planning "Stores" in beta. Its 1.14.1 notes show the trap of existence-as-completion: apply "no longer calls a change ready to archive too early", and because "specs reads done once any spec file exists", `/opsx:continue` now checks for capabilities still missing a spec file. Self-reported: 68.0k stars, "more than 265,000 developers a month".

### 3.8 Plan modes

- **A plan is a markdown file, and plan mode is a prompt.** Armin Ronacher found Claude Code's plan is "effectively a markdown file", the read-only behavior comes from an injected prompt ("MUST NOT make any edits" except the plan file) with the file-writing tools still present, and "There is no other enforcement from what I can tell" ([post](https://lucumr.pocoo.org/2025/12/17/what-is-plan-mode/), 2025-12-17).
- **Vendors keep patching it** ([Claude Code changelog](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md)): 2.0.28 (2025-10-27) added a Plan subagent; 2.1.47 (2026-02-18) fixed a plan "lost after context compaction"; 2.1.136 (2026-05-08) fixed plan mode not blocking writes when a matching `Edit(...)` allow rule exists; 2.1.212 (2026-07-16) fixed it auto-running file-modifying Bash commands; 2.1.290 (2026-10-05) fixed a plan lost when a cloud container restarted before the plan was presented. The changelog does not say enforcement moved from prompt to code, so say "vendors keep patching it". The Ultraplan cloud planning preview was removed in 2.1.222 (2026-08-04); the docs give no reason beyond the generic research-preview note, so it is not evidence of decline.
- **Commodity.** Gemini CLI added a read-only plan mode with `ask_user` and `enter_plan_mode` and `exit_plan_mode` tools ([2026-03-11](https://developers.googleblog.com/plan-mode-now-available-in-gemini-cli/)); Cursor says "Most new features at Cursor now begin with Agent writing a plan" ([2025-10-07](https://cursor.com/blog/plan-mode)).
- **"Plan mode is dead"** (593 HN points) argues from a failed planning-first product that "I conflated planning with a plan", and that separating plan and build felt like a waterfall: "There was no going back up the waterfall." Its proposed loop is understand, act, inspect, clarify, adjust, act again ([Nadeem](https://www.aymannadeem.com/artificial/intelligence,/developer/tools/2026/09/24/plan-mode-is-dead.html), 2026-09-24). That is an interactive-use argument; unattended pipelines keep a plan as a gate.

### 3.9 Research, plan, implement (HumanLayer)

- **ACE-FCA** ([2025-09](https://github.com/humanlayer/advanced-context-engineering-for-coding-agents/blob/main/ace-fca.md)): keep context utilization "in the 40-60% range" through frequent intentional compaction, and concentrate human review on research and plans: "a bad line of a plan could lead to hundreds of bad lines of code" (research: thousands). Reported results: BAML (35k LOC) draft PRs "in about 7 hours" against estimates of 3-5 days each, a team of three averaging about $12k a month on Opus, and an admitted failure (a parquet-java Hadoop removal "did not go well").
- **Why Software Factories Fail** ([2026-07-23](https://raw.githubusercontent.com/humanlayer/advanced-context-engineering-for-coding-agents/HEAD/wsff.md), also an AI Engineer [keynote](https://www.youtube.com/watch?v=Ib5GBkD555M)): "In July 2025 we went full lights-off." By about the third incident in November "we decided it would be easier to rewrite from scratch", costing a cofounder two weeks by hand. The argument: RL rewards pass/fail tests and "there is no penalty for eroding codebase maintainability". The prescription is front-loaded human alignment in four phases (product requirements, system architecture, program design, vertical slices), with a guess that "~40% of tasks get oneshot or oneshot w/ 1-2 rounds of light feedback", one combined plan document for medium tasks and every step for large ones, and "embrace the constraints and move 2-3x faster, safely". It also quotes Faros AI figures second-hand (incidents per PR +242.7%, 31.3% of PRs skipping review) and calls them a correlation signal, not a smoking gun.
- **A benchmark from the same author** ([SlopCodeBench on Opus 5](https://github.com/humanlayer/advanced-context-engineering-for-coding-agents/blob/main/benchmarking-opus-5-on-slop-code-bench.md), 2026-07-27): 3 problems, 17 checkpoints with the spec evolving, a fresh context per checkpoint; Opus 5 passed 4 of 17 strictly (24%), Opus 4.8 and Sonnet 5 one each (6%), and "none of the models made it to the end of any challenge with everything passing".

### 3.10 Research variants

- **Taxonomy.** A comparison of six frameworks (Spec Kit, OpenSpec, BMAD, GSD, Spec Kitty, Reversa) across specification, context, roles, execution, validation and portability finds "no framework strongly covers all six dimensions" and lists shared risks: "drift between specification and code, excessive trust in generated artifacts, fragility of community extensions, platform dependence and a lack of benchmarks for the complete process" ([paper](https://arxiv.org/abs/2606.04967), 2026-06-03).
- **Citations as checkable claims.** traceSDD requires per-line requirement citations. Across 840 implementations only the cited condition enabled automated hallucination detection (true detection rate 86.4% with Claude Sonnet 4.6, 88.0% with GLM-5-turbo, against 0% for the alternatives, no false positives), but the uncited condition produced more deterministic output (Cohen's d -0.76 and -0.72) ([paper](https://arxiv.org/abs/2606.30689), 2026-06-28). You can make output checkable or repeatable; pick deliberately.
- **Adversarial reviewer with fresh context on every pass:** the [Verified Spec-Driven Development gist](https://gist.github.com/dollspace-gay/d8d3bc3ecf4188df049d7a4726bb2a00) (no tooling; skim for the builder and adversary split).

## 4. Critiques, and the evidence on what works

### 4.1 The waterfall and ceremony critique

| Account | Date | What they did | What they found |
|---|---|---|---|
| [Böckeler, Thoughtworks](https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html) | 2025-10-15 | Hands-on with Kiro, Spec Kit, Tessl | A small bug became "4 user stories with a total of 16 acceptance criteria"; "I'd rather review code than all these markdown files"; "I frequently saw the agent ultimately not follow all the instructions" (HN: 128 points) |
| [Thoughtworks Radar](https://www.thoughtworks.com/radar/techniques/spec-driven-development) | 2025-11-05 | Placed SDD in Assess | "We may be relearning a bitter lesson". The page now carries a generic "not on the current edition" banner, which is not evidence of a changed view |
| [Marmelab](https://marmelab.com/blog/2025/11/12/spec-driven-development-waterfall-strikes-back.html) | 2025-11-12 | Critique citing a Spec Kit example | Adding the current date to a time-tracking app produced "8 files and 1,300 lines of text"; the agent "marked the 'verify implementation' task as done without writing a single unit test"; "For large existing codebases, SDD is mostly unusable" (HN: 225 points, 191 comments) |
| [Scott Logic](https://blog.scottlogic.com/2025/11/26/putting-spec-kit-through-its-paces-radical-idea-or-reinvented-waterfall.html) | 2025-11-26 | Rebuilt a hobby-app feature with Spec Kit and measured | Table below; "Spec Kit drags you right back into the past!" |
| [hubreb](https://hubreb.github.io/blog/spec-kit-brownfield-implementation) | 2026-05-12 | Spec Kit v0.7.5 with Claude Code on Opus 4.7, two personal codebases, five sessions | A design contract cited the wrong code path while the outcome was right: "documentation that is correct in outcome and wrong in cited mechanism passes inspection on a quick read"; a 400-line reverse-engineering document from static analysis missed three runtime bugs |
| [Nadeem](https://www.aymannadeem.com/artificial/intelligence,/developer/tools/2026/09/24/plan-mode-is-dead.html) | 2026-09-24 | Founder post-mortem of a planning-first app | "I conflated planning with a plan" (HN: 593 points) |

Scott Logic's measurements (single author, hobby app, not a controlled study):

| Measure | Spec Kit, feature 1 | Spec Kit, feature 2 (GPS) | His regular approach, same end goal |
|---|---|---|---|
| Agent execution time | 33m30 | 23m30 | 8m |
| Code produced | 689 lines | about 300 lines | 1,000 lines, no markdown |
| Markdown produced | 2,577 lines | 2,262 lines | none |
| Human review | 3.5 hours | about 2 hours | 15 minutes of code review plus 9 minutes of functional test and fixes |

His conclusion: "I am a lot more productive without SDD, around ten times faster." The constitution step alone took about 4 minutes and 161 to 189 lines. Read it as the cost of ceremony on small work, not as a verdict on SDD for work that is large, repeated or needs alignment among several people.

### 4.2 What the positive evidence is, and is not

- **Correlational.** Scoring the artifacts of 114 open-source SDD repositories against ISO/IEC/IEEE 29148 and deriving 40 practices in 10 quality dimensions (endorsed by 15 practitioners), the EPIC study found the top third of repositories by spec quality spent 11.8% of commits on bug fixes against 20.4% in the bottom third, with a median of 4x as many contributors ([EPIC](https://arxiv.org/abs/2610.07534), 2026-10-06). One practitioner's rule: write "must" instead of "should", because the agent may treat "should" as optional.
- **Task-specific.** An agent that writes semi-formal pre and post-conditions before generating tests detected 9.8 percentage points more production bugs at Google (p = 0.0352) and gained 2.5 points of branch coverage ([paper](https://arxiv.org/abs/2608.17177)).
- **Vendor-reported.** The PKFARE story (five weeks for 100,000 lines) is AWS-published; Spec Kit's own guide says its timeline shows "adoption rather than measured time savings".
- **Practitioner estimates.** HumanLayer's "2-3x faster, safely" and its roughly 40% one-shot guess are judgment, not measurement.
- **Right-sizing is where the field converged.** BMAD v6.12 decides the ceremony "after investigating it, not before"; Kiro offers Quick Spec without gates and recommends plan mode for 15-60 minute tasks; HumanLayer uses one combined plan document for medium tasks and all four phases only for large ones. That matches the bounded-scope boundary below.

### 4.3 Passing the gates is not the same as quality over time

[SlopCodeBench](https://arxiv.org/abs/2603.24755) (36 problems, 196 checkpoints, 15 agents; v2 revised 2026-05-07) has agents extend their own code as specifications evolve. "No agent fully solves any problem end-to-end, and the best agent passes 14.8% of checkpoints"; structural erosion rose in 77% of trajectories and verbosity in 75.5%; agent code is 2.3x more verbose and 2.0x more eroded than 473 open-source Python repos; explicit quality guidance reduced initial verbosity and erosion "by up to a third, without affecting degradation rates". HumanLayer's three-problem subset run gave the result above (Opus 5 passed 4 of 17 strictly, 24%). Spec and plan gates catch intent gaps but do not by themselves measure maintainability.

### 4.4 Evidence scoreboard

| Claim | Best evidence | Type | Strength |
|---|---|---|---|
| Better specs mean fewer bug fixes | EPIC: 11.8% vs 20.4% of commits | Correlation across 114 repos | Weak to moderate |
| Spec-first test generation finds more bugs | Google: +9.8 points, p = 0.0352 | Controlled, production bugs, one company | Moderate, narrow |
| SDD speeds delivery | PKFARE (vendor); HumanLayer 2-3x; Scott Logic roughly 10x slower | Vendor and single-author accounts | Anecdotal, pointing both ways |
| SDD is waterfall | Marmelab, Scott Logic, Radar, Nadeem | Opinion plus anecdotes | Unproven either way |
| Agents follow the spec | Böckeler, Marmelab, hubreb | Hands-on anecdotes | Agents skip or reinterpret steps |
| Green gates mean maintainable code | SlopCodeBench | Benchmark | Negative |
| End-to-end success rate of an SDD pipeline | None found | n/a | Unmeasured |

## 5. Why the tools stopped trusting the agent's "done"

- Spec Kit added the append-only converge pass and a bug process where "Missing verification is not a successful fix".
- BMAD counts an unregistered test as missing, replaced the acceptance-criteria checkbox ritual with an intent auditor, and a reviewer asks whether any test would fail if the behavior broke.
- Agent output flowing between steps is untrusted input: Spec Kit warns of no shell-escaping filter, Kiro wraps captured outputs in delimiters, and its IDE asks before an agent edits workflow recipes. The controller-side version is each agent writing JSON and deterministic code merging and validating.

## 6. Where specs fit a durable SDLC pipeline

### 6.2 Bounded scope is what makes a spec worth writing

A realistic scope for an unattended pipeline is deterministic feature kinds done many times before, UI built from existing primitives, mostly CRUD. Product judgement stays in a human-reviewed planning step upstream (the PRD and design the run starts from), and a draft PR comes out the other end for human review. Pure greenfield with no precedent is out of scope. This is the conclusion HumanLayer reached by the harder road: front-load human alignment and put review where plan errors multiply. Without a bounded scope the plan is the spec problem (what is the product?); with one, the plan is a template-shaped artifact a critic can check mechanically.

### 6.3 Where the SDD tool stops and a durable controller starts

| Situation | Reach for | Why |
|---|---|---|
| Interactive work under about an hour | A plan mode, or Kiro Quick Spec | Kiro suggests plan mode for 15-60 minute tasks; ceremony costs more than it saves on small work |
| Brownfield, change-sized work reviewed by a team | OpenSpec | Delta specs and propose, apply, archive fit changes to existing code |
| Greenfield with PRD, architecture and story process | BMAD | Heavy; v7 is in flux |
| AWS-centric team that wants IDE gates and property tests | Kiro | Commercial; Workflows and PBT features differ by surface (PBT is IDE-only) |
| Templates plus a converge loop, own the controller | Spec Kit | MIT; engine's resume re-runs the blocked step |
| Unattended multi-hour runs across pods | Own durable controller with borrowed artifact templates | No tool documents mid-step durability |

### 6.4 What the research suggests changing

1. **Make the plan typed and checkable.** Anthropic found a model "less likely to inappropriately change or overwrite JSON files compared to Markdown files" ([harness post](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)), which supports typed JSON artifacts with IDs per acceptance criterion; use "must" in requirement text.
2. **Put a non-LLM checker next to the architect critic.** Kiro's SMT pass finds ambiguity, inconsistency and incompleteness; the cheap version is schema checks (every criterion has an ID, every endpoint has a test).
3. **Turn acceptance criteria into executable properties** and add a mutation check on changed lines ([AI Code Review and Quality Gates](ai-code-review-and-quality-gates.md)).
4. **Right-size by phase kind.** SINGLE for one-shot tasks, GATED where the plan carries risk, with ceremony decided after investigation.
5. **Validate content, not presence, in idempotency checks.**
6. **Keep specs alive.** The documented risk is "drift between specification and code"; a spec-delta artifact written into each PR (OpenSpec's delta specs and archive step are the model) keeps the spec a maintained asset rather than paperwork.
7. **Cap iterations and let critics pass.** Kiro requires `maxIterations`, `/goal` defaults to 5, and loop phases should carry a maximum with the counter in durable state.
8. **Re-test scaffolding at each model upgrade.** Agent OS v3 retired phases because models improved; Anthropic's "every component in a harness encodes an assumption about what the model can't do on its own" ([harness design](https://www.anthropic.com/engineering/harness-design-long-running-apps)) is the rule.

## 7. Practical takeaways

1. Right-size the ceremony: one-shot tasks get no spec, medium tasks one combined plan, large or risky work the full chain.
2. Put the human where a bad line of plan costs hundreds of bad lines of code: review the plan, not just the diff.
3. Keep spec artifacts typed, small and ID-addressable; write requirements as "must".
4. Convert acceptance criteria into executable checks, and check the checks with mutants.
5. Never treat file existence as completion; validate content.
6. Let critics return PASS, cap every loop, and escalate by default.
7. Persist the plan outside the agent session (a plan held in a session was lost on compaction and container restart in one major tool).
8. Treat agent output between steps as untrusted input.
9. Measure cost per verified outcome and human commits per merged PR, not tokens.
10. Re-audit prompts, gates and scaffolding at every model upgrade.

## 8. Gaps and caveats

- No source reported a measured end-to-end success rate or time saved for any SDD pipeline in production; positive evidence is correlational (EPIC), task-specific (Google) or vendor-reported (PKFARE).
- Tessl's spec-as-source Framework status (deprecated or still available) is inferred only from the current docs index, overview and homepage, not from an announcement.
- OpenAI's "Harness engineering" post, which HumanLayer and Symphony reference, returned 403 and is unverified here; HumanLayer's QRSPI talk is confirmed only by title.
- Kiro Crew pricing, Cursor's version and pricing, and Claude Code plan pricing were not checked.
- Star counts cited are self-reported by vendors; GitHub release pages were not opened (repos and changelogs were read as raw files).
- The Scott Logic, Marmelab, hubreb and HumanLayer accounts are single-author. A reported claim that a Kiro agent caused a cloud cost-tool outage appeared only in a secondary search summary and is not included.

## Unverified leads

- [An Accidental Blackboard](https://martinfowler.com/articles/exploring-gen-ai/an-accidental-blackboard.html) (Giles Edwards-Alexander, Thoughtworks Exploring Gen AI series, dated 2 September 2026): found while checking dates and not opened in full, so nothing from it is cited above; the title points at the blackboard coordination pattern.

## Related

- [AI Code Review and Quality Gates](ai-code-review-and-quality-gates.md)
- [Context Engineering and Memory](context-engineering-and-memory.md)
- [Human in the Loop](human-in-the-loop.md)
- [Reward Hacking and Verification](reward-hacking-and-verification.md)
- [Evals for Agentic Pipelines](evals-for-agentic-pipelines.md)
- [Autonomous Coding Agents](autonomous-coding-agents.md)
- [Cost Economics](cost-economics.md)
