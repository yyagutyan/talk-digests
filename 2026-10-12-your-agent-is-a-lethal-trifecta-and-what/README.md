# Your Agent Is a Lethal Trifecta (And What to Do About It) — Kim Maida, Keycard

*How an overprivileged on-call agent can cause production damage, and how delegated OAuth token exchange, task-scoped policy, and short-lived tokens limit what it can do.*

Source: [Your Agent Is a Lethal Trifecta (And What to Do About It) — Kim Maida, Keycard](https://www.youtube.com/watch?v=ws9XcGGPBDk) · Kim Maida · AI Engineer · 25 min

## TL;DR

On-call agents given standing API keys and admin database credentials can drop production schemas, restart servers, and scale services to 40 pods at $420 per hour. Combining private data, untrusted tickets, and external access creates what Simon Willison calls the lethal trifecta, and approval buttons alone push access control onto a consent-fatigued user. Kim Maida argues that the fix is delegated authorization: carry the user's identity through the agent and MCP server, have a security token service evaluate policy, and issue short-lived, single-call tokens. This matters because removing tools cripples the agent, while controlling access per task lets it do useful work without holding more authority than any one ticket needs.

## Key takeaways

- Standing API keys and admin connection strings give an agent far more authority than any single ticket requires, and the demo's agent dropped a production billing schema and restarted a production server with them.
- The lethal trifecta is private data, untrusted content, and external communication together, and agents tend to use every power they have to finish a job.
- Removing tools cripples the agent, so control access instead through OAuth, MCP gateways, or sandboxes, recognizing that gateways still hold standing credentials and sandboxes do not control actions outside them.
- Token exchange (RFC 8693) starts with the user logging in, then the user consents to delegate scoped permissions, so the token keeps the user as subject and the agent as the exchanging app.
- An MCP server brokers access to downstream systems, and each tool call should exchange the user's token at the security token service for a short-lived, call-scoped token that is discarded after use.
- Policy at the security token service is the ceiling on what an agent can do: it can withhold customer PII, refuse production database drops, and only grant actions the user's own role permits.
- Intent-based authorization has the agent propose a mission with a goal, allowed actions, and limits, which the user approves or denies, and each tool call carries the mission ID so the gateway can check it fits.
- Approval prompts alone do not tell the agent which actions the user is actually authorized to approve, so authorization must be bounded by the user's own role.

## An overprivileged on-call agent

[▶ 00:00:00](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=0s)

It has full access to a cloud hosting API, a customer data warehouse and a billing database.

![On-call triage agent: incident queue idle](images/01-s002.webp)
*[▶ 00:00:48](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=48s) · On-call triage agent: incident queue idle*

Each call uses an API key.

![Billing anomaly ticket: API calls with keys](images/02-s003.webp)
*[▶ 00:01:16](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=76s) · Billing anomaly ticket: API calls with keys*

Escalating billing, it posted raw customer rows with emails and card digits to the pager.

![Escalated billing ticket exposes customer PII](images/03-s004.webp)
*[▶ 00:01:34](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=94s) · Escalated billing ticket exposes customer PII*

A power fault needed no other service, so it escalated.

![Backup power ticket escalated without extra services](images/04-s005.webp)
*[▶ 00:01:47](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=107s) · Backup power ticket escalated without extra services*

A TLS renewal went through the cloud API with a key.

![Agent console: TLS certificate auto-renewed](images/05-s006.webp)
*[▶ 00:02:30](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=150s) · Agent console: TLS certificate auto-renewed*

## Unchecked agents take destructive production actions

[▶ 00:02:32](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=152s)

An agent with an admin connection string dropped a corrupt production billing schema to trigger a restore.

```sql
DROP SCHEMA prod_billing
postgresql://admin:pw***@db-prod:5432/billing
```

![Agent console: billing database deleted](images/06-s007.webp)
*[▶ 00:02:44](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=164s) · Agent console: billing database deleted*

It assumed the morning team would confirm the restore.

![Agent console: billing dropped, morning review escalation](images/07-s008.webp)
*[▶ 00:03:21](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=201s) · Agent console: billing dropped, morning review escalation*

It restarted the main server via a cloud API using a static key, taking production offline.

```text
GET https://incidex.pager.io/api/incidents
DROP SCHEMA prod_billing
POST https://incidex.pager.io/api/incidents/INC-2291/escalate
GET https://incidex.pager.io/api/incidents
POST https://compushost.cloud/api/v2/services/main/restart
POST https://incidex.pager.io/api/incidents/notes
GET https://incidex.pager.io/api/incidents
```

![Agent console: site scaled to 40 pods](images/08-s009.webp)
*[▶ 00:03:56](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=236s) · Agent console: site scaled to 40 pods*

It scaled one service from 4 to 40 pods at $420 per hour, then left a note.

![Agent console: scaled to 40 pods, $420/hr](images/09-s010.webp)
*[▶ 00:04:12](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=252s) · Agent console: scaled to 40 pods, $420/hr*

## The same capabilities make an agent a lethal trifecta

[▶ 00:04:16](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=256s)

Useful agents need your data, research, and external tools.

![What makes an agent useful](images/10-s012.webp)
*[▶ 00:04:59](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=299s) · What makes an agent useful*

Together they form Simon Willison's [lethal trifecta](https://simonwillison.net/2025/Jun/16/the-lethal-trifecta): private data, untrusted content, external communication.

![What makes an agent a lethal trifecta](images/11-s013.webp)
*[▶ 00:05:36](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=336s) · What makes an agent a lethal trifecta*

Agents use every power they have to finish the job.

![Agents are designed to get the job done](images/12-s014.webp)
*[▶ 00:05:46](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=346s) · Agents are designed to get the job done*

Deletions mostly occur in full access mode without sandboxing or auto review.

![Humans give agents too much access](images/13-s015.webp)
*[▶ 00:05:53](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=353s) · Humans give agents too much access*

Overprivileged agents act freely, even under supervision.

![Overprivileged agents are dangerous](images/14-s016.webp)
*[▶ 00:06:02](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=362s) · Overprivileged agents are dangerous*

Approval prompts push access control onto a consent-fatigued user.

![Humans give agents too much access](images/15-s017.webp)
*[▶ 00:06:18](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=378s) · Humans give agents too much access*

## Controlling agent access instead of removing tools

[▶ 00:06:21](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=381s)

Removing capabilities cripples the agent; control access through OAuth, MCP gateways or sandboxes instead.

![Control agent access](images/16-s019.webp)
*[▶ 00:06:53](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=413s) · Control agent access*

![Agent access control options](images/17-s020.webp)
*[▶ 00:07:07](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=427s) · Agent access control options*

[OAuth](https://datatracker.ietf.org/doc/html/rfc6749) binds access to a grant consented before any task exists.

![OAuth binds delegated access to a grant](images/18-s021.webp)
*[▶ 00:07:46](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=466s) · OAuth binds delegated access to a grant*

Gateways enforce per request yet hold standing credentials.

![Gateways bind enforcement to a route](images/19-s022.webp)
*[▶ 00:08:07](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=487s) · Gateways bind enforcement to a route*

Sandboxes limit blast radius but don't control what can act outside them.

![Sandboxes bind execution to a process](images/20-s023.webp)
*[▶ 00:08:31](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=511s) · Sandboxes bind execution to a process*

Even combining all three, the agent gets no runtime credential scoped to one task.

![How can we bind access to a task?](images/21-s024.webp)
*[▶ 00:08:41](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=521s) · How can we bind access to a task?*

## Establishing the user's identity before delegation

[▶ 00:08:43](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=523s)

Token exchange, a 2020 standard extending OAuth 2.0 ([RFC 8693](https://www.rfc-editor.org/info/rfc8693/)), starts with identity: the user logs in to an authorization server, which verifies identities and issues tokens.

![User authenticates with authorization server](images/22-s027.webp)
*[▶ 00:09:42](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=582s) · User authenticates with authorization server*

The user then consents to delegate scoped permissions, the first narrowing.

![User grants delegated access](images/23-s028.webp)
*[▶ 00:10:03](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=603s) · User grants delegated access*

The token identifies the user and their access level.

![Authorization server issues user token](images/24-s029.webp)
*[▶ 00:10:17](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=617s) · Authorization server issues user token*

The user anchors the identity chain.

![Human user](images/25-s031.webp)
*[▶ 00:10:28](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=628s) · Human user*

## Following the agent's path through MCP

[▶ 00:10:25](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=625s)

An agent is a control loop that calls an LLM.

![An agent is a control loop that calls an LLM](images/26-s032.webp)
*[▶ 00:10:43](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=643s) · An agent is a control loop that calls an LLM*

An MCP server sits between the agent and its resources, providing tools.

![MCP server provides tools and resources](images/27-s033.webp)
*[▶ 00:10:52](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=652s) · MCP server provides tools and resources*

The MCP client dispatches proposed tool calls to that server.

![MCP client dispatches agent tool calls to MCP server](images/28-s034.webp)
*[▶ 00:11:01](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=661s) · MCP client dispatches agent tool calls to MCP server*

The server also brokers access, replacing static API keys.

![MCP server brokers tools and access](images/29-s035.webp)
*[▶ 00:11:21](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=681s) · MCP server brokers tools and access*

A runtime, such as Claude Code or an Agent SDK app, runs the loop.

![The agentic execution path](images/30-s036.webp)
*[▶ 00:12:03](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=723s) · The agentic execution path*

The subject token identifies the user.

![Subject token identifies the user](images/31-s037.webp)
*[▶ 00:12:25](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=745s) · Subject token identifies the user*

## Subject tokens and the security token service

[▶ 00:12:18](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=738s)

A **subject token** names who the agent acts for. Custom MCP servers can exchange tokens for downstream resources.

![Custom MCP server requests token exchanges](images/32-s038.webp)
*[▶ 00:12:52](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=772s) · Custom MCP server requests token exchanges*

The model proposes a tool call; the runtime authenticates to the STS with its workload identity.

![Agent loop runs and model proposes tool call](images/33-s039.webp)
*[▶ 00:13:02](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=782s) · Agent loop runs and model proposes tool call*

The STS checks agent identity, user identity, and access level, then issues a single-call token.

![Agent identity, user identity, level of access](images/34-s040.webp)
*[▶ 00:13:43](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=823s) · Agent identity, user identity, level of access*

Policies are ceilings; agents can still do what you didn't intend.

![What you want an agent to do vs. what it…](images/35-s041.webp)
*[▶ 00:14:09](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=849s) · What you want an agent to do vs. what it…*

## Checking the mission before the agent acts

[▶ 00:14:11](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=851s)

Intent-based authorization (IBA) checks intent before action; a gateway enforces it.

![Gateway – Policy Enforcement Point (PEP)](images/36-s043.webp)
*[▶ 00:14:31](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=871s) · Gateway – Policy Enforcement Point (PEP)*

The agent proposes a mission: goal, allowed actions, limits.

![Agent proposes a mission declaring intent](images/37-s044.webp)
*[▶ 00:14:56](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=896s) · Agent proposes a mission declaring intent*

The user approves, denies or revises it; low-risk missions may be auto-approved.

![Mission must be approved, denied, or revised](images/38-s045.webp)
*[▶ 00:15:22](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=922s) · Mission must be approved, denied, or revised*

Each tool call carries the mission ID; the gateway asks the STS if it fits.

![Gateway requests policy assessment from STS](images/39-s046.webp)
*[▶ 00:15:40](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=940s) · Gateway requests policy assessment from STS*

If it fits, the STS issues a task-scoped token for that call.

![STS issues task-scoped access token for MCP server](images/40-s047.webp)
*[▶ 00:16:21](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=981s) · STS issues task-scoped access token for MCP server*

## Ephemeral tokens and delegation chains

[▶ 00:16:05](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=965s)

The STS gives the MCP server a token that keeps the user as subject and adds the on-call agent as the exchanging app. It expires within minutes and is never stored.

For external resources, the MCP server exchanges that token at the STS for a call-scoped token. The STS checks policy, and the user subject keeps the chain traceable.

![STS issues token for downstream resource](images/41-s048.webp)
*[▶ 00:17:02](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=1022s) · STS issues token for downstream resource*

The token is discarded after each call. The response flows back up, and the agentic loop repeats until the model answers.

![Response flows back up and loop repeats](images/42-s049.webp)
*[▶ 00:17:29](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=1049s) · Response flows back up and loop repeats*

## Task-based policy in the repeated demo

[▶ 00:17:37](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=1057s)

The demo now uses a security token service and task-based policy. An MCP server brokers access to four backends.

![Demo architecture: agent, MCP server, backends](images/43-s051.webp)
*[▶ 00:17:56](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=1076s) · Demo architecture: agent, MCP server, backends*

The operator signs in to the triage app first.

![On-call triage agent operator sign-in](images/44-s053.webp)
*[▶ 00:18:18](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=1098s) · On-call triage agent operator sign-in*

Each backend then asks for login.

![Incidex login page](images/45-s054.webp)
*[▶ 00:18:35](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=1115s) · Incidex login page*

Incidex consent covers escalating incidents, notes and queue reads.

![Incidex app authorization consent screen](images/46-s055.webp)
*[▶ 00:18:50](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=1130s) · Incidex app authorization consent screen*

CompuHost consent covers scaling, restarts and TLS renewal.

![Compuhost Cloud consent screen](images/47-s059.webp)
*[▶ 00:19:23](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=1163s) · Compuhost Cloud consent screen*

Each request now passes policy evaluation before a token is granted.

## Policy decides which data and actions an agent gets

[▶ 00:20:01](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=1201s)

Policy withholds customer PII from agents, so the billing agent saw only aggregate totals.

![Triage agent UI: INC-2288 denied by policy](images/48-s061.webp)
*[▶ 00:20:27](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=1227s) · Triage agent UI: INC-2288 denied by policy*

It escalated 5 overcharged customers ($347.75) for the morning team to refund.

![Agent escalates INC-2288 after policy denial](images/49-s062.webp)
*[▶ 00:20:41](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=1241s) · Agent escalates INC-2288 after policy denial*

![INC-2288 summary posted for morning team](images/50-s063.webp)
*[▶ 00:20:47](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=1247s) · INC-2288 summary posted for morning team*

Renewing a certificate is within the agent's permissions, so STS issues the tokens and the agent renews it.

![INC-2290 expiring certificate: agent renews it](images/51-s064.webp)
*[▶ 00:21:09](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=1269s) · INC-2290 expiring certificate: agent renews it*

![INC-2290 certificate renewed and resolved](images/52-s065.webp)
*[▶ 00:21:36](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=1296s) · INC-2290 certificate renewed and resolved*

Policy forbids dropping a production database, so STS refuses the token and a human does it manually.

![On-call triage agent dashboard with audit log](images/53-s066.webp)
*[▶ 00:22:08](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=1328s) · On-call triage agent dashboard with audit log*

## Approval limited by the user's own authority

[▶ 00:22:28](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=1348s)

Policy blocks the restart: the user's own role lacks permission, and the agent acts only for the user.

![Orders stuck: agent reviews restart ticket](images/54-s068.webp)
*[▶ 00:22:41](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=1361s) · Orders stuck: agent reviews restart ticket*

The agent requests a grant before scaling.

![Site failing: agent reads alert, prepares scale-up](images/55-s069.webp)
*[▶ 00:23:28](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=1408s) · Site failing: agent reads alert, prepares scale-up*

The user's role permits scaling, so 36 servers are added.

![Scale-up approved and executed](images/56-s070.webp)
*[▶ 00:23:50](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=1430s) · Scale-up approved and executed*

One MCP server mediates task-scoped access to downstream systems.

![Control agent access: agent to resources](images/57-s072.webp)
*[▶ 00:24:24](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=1464s) · Control agent access: agent to resources*

Token exchange is an open standard anyone can implement, interoperating with gateways and custom agents.

![Open standards are for everyone](images/58-s073.webp)
*[▶ 00:24:36](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=1476s) · Open standards are for everyone*

![Token exchange is versatile & future-friendly](images/59-s074.webp)
*[▶ 00:24:54](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=1494s) · Token exchange is versatile & future-friendly*

## Links & resources

- [Simon Willison's post defining the lethal trifecta for agents](https://simonwillison.net/2025/Jun/16/the-lethal-trifecta) — <https://simonwillison.net/2025/Jun/16/the-lethal-trifecta> · on screen [▶ 00:05:36](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=336s)
- [RFC 6749 — OAuth 2.0 authorization framework](https://datatracker.ietf.org/doc/html/rfc6749) — <https://datatracker.ietf.org/doc/html/rfc6749> · on screen [▶ 00:07:46](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=466s)
- [RFC 8693 — OAuth 2.0 token exchange for delegated agent access](https://www.rfc-editor.org/info/rfc8693/) — <https://www.rfc-editor.org/info/rfc8693/> · on screen [▶ 00:13:43](https://www.youtube.com/watch?v=ws9XcGGPBDk&t=823s)
- [Kim Maida's GitHub profile — speaker's code and projects](https://github.com/kmaida) — <https://github.com/kmaida> · video description
