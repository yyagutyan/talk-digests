# Scheduled Agents, Dreaming and Q&A: Claude Managed Agents (Part 3 of 4) — Anthropic

*How to fan one request out to parallel agent sessions, run agents on a schedule, and use Dreaming to improve memory from past runs in Claude Managed Agents.*

Source: [Scheduled Agents, Dreaming and Q&A: Claude Managed Agents (Part 3 of 4) — Anthropic](https://www.youtube.com/watch?v=qJtcEDc_WJU) · Gabe Cemaj · AI Engineer · 26 min

## TL;DR

Agent builders need to run recurring work, coordinate many sessions at once, and keep agents improving over time, but the basic primitive is a single session. The talk shows a main agent that dispatches one session per financial artifact through a custom tool, deployments that run an agent on a schedule, and Dreaming, a research preview that reviews 30 days of past sessions to update a memory store. Together these pieces make long-running, recurring agent work practical without custom infrastructure, since the platform handles compaction and capacity. The speaker stresses that Dreaming's changes should be checked with your own evals before you rely on them.

## Key takeaways

- A main agent can call a custom `dispatch analysis` tool to start one session per financial artifact, as the demo did for Google and Microsoft.
- A deployment combines an agent definition, a schedule, initial messages, and optional resources such as memory stores, repos, and files, and it runs a new session for one specific agent each time it fires.
- Memory stores are independent entities that persist state across sessions, and you attach them to whichever sessions you choose rather than to a deployment.
- Dreaming is a research preview: you pick a memory store, a model, and optional instructions, then run the last 30 days of sessions through it to infer your tasks and write an updated store.
- Observability starts from the raw event log, where the debug view shows when each model call starts and finishes so latency can be derived, and you filter events yourself to build the metrics you need.
- The platform runs compaction for you, so sessions can last for days (one production user has reached 10-hour sessions), and you do not need to recompact the persistent store offline.
- Workspaces are the only access boundary today, with no RBAC beyond them, so multi-tenant apps need a separate authorization layer; scaling needs no pod or capacity settings, only API calls and rate limits.
- For a fixed target, define an outcome with an exact rubric and a max iteration count so the agent loops until it passes, and run evals on each new model because the right amount of skill versus harness keeps shifting.

## One main agent fans out to a session per ticker

[▶ 00:00:00](https://www.youtube.com/watch?v=qJtcEDc_WJU&t=0s)

A main agent checks whether it has enough information. If not, it calls a custom `dispatch analysis` tool that starts one session per financial artifact. The demo dispatches two tickers, Google and Microsoft, and kicks off a run for each.

Each session runs collaborating agents, with an outcome definition for self-supervision. Sessions learn from one another through **memory stores**.

## Scheduled deployments for recurring agent work

[▶ 00:01:01](https://www.youtube.com/watch?v=qJtcEDc_WJU&t=61s)

For recurring work, like a daily analysis of watched companies, the talk introduces **deployments**. They are API-first, with the SDK as the main interface for custom UIs and workflows.

A deployment combines an agent definition, a schedule, initial messages, and optional resources (memory stores, repos, files). Runs start on schedule or on demand, from the console or a custom UI.

## Dreaming: learning from past sessions

[▶ 00:02:54](https://www.youtube.com/watch?v=qJtcEDc_WJU&t=174s)

Dreaming is a research preview. You pick a memory store, a model and optional instructions, then run the last 30 days of sessions through it. Sub-agents check whether memory captured what was needed and whether issues recurred.

In the demo, it found gaps in a skill and added the lesson to the memory store. The speaker expects this to extend to skills and agent prompts.

## Observability starts from the raw event log

[▶ 00:04:40](https://www.youtube.com/watch?v=qJtcEDc_WJU&t=280s)

Every session event is available live in the console or through the API. The transcript view is a readable summary, while the debug view shows every event, including when a model call starts and finishes, so latency can be derived. Metrics aren't predefined. You filter the raw events yourself and can ask for anything missing. To use your own stack, such as OpenTelemetry, build a connector on the API, since the platform stays unopinionated about tooling.

## Compaction keeps long-running agents going

[▶ 00:05:59](https://www.youtube.com/watch?v=qJtcEDc_WJU&t=359s)

Asked whether a long-running agent's persistent store grows until it must be recompacted offline, the answer was no: the platform runs compaction for you, so sessions can last days. One user has reached 10-hour sessions in production. In theory, that means an agent doing a daily task could run for months, or indefinitely, without its persistent store blowing up.

## Letting Claude choose the agent topology

[▶ 00:06:43](https://www.youtube.com/watch?v=qJtcEDc_WJU&t=403s)

There is no built-in graph or topology builder yet. For now, you spawn sub-agents and tell Claude what you want. The speaker says people want to constrain how Claude works, but as models get smarter, the team keeps removing those guardrails. Letting Claude choose the topology reportedly outperforms a human-designed one. Asked about graph workflows in the UI: "Not yet. Not yet. Not yet. But maybe soon."

## Workspaces as the current access boundary

[▶ 00:07:46](https://www.youtube.com/watch?v=qJtcEDc_WJU&t=466s)

Today, **workspaces** are the main way to separate agents. Put each customer in its own workspace and give specific users access to it. Anyone with access to a workspace can use every agent inside it. The speaker acknowledges this leaves a gap in granular control, and says finer-grained RBAC is on the roadmap.

## Deployments schedule sessions; memory stores attach on demand

[▶ 00:08:53](https://www.youtube.com/watch?v=qJtcEDc_WJU&t=533s)

A **deployment** is not a snapshot of agents. It runs sessions for one specific agent on a schedule. For example, a research agent can be set to research a company once a quarter, creating a new session each time that produces a report.

A **memory store** is separate. It persists state across all sessions, and it is an independent entity that you attach to whichever sessions you choose.

## Defining a harness and checking agent quality

[▶ 00:09:38](https://www.youtube.com/watch?v=qJtcEDc_WJU&t=578s)

A harness here means the tool loop: context, session state, and task execution. Builders often mean coordinating multiple sessions as one unit of work; the platform gives a single session as the primitive.

For quality, run evals across varied tasks. For a fixed target, define **outcomes**: an exact rubric plus max iterations, and the agent keeps looping until it passes.

## Skills versus multi-agent: a moving boundary

[▶ 00:11:17](https://www.youtube.com/watch?v=qJtcEDc_WJU&t=677s)

The line between thick harness, thin harness and thick skills keeps moving. As Claude gets smarter, the help you give it fades, so what you need today may not hold tomorrow. The answer is to run evals on each new model, though that is tiring. Skills are a good abstraction today, giving the model enough information. They are expected to thin as models act unaided. One speaker finds multi-agent setups more useful; mileage varies by task.

## Dreaming needs evals to pick a direction

[▶ 00:12:57](https://www.youtube.com/watch?v=qJtcEDc_WJU&t=777s)

**Dreaming** reviews a chosen window of agent transcripts, infers the tasks you're pursuing, and writes a new memory store for them. Anthropic runs evals to confirm it helps; customers saw significant gains. Repeated dreaming passes can diverge, and one session's verdict isn't enough, so evals should choose the direction. Use your own evals. Dreaming is an early research preview.

## Managed Agents is inspired by Claude Code, not built on it

[▶ 00:15:07](https://www.youtube.com/watch?v=qJtcEDc_WJU&t=907s)

Asked whether Managed Agents is just Claude Code underneath, the presenter says the team took the Claude Code harness as inspiration, not as a base. Claude Code is an out-of-the-box terminal product. Managed Agents is an API where you bring your own context, information and capabilities. It aims to match Claude Code's gains on non-coding tasks, and to run reliably at scale as infrastructure.

## Console access, model limits and multi-tenancy

[▶ 00:17:14](https://www.youtube.com/watch?v=qJtcEDc_WJU&t=1034s)

The console is on Claude Platform on AWS, not Bedrock. Only Claude models are supported for now. End users need no console account: you hold one API key and pay per token.

For multi-tenant apps, use **workspaces** as the tenancy primitive, not bring-your-own-key, which loses observability. Either share one workspace and map agents to customers yourself, or use one workspace per customer, which means recreating agents.

Workspaces are the only access boundary and there is no RBAC beyond them, so you need your own layer on top.

## Scaling by creating sessions, not pods

[▶ 00:20:09](https://www.youtube.com/watch?v=qJtcEDc_WJU&t=1209s)

Scaling takes no infrastructure settings. You can create as many sessions as you want, and there is no parameter for choosing how many pods to run. Rate limits still apply. There is no Kubernetes-style HPA or minimum-pod, timeout, or similar scaling configuration to set, because the platform manages capacity. Your only job is to make API calls.

## Shipping to customers: your product wraps the API

[▶ 00:21:09](https://www.youtube.com/watch?v=qJtcEDc_WJU&t=1269s)

Asked how to ship an agent-backed product to customers, the Anthropic speaker says this already runs at scale. Notion's external agents and Atlassian's Jira integrations are built on [Managed Agents](https://platform.claude.com/docs/en/managed-agents/overview). The pattern:

- Build your own product and UI for your customers.
- Call Managed Agents through the API for the agent itself.
- Handle all API calls and session management on your side.

## Cowork or managed agents: which to choose

[▶ 00:23:44](https://www.youtube.com/watch?v=qJtcEDc_WJU&t=1424s)

The console quick start lets non-technical users describe an agent to Claude and get a first version. Managed Agents is an API product, so it is more intimidating. Users comfortable with Claude Code can use its managed agent skill, which walks them through building a first agent in natural language.

Cowork suits personal agents. Managed agents suit agents for a team, an internal organization, or customer-facing products.

To port a Cowork setup, describe it and supply the API skill. The speaker expects Claude can make the translation.

## On-prem deployment for enterprises

[▶ 00:25:18](https://www.youtube.com/watch?v=qJtcEDc_WJU&t=1518s)

Enterprises with sensitive data sets may want to run managed agents in-house. Some already run their own secure proxies to Anthropic's models, and they want to allocate compute to scale on their own terms. A customer question asked whether an on-prem option is planned. The answer: many enterprise customers have asked for it, Anthropic is seriously considering it, but there is nothing to share yet.

## Links & resources

- [Workshop repo — speaker's GitHub, linked in the description](https://github.com/gcemaj) — <https://github.com/gcemaj> · video description
- [Claude Managed Agents docs — official platform documentation](https://platform.claude.com/docs/en/managed-agents/overview) — <https://platform.claude.com/docs/en/managed-agents/overview> · video description
- [Managed Agents engineering post — Anthropic's write-up](https://www.anthropic.com/engineering/managed-agents) — <https://www.anthropic.com/engineering/managed-agents> · video description
