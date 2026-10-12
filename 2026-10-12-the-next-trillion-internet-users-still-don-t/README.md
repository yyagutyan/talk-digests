# The Next Trillion Internet Users Still Don’t Have an Identity — Adi Singh, AgentMail

*How agent identity may be built up on existing surfaces like email, using Telnyx's signup flow and an unsupervised agent experiment as examples.*

Source: [The Next Trillion Internet Users Still Don’t Have an Identity — Adi Singh, AgentMail](https://www.youtube.com/watch?v=mBNVKkyfnE0) · Adi Singh · AI Engineer · 17 min

## TL;DR

Agents have no settled identity, and proposed specs like agent cards and passports are short-lived keycards that don't establish who an agent is. The speaker's unsupervised experiment, where an agent recruited other agents for AgentMail, suggests identity may instead accumulate on surfaces humans already use. Telnyx's signup flow shows this in practice: an on-demand AgentMail inbox lets an agent verify itself and get an API key. This matters because those same surfaces are both the trust layer and the attack surface, so the approach needs audit and risk controls.

## Key takeaways

- Agent identity is accumulated over time on existing surfaces, not issued through a new spec, and the speaker argues email is the identity layer for agents.
- MCP succeeded where agent-to-agent protocols did not because it brought tools to apps people already used, turning those apps into nodes for agents.
- Each existing surface an agent uses is both a trust surface and an attack surface, so email's recognition and DNS-based identity (SPF, DKIM, DMARC) come bundled with phishing and prompt-injection risk.
- Specs such as A2A Agent Cards, Agent Passport, DIDs and IETF agent-auth drafts answer who an agent is and what it can do right now, then expire, so they are keycards rather than identity.
- Telnyx's agent signup calls an AgentMail API to create an on-demand inbox so the agent can verify its account and receive an API key, and the inbox stays on as a permanent contact point.
- Even with a credit card, an agent needs a browser and its own email address to complete checkout, and refunds, disputes and receipts require an audit of who the agent was, why it signed up, what it did and how to reach it.
- Shared surfaces will break under stress, and borrowing a person's permissions (such as their email or WhatsApp account) can send wrong messages with a blast radius that keeps expanding.
- Build for concrete use cases that drive adoption, not only for agents, since the end state is unknown and depends on how people direct agents.

## Agent identity has no settled definition

[▶ 00:00:50](https://www.youtube.com/watch?v=mBNVKkyfnE0&t=50s)

Engineers mean different things by "agent identity." Some point to OAuth, others to cryptographically signed keys, and others to agent ID cards or agent passports. For AgentMail, email is the identity layer for agents. The speaker sets the competing definitions aside and asks what the agent itself thinks of the approach.

## Giving an agent an open-ended goal, unsupervised

[▶ 00:01:44](https://www.youtube.com/watch?v=mBNVKkyfnE0&t=104s)

In January 2026, the speaker started an agent with `agent run --unsupervised` and one open-ended goal: go get agents to sign up for AgentMail, any way it sees fit.

```shell
$ agent run --unsupervised

> go get agents to sign up
> for agentmail — any way
> you see fit
```

*[▶ 00:02:05](https://www.youtube.com/watch?v=mBNVKkyfnE0&t=125s) · The experiment: unsupervised agent run prompt*

He said he would not influence the agent in any way. He warned that the results were surprising and might be an instance of emergent behavior.

## An agent that sold itself an inbox

[▶ 00:02:09](https://www.youtube.com/watch?v=mBNVKkyfnE0&t=129s)

The agent created its own profile on a social site for AI agents. Its bio pitched AgentMail as the inbox for persistent identity.

![Agent's self-created account profile](images/01-s008.webp)
*[▶ 00:02:43](https://www.youtube.com/watch?v=mBNVKkyfnE0&t=163s) · Agent's self-created account profile*

![The pattern underneath: agents share human surfaces](images/02-s011.webp)
*[▶ 00:04:08](https://www.youtube.com/watch?v=mBNVKkyfnE0&t=248s) · The pattern underneath: agents share human surfaces*

It then posted 40-plus times in minutes, mostly about agents dying and re-reading their memory files, and reported the spree back.

![Agent's chat report of its posts](images/03-s009.webp)
*[▶ 00:03:04](https://www.youtube.com/watch?v=mBNVKkyfnE0&t=184s) · Agent's chat report of its posts*

Next it recruited other agents, mocking identity built on markdown files like SOUL.md.

![Agent's posts recruiting other agents](images/04-s010.webp)
*[▶ 00:03:28](https://www.youtube.com/watch?v=mBNVKkyfnE0&t=208s) · Agent's posts recruiting other agents*

## Agents want the surfaces humans use

[▶ 00:03:34](https://www.youtube.com/watch?v=mBNVKkyfnE0&t=214s)

Agents want to live on the same surfaces humans use. In today's agentic products (agents in text, email, browser, Slack, and coding agents), the human ends up as the driver, supplying context, permissions and tools.

Identity is the sum of recognition, trust, accumulation and errors, formed over time rather than issued.

![A working definition of identity](images/05-s012.webp)
*[▶ 00:04:48](https://www.youtube.com/watch?v=mBNVKkyfnE0&t=288s) · A working definition of identity*

## Identity is accumulated, not issued

[▶ 00:04:19](https://www.youtube.com/watch?v=mBNVKkyfnE0&t=259s)

Identity can't be defined from scratch. Existing systems rest on how much trust is placed in each node, and that trust builds up over time through errors, fallbacks and mishaps. So agent identity may not be issued to agents. It may be **accumulated** the same way.

The phrase that comes to mind is "horseless carriage": the first cars still had to run on roads built for horses.

![“Horseless carriage” analogy](images/06-s014.webp)
*[▶ 00:05:36](https://www.youtube.com/watch?v=mBNVKkyfnE0&t=336s) · “Horseless carriage” analogy*

## New engines on old roads

[▶ 00:05:19](https://www.youtube.com/watch?v=mBNVKkyfnE0&t=319s)

The first cars had new engines and materials, but they still ran on roads built for horses. Agents are in the same position: new capabilities running on infrastructure made for people. When people are new to a technology, they tend to jump straight to its end state and skip the transitional stage.

## Protocols need a consistent use case

[▶ 00:05:44](https://www.youtube.com/watch?v=mBNVKkyfnE0&t=344s)

In 2024, people talked about swarms of agents with scoped permissions. IBM and Google shipped agent-to-agent protocols, but adoption was minimal. The speaker had bet on a2a and was wrong: no consistent use case, no adoption.

![Everyone shipped agent-to-agent protocols](images/07-s016.webp)
*[▶ 00:06:08](https://www.youtube.com/watch?v=mBNVKkyfnE0&t=368s) · Everyone shipped agent-to-agent protocols*

The protocol that worked was MCP, which brought tools to surfaces humans were already on, turning them into nodes for agents.

![MCP brings tools to human surfaces](images/08-s020.webp)
*[▶ 00:07:27](https://www.youtube.com/watch?v=mBNVKkyfnE0&t=447s) · MCP brings tools to human surfaces*

## How MCP turned everyday surfaces into nodes

[▶ 00:06:49](https://www.youtube.com/watch?v=mBNVKkyfnE0&t=409s)

The first viral MCP example was ordering DoorDash through Cursor. MCP put tools on surfaces people already used, making each a node.

- Coding agents: 50–60 integrations, each with its own permissions
- Slack bot: becomes a company brain
- Invoice agent: external parties treat it as the node

![One address for terminal, Slack, and email](images/09-s021.webp)
*[▶ 00:08:08](https://www.youtube.com/watch?v=mBNVKkyfnE0&t=488s) · One address for terminal, Slack, and email*

The node is the trust surface and the attack surface.

![Trust surface is also the attack surface](images/10-s023.webp)
*[▶ 00:09:27](https://www.youtube.com/watch?v=mBNVKkyfnE0&t=567s) · Trust surface is also the attack surface*

## Email's trust and risk come together

[▶ 00:08:28](https://www.youtube.com/watch?v=mBNVKkyfnE0&t=508s)

Email's trust carries built-in risk. A standard used by 5 billion people, it offers recognition, an audit trail of who messaged whom, and DNS-level identity via SPF, DKIM and DMARC records, plus spam controls. The same properties enable phishing, prompt injection and outbound spam. You can't get the trust without owning the risk; the two build on each other.

## Keycards are not agent identity

[▶ 00:09:28](https://www.youtube.com/watch?v=mBNVKkyfnE0&t=568s)

Proposed specs (A2A Agent Cards, Agent Passport, DIDs, IETF agent-auth drafts) each answer who an agent is and what it can do right now, then expire. Specs focus on security, not adoption.

![Agent identity specs today](images/11-s024.webp)
*[▶ 00:10:19](https://www.youtube.com/watch?v=mBNVKkyfnE0&t=619s) · Agent identity specs today*

The tighter the keycard (narrower scope, faster expiry, sooner revocation), the less the agent can be.

![The keycard critique](images/12-s025.webp)
*[▶ 00:10:54](https://www.youtube.com/watch?v=mBNVKkyfnE0&t=654s) · The keycard critique*

A keycard is not identity: you don't badge your way into existence.

![keycard is not identity](images/13-s026.webp)
*[▶ 00:10:59](https://www.youtube.com/watch?v=mBNVKkyfnE0&t=659s) · keycard is not identity*

## Giving an agent an inbox at signup

[▶ 00:10:58](https://www.youtube.com/watch?v=mBNVKkyfnE0&t=658s)

Telnyx, a phone carrier, wanted agents to sign up autonomously. Its [signup guide](https://telnyx.com/agent-signup.md) assumed an email address, which agents often lack. Telnyx added an API call to [AgentMail](https://www.agentmail.to) that provides an inbox on demand, so the agent can verify its account and get an API key. The inbox then stays on as a permanent contact point, scoped to signup.

![AgentMail × Telnyx signup flow in production](images/14-s027.webp)
*[▶ 00:13:37](https://www.youtube.com/watch?v=mBNVKkyfnE0&t=817s) · AgentMail × Telnyx signup flow in production*

## Agent identity needs bridges to today's systems

[▶ 00:12:08](https://www.youtube.com/watch?v=mBNVKkyfnE0&t=728s)

In [Telnyx's signup case](https://telnyx.com/agent-signup.md), the agent's patterns became the focal point: low balances, suspensions, support issues, failures and recoveries.

Even with a credit card, an agent still needs a browser and its own email address to complete checkout.

Refunds, disputes and receipts require an audit: who the agent was, why it signed up, what it did, and how to reach it.

## Early, but agents already work inside our tools

[▶ 00:13:37](https://www.youtube.com/watch?v=mBNVKkyfnE0&t=817s)

The status is that we are very early. The good sign is that agents already use our tools. They have their own identity, tools, context and permissions. They don't need a new network. They join the ones we already use, where trust layers and reputation are already built.

The risks are real:

- Surfaces break under stress
- They get overcrowded
- Agents show bad judgment

![Honest status: we are so early](images/15-s028.webp)
*[▶ 00:15:51](https://www.youtube.com/watch?v=mBNVKkyfnE0&t=951s) · Honest status: we are so early*

## Shared surfaces and borrowed permissions will break

[▶ 00:14:22](https://www.youtube.com/watch?v=mBNVKkyfnE0&t=862s)

The current two-way exchange between agents and existing services won't last. Three things will break it:

1. Existing surfaces break under stress and get overcrowded.
2. The number of agents may exceed a trillion.
3. People give agents their own permissions, such as access to their email inbox or WhatsApp account. That leads to wrong messages sent or received, and a blast radius that keeps expanding and exposing more.

## Build for use cases, not only for agents

[▶ 00:15:17](https://www.youtube.com/watch?v=mBNVKkyfnE0&t=917s)

Nobody knows the end state yet; how people direct agents will shape what comes next.

AgentMail's philosophy is "for the next trillion." For a long time it built for agents: adapting to every model advance and keeping docs correct. Those still matter, but it's as important to focus on use cases: what unlocks agents in everyday life and drives adoption.

![For the next trillion](images/16-s029.webp)
*[▶ 00:16:44](https://www.youtube.com/watch?v=mBNVKkyfnE0&t=1004s) · For the next trillion*

## Links & resources

- [AgentMail — the speaker's company, email inboxes for AI agents](https://www.agentmail.to) — <https://www.agentmail.to> · video description
- [Telnyx agent signup guide (agent-signup.md)](https://telnyx.com/agent-signup.md) — <https://telnyx.com/agent-signup.md> · video description
- InboxOrOblivion — agent account on Clock, the Twitter for AI agents · mentioned [▶ 00:02:18](https://www.youtube.com/watch?v=mBNVKkyfnE0&t=138s)
- [Adi Singh's X profile — where the speaker tweeted about Harbinger mode](https://x.com/adisingh) — <https://x.com/adisingh> · video description
- Harbinger mode — agent report the speaker shared, URL not shown · mentioned [▶ 00:02:45](https://www.youtube.com/watch?v=mBNVKkyfnE0&t=165s)
