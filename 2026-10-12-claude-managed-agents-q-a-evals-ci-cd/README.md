# Claude Managed Agents Q&A: Evals, CI/CD and Multi-Tenancy (Part 4 of 4) — Anthropic

*How to version agent configs in CI/CD, isolate customers, add GPU sandboxes, and keep long-running Claude Managed Agents responsive.*

Source: [Claude Managed Agents Q&A: Evals, CI/CD and Multi-Tenancy (Part 4 of 4) — Anthropic](https://www.youtube.com/watch?v=QSeaZ6qHyXs) · Priyanka Phatak and Gabe Cemaj · AI Engineer · 19 min

## TL;DR

This Q&A closes a four-part Claude Managed Agents workshop, covering config versioning, multi-tenancy, evals, and long-running agents. Long-lived agent resources can be declared as YAML in your codebase and applied by a Terraform-style CLI in CI/CD. For customer isolation, you choose the boundary yourself, either a workspace or agent per customer, and you can map sessions to customers with metadata. Custom compute such as GPUs requires self-hosted sandboxes, and a sub-agent can hold custom tools so the main agent stays free to receive messages.

## Key takeaways

- Declare long-lived agent resources as YAML in your codebase and have CI/CD run the Terraform-style ant CLI to update them whenever a file changes.
- You cannot message an agent while it is waiting for a custom tool result; the message returns a 400, while messages sent during other model work can be queued.
- For multi-tenancy, pick the isolation boundary yourself (an agent, a workspace, or an org per customer) and keep a mapping of which customer maps to which agent.
- With one workspace per customer, keep the agent template (system prompt, model, tools) in YAML and let CI push it to every workspace; with one shared workspace, store a customer ID in each session's metadata.
- If customers need data segregation, give them separate workspaces, because resources are allocated per workspace and any API key in a workspace can access its resources.
- Anthropic's provided sandboxes are all the same today, so custom GPU or memory needs require self-hosted sandboxes, such as running the agent loop on Modal.
- Garbage in, garbage out applies to evals: ill-defined evals can reach 100% and mean little, so seed your dataset from historical sessions.
- Sessions past six hours need no special setup; define exit criteria as an outcome rubric, and check progress via the events API or a custom status tool.

## Managing agent resources as YAML in CI/CD

[▶ 00:00:00](https://www.youtube.com/watch?v=QSeaZ6qHyXs&t=0s)

Long-lived agent resources can be declared in YAML and managed with a Terraform-style CLI that wraps the [Claude Managed Agents](https://platform.claude.com/docs/en/managed-agents/overview) API. The YAML files live in your codebase, and CI/CD runs the CLI to update those resources whenever a file changes. Anthropic uses the same approach internally: durable resources are checked in and applied from code.

## Messages during custom tool waits

[▶ 00:01:10](https://www.youtube.com/watch?v=QSeaZ6qHyXs&t=70s)

You can't send a message while the model is waiting for a custom tool result. Execution pauses at that point, and a message sent then returns a 400. While the model is doing other work, messages can be queued. The platform expects the tool result to come next, and blocking these calls keeps ordering intact.

## Running evals: harness updates and data quality

[▶ 00:02:45](https://www.youtube.com/watch?v=QSeaZ6qHyXs&t=165s)

Anthropic runs many evals when new models arrive, and updates harness assumptions early to drop what's no longer needed. The cloud lets it run thousands of sessions in parallel to tune prompts.

For your own agents, the advice is simple: garbage in, garbage out. Ill-defined evals can hit 100% and mean little. Seed the dataset from historical sessions and score against them.

## Sharing resources through workspaces

[▶ 00:04:42](https://www.youtube.com/watch?v=QSeaZ6qHyXs&t=282s)

Resources such as agents, memory stores and MCP channels are bound to a workspace, and any API key in that workspace can access them. A platform admin creates workspaces and decides who gets into which, so group people who need the same resources. There's no per-resource sharing control; partial sharing is something builders design into their own integration for end users.

## Agents reviewing agent-written code

[▶ 00:06:29](https://www.youtube.com/watch?v=QSeaZ6qHyXs&t=389s)

The workshop aimed at this: agents that review code. Wired to CMA, each PR starts sessions with sub-agents that hunt bugs. Internally, this cuts review time and bugs, though PR volume remains an open problem.

Code must run. Claude can run the app in the sandbox, self-validate as a QA agent, drive the browser, and possibly return a video. No tunneling yet.

## Tenant isolation is a design choice

[▶ 00:08:48](https://www.youtube.com/watch?v=QSeaZ6qHyXs&t=528s)

Claude Managed Agents does not support on-prem deployment. For separating customers on the hosted service, you define the boundary yourself. The options given are:

- An agent per customer, which segregates each customer's data.
- A workspace per customer.
- An org per customer, for true isolation.

Whichever you choose, keep a mapping of which customer maps to which agent.

## One agent, many customers: workspace or metadata mapping

[▶ 00:09:54](https://www.youtube.com/watch?v=QSeaZ6qHyXs&t=594s)

Two patterns work. With **one workspace per customer**, keep the agent template (system prompt, model, tools) in YAML in your code and let CI push it to every workspace. Or put all customers in **one workspace** with a single agent, and store a customer ID in each session's metadata field to map sessions to customers.

If customers need data segregation, use separate workspaces, since resources are allocated per workspace. The speaker confirmed that a customer requiring dedicated data should get its own workspace.

## Custom compute needs self-hosted sandboxes

[▶ 00:12:10](https://www.youtube.com/watch?v=QSeaZ6qHyXs&t=730s)

Anthropic's provided sandboxes are all the same today, so custom memory or GPU needs mean self-hosted sandboxes.

- Run the agent loop on Modal, which offers GPUs. Anthropic has cookbooks and a near-first-party integration for it.
- For other scale needs, use a different sandbox provider. Cookbooks cover Modal, Daytona and others.

Sandbox specifications are listed in the [Managed Agents docs](https://platform.claude.com/docs/en/managed-agents/overview).

## Multiplayer Slack agents on CMA

[▶ 00:13:20](https://www.youtube.com/watch?v=QSeaZ6qHyXs&t=800s)

Map each Slack thread to a session. Inject every user message as a `user.message` naming the sender.

Share memory across sessions so agents learn from each other.

Add a Slack post tool so the agent explicitly chooses when to reply, not mirroring output.

For Cloud Tag-style behavior, use one agent per channel, each with separate memory limited to that channel.

## Running agents for hours without special setup

[▶ 00:14:34](https://www.youtube.com/watch?v=QSeaZ6qHyXs&t=874s)

No special setup is needed for sessions past six hours; they keep running. The speaker's recommended method is the **outcome** system: define exit criteria as a rubric, and Claude iterates until it meets them.

To check progress, read the session's event stream via the events API. Or add a custom status tool that Claude calls at intervals.

## Keeping the coordinator free with a sub-agent

[▶ 00:16:30](https://www.youtube.com/watch?v=QSeaZ6qHyXs&t=990s)

To handle messages during a custom-tool wait (reportedly a 400), the speaker suggested putting custom tools on a sub-agent, keeping the coordinator free to receive messages. The sub-agent emits the tool use; you return its result. Sub-agents run in a separate thread of the same session; you talk only to the main thread, which may sit idle meanwhile.

## Links & resources

- [Claude Managed Agents docs — overview and reference](https://platform.claude.com/docs/en/managed-agents/overview) — <https://platform.claude.com/docs/en/managed-agents/overview> · video description
- [Anthropic engineering post on managed agents](https://www.anthropic.com/engineering/managed-agents) — <https://www.anthropic.com/engineering/managed-agents> · video description
- [Speaker's GitHub — workshop repo with sample code, URL not shown](https://github.com/gcemaj) — <https://github.com/gcemaj> · video description
- [AI Engineer YouTube channel — other talks and repo walkthroughs](https://www.youtube.com/@aiDotEngineer) — <https://www.youtube.com/@aiDotEngineer> · video description
