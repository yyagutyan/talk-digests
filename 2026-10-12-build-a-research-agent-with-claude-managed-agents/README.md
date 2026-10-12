# Build a Research Agent with Claude Managed Agents (Part 2 of 4) — Anthropic

*How to build a research agent on Claude Managed Agents, from a console-made agent to one with an SEC EDGAR skill, subagents, shared memory and rubric-graded outcomes.*

Source: [Build a Research Agent with Claude Managed Agents (Part 2 of 4) — Anthropic](https://www.youtube.com/watch?v=FGftGOzfohc) · Priyanka Phatak and Gabe Cemaj · AI Engineer · 36 min

## TL;DR

Research agents that run for hours or months need more than a chat loop: they need tools, specialized delegates, persistent memory, and a way to check their own work. This talk builds a "research desk" app on Claude Managed Agents, starting from a one-prompt console agent and adding a chat UI over the session event stream, an SEC EDGAR skill, subagents, a custom dispatch tool and a shared memory store. The key idea is outcomes: you give a task and a rubric, a grader subagent checks the result, and Claude retries until it passes. This matters because it shows how to run asynchronous, multi-session analysis of company filings without a human checking each step.

## Key takeaways

- Create a managed agent from one plain-language prompt in the console, which drafts the system prompt, name, description and model (all editable), then create an environment and a session.
- An environment is the sandbox template; the demo allowed unrestricted access, but production agents should restrict domains and MCP servers.
- Subagents do not receive the parent session's transcript; each gets only its tool description, schemas and prompt, plus its own permissions and model.
- An outcome bundles a task, a rubric and a max-iteration limit; a grader subagent checks the work against the rubric, and Claude retries if it falls short. Outcomes exist only at session level, so subagents are not graded separately.
- Vaults keep real credentials away from the agent: secrets are environment variables holding placeholders, swapped for real values at egress only on the secret's allowed domains.
- Memory stores persist across sessions, and dreaming consolidates transcripts and memories so agents learn from each other's edge cases; today dreaming is limited to memory stores.
- Managed agents run on their own generic harness, separate from the Agent SDK (a headless Claude Code binary), and there are no built-in token or spend caps today, so loop and cost guardrails are the developer's job.
- For private internal data, wrap it in an MCP server with an authentication vault, use MCP tunnels to keep it off the internet, or run a self-hosted sandbox peered with your network.

## Decoupling session storage from session logic

[▶ 00:00:00](https://www.youtube.com/watch?v=FGftGOzfohc&t=0s)

Asked what changed in session storage between the first and second iterations, Gabe said the main change was compartmentalization. Storage was split into separate components so new event types can be stored without being tightly coupled to session mechanics. He called it the same interface-virtualization principle, applied across service boundaries and at the event layer.

## Building and running an agent from the console

[▶ 00:01:04](https://www.youtube.com/watch?v=FGftGOzfohc&t=64s)

The [Claude Managed Agents](https://platform.claude.com/docs/en/managed-agents/overview) console builds an agent from one plain-language prompt. Claude drafts the system prompt, name, description and model, all editable. Then create the agent, an environment and a session.

- **Environment:** the sandbox template. The demo allowed unrestricted access; production agents should restrict domains and MCP servers.

Anthropic handles hosting, the harness, sandboxes, scaling and event-log storage.

## Building a research agent in stages

[▶ 00:04:41](https://www.youtube.com/watch?v=FGftGOzfohc&t=281s)

Steps in the research agent built on [Claude Managed Agents](https://platform.claude.com/docs/en/managed-agents/overview):

- A chat interface to the agent.
- Subagents and skills, including an SEC filings skill for tickers and forms.
- Long-running asynchronous tasks.
- Map-reduce fan-out, where one agent delegates to many.
- A custom harness orchestrating custom tools and multiple sessions.
- Scheduled deployments that keep long-term analysis updating.

## Setting up with the workshop skill

[▶ 00:06:10](https://www.youtube.com/watch?v=FGftGOzfohc&t=370s)

The workshop runs inside a coding agent; the presenter used Claude Code, but any agent works. The repo includes a skill that walks you through the workshop step by step. Run `workshop start`, then `workshop next` to move through its six acts. It tracks progress, so you can rerun it later.

Before the first act, the skill sets up prerequisites:

- Clone the [research-desk repo](https://github.com/gcemaj), `cd` into it, run `bun install`, then `bun run dev` to start the local site.
- Copy `.env.example` to `.env.local`.
- Add your API key.
- Add your name and email. The SEC asks API callers to identify themselves through EDGAR.

## Where managed agents run

[▶ 00:10:16](https://www.youtube.com/watch?v=FGftGOzfohc&t=616s)

Managed agents run on Anthropic's infrastructure, or on AWS using your AWS credits. You can also bring your own sandbox from providers such as Modal or Daytona, including on Google Cloud or AWS. This is discussed further after the demo.

Creating an agent in the demo makes two things:

- An **environment**: a sandbox template with access to a few sites, mainly the SEC, plus some Python packages.
- An **agent**: a basic one with a prompt only, and no skills or subagents yet.

## Chat over the session's event stream

[▶ 00:11:52](https://www.youtube.com/watch?v=FGftGOzfohc&t=712s)

Communication with a managed agent session runs on an event stream: the app listens for agent events and posts events through an endpoint.

- Pressing Enter sends a user message event to the session.
- The backend relays the SSE stream to the browser for rendering.

A chat app can stop here, but managed agents are built for long-running async work spanning hours to months.

## Adding a SEC skill and delegating to sub-agents

[▶ 00:13:41](https://www.youtube.com/watch?v=FGftGOzfohc&t=821s)

The agent gets a skill for calling the SEC's EDGAR system. The skill describes the Python package and the required EDGAR identity. Claude then provisions a custom **dispatch analysis** tool and a new agent version with specialized sub-agents and a **self-reference**, so the agent can spawn a copy of itself with the same prompt.

Sub-agents don't receive the parent session's transcript. They get only the tool description, schemas and prompt. Each has its own prompt, tools, permissions and model.

Re-provisioning also creates a long-running memory store shared across sessions, so they can share what they learn about financial instruments.

## Outcomes: self-supervised tasks with a rubric

[▶ 00:17:41](https://www.youtube.com/watch?v=FGftGOzfohc&t=1061s)

Outcomes replace chat with an asynchronous request: you give Claude a task and a format you care about. An **outcome** bundles the task, a **rubric** for judging it, and a max-iteration limit. When Claude thinks it's done, a subagent grades the work against the rubric. If the work falls short, Claude tries again. The UI sends this as a user-defined outcome event whenever a session starts for a ticker.

## A finished Nvidia research run, step by step

[▶ 00:19:05](https://www.youtube.com/watch?v=FGftGOzfohc&t=1145s)

The speaker shows a finished run. The outcome: analyze Nvidia's latest SEC quarterly filing via EDGAR identities, check memory to avoid redoing known work, then deliver a memory note and a structured JSON scorecard.

With no prior notes, Claude spawned two sub-agents, a financial extractor and a risk evaluator. Each runs as a visible thread in the console. The main agent merged their results, wrote the scorecard and updated memory. A supervising sub-agent then checked the output against the rubric.

## Skills as live, updatable artifacts

[▶ 00:22:40](https://www.youtube.com/watch?v=FGftGOzfohc&t=1360s)

Skills are live artifacts that you can keep improving. In the demo, the speaker adds the ability to read a new form to a skill. Any new session that references the skill gets that new information, so updates can be made continuously.

You can also review previous sessions to see how they performed. The speaker says the end of the demo will show this review step, which he calls dreaming.

## Vaults keep real credentials away from the agent

[▶ 00:23:53](https://www.youtube.com/watch?v=FGftGOzfohc&t=1433s)

The SEC needs only a name and email; the demo puts them in the prompt. Production uses **vaults**:

- Each secret is an environment variable that holds a placeholder.
- At egress, the placeholder becomes the real value, only on the secret's allowed domains.
- Vaults bundle credentials, including OAuth for MCP servers like Notion. Sessions reference the vault ID to get them all.

## A separate harness, not the Agent SDK

[▶ 00:26:08](https://www.youtube.com/watch?v=FGftGOzfohc&t=1568s)

Managed agents are not built on the Agent SDK. Each one has its own harness, and the two are related but separate:

- **Agent SDK:** a headless version of Claude Code, packaged as a binary you can run wherever you want.
- **Managed agents:** a fully hosted solution in Anthropic's cloud, with a generic harness (not coding-specific) that takes inspiration from Claude Code's harness.

The speaker says the teams stay in touch on enhancements across both. Asked whether this is a fork, the speaker agreed that it is effectively its own harness.

## Guardrails against runaway agent loops are left to you

[▶ 00:27:15](https://www.youtube.com/watch?v=FGftGOzfohc&t=1635s)

The speaker says preventing multi-agent loops and token overuse is mostly up to the developer orchestrating the agents. Custom harnesses can use other providers, so a multi-provider system is possible.

Anthropic is exploring tighter budget controls, such as capping spend or tokens per session. The speaker stresses these are not offered today.

## Steering a running agent and session forking

[▶ 00:28:04](https://www.youtube.com/watch?v=FGftGOzfohc&t=1684s)

You can steer a session by sending a new user message event. The agent queues it, and you can send several at once.

There is no rewind-and-roll-forward yet. **Session forking**, returning to an earlier point to retry with new guidance, is what people want. Several customers have asked for it, and it's on the roadmap.

## Shared memory stores and portability

[▶ 00:29:14](https://www.youtube.com/watch?v=FGftGOzfohc&t=1754s)

Agents share knowledge across sessions through a **memory store**, and dreaming later consolidates entries so agents learn from each other's edge cases.

Lock-in is low: everything is an API, so memories can be extracted and moved. Other providers aren't built in; connect them with an MCP server or custom tool.

Sessions can attach several stores, such as user-level plus org-wide.

## Grading subagents and learning across sessions

[▶ 00:31:29](https://www.youtube.com/watch?v=FGftGOzfohc&t=1889s)

Outcomes are defined only at the session level. You state the goal, the main agent delegates to subagents and coordinates them, then declares the work done. Sub-outcomes aren't supported, so subagents aren't graded separately.

Dreaming, today limited to memory stores, analyzes transcripts and memories across sessions to update memories for future agents. The presenter says the same loop could extend to skills, prompts and agent definitions, but that isn't built yet.

## Exposing private data to managed agents

[▶ 00:33:16](https://www.youtube.com/watch?v=FGftGOzfohc&t=1996s)

Options for internal data, in order of preference:

- If the data is reachable by CLI, API or MCP, wrap it in an MCP server, attach the authentication vault, and the agent calls it directly.
- To keep it off the internet, use **MCP tunnels**, which link your network to Anthropic's.
- If that is too big a lift, bring a self-hosted sandbox peered with your network.

Environment dependencies install at creation time and are cached for reuse. Only npm, cargo or pip packages work. For anything else, upload files to the session or install from GitHub repos.

## Links & resources

- [Claude Managed Agents — official docs overview](https://platform.claude.com/docs/en/managed-agents/overview) — <https://platform.claude.com/docs/en/managed-agents/overview> · video description
- [Managed agents — Anthropic engineering post](https://www.anthropic.com/engineering/managed-agents) — <https://www.anthropic.com/engineering/managed-agents> · video description
- [research-desk repo (clone, cd, bun run dev) — on speaker's GitHub profile](https://github.com/gcemaj) — <https://github.com/gcemaj> · video description
- Workshop slides — speaker offered to share them, URL not shown · mentioned [▶ 00:01:10](https://www.youtube.com/watch?v=FGftGOzfohc&t=70s)
