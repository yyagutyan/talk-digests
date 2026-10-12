# AI in Healthcare Series: When AI Stops Being a Project

*How large healthcare enterprises are moving AI from pilots to long-running agent workflows, and how they manage cost, model choice, and leadership.*

Source: [AI in Healthcare Series: When AI Stops Being a Project](https://www.youtube.com/watch?v=ZlLV9ZHauAM) · Matt Lungren, Justin Norden and Sandeep Dadlani · Stanford Online · 37 min

## TL;DR

Large healthcare enterprises are moving from chatbots to long-running agents, but their processes, costs, and governance are not built for multi-step work. Point solutions add little unless they are tied to an end-to-end workflow and a target metric. Models are commoditizing, so durable value sits in the workflow, the harness that can swap models, and business context. Sandeep Dadlani argues that AI will soon be either a project or the business, and that the difference comes from CEO-level ownership.

## Key takeaways

- Distinguish real agents from chatbots: agency means reasoning, deciding, and acting over multi-step work, not just answering questions.
- Expect enterprise processes to lag agent capability, so long tasks often have to be split into iterative pieces that fit existing send-and-wait workflows.
- Redesign the end-to-end workflow, such as getting paid or routing a patient, instead of building siloed point solutions that never add up to a bottom-line metric.
- Define the target metric first and track token spend, outcomes, and before-and-after NPS, because teams often do not know their token efficiency.
- Build a harness that can swap models so you are not locked in, since a specialized vendor model today may be replaced by a general model tomorrow.
- Do not judge models by public benchmarks alone; teams that know their own use cases can often evaluate a new release within days.
- Put engineers and product managers directly in front of the problem, such as patient interactions and call-center shifts, and form tiger teams that own one process end to end.
- Treat AI as a CEO-level leadership agenda rather than an IT, innovation, or 'AI guy' problem, with regular cross-functional reviews of use cases and spend.

## Agents moving from answers to long, multi-step work

[▶ 00:00:00](https://www.youtube.com/watch?v=ZlLV9ZHauAM&t=0s)

Agents are now doing multi-step work, not just answering questions. OpenAI's internal data shows agents handling tasks in finance, recruiting and legal, not only engineering, some estimated at about eight human hours.

The **capability overhang** is the gap between what models can do and what organizations actually use. The speaker struggles to name an eight-hour continuous task to give an agent, though GPT-4 already had plenty of work to operationalize.

Enterprises aren't built for this. Their processes send work out and wait for results, so long tasks have to be split into iterative pieces. He admits he doesn't have a good answer.

UnitedHealth Group, with about 22,000 engineers working with LLMs, isn't ready for long-form agentic work on governance, guardrails, security or imagination. He warns against calling everything an agent: agency means reasoning, deciding and acting.

## Reimagining end-to-end workflows instead of siloed agents

[▶ 00:06:22](https://www.youtube.com/watch?v=ZlLV9ZHauAM&t=382s)

Point solutions fix one task, but the value sits in the **end-to-end workflow**, such as getting paid or routing a patient to the right place. Rethinking the whole flow for new technology is the most impactful path and the hardest.

Without a target metric, point solutions don't add up; CFOs see AI everywhere but in the bottom line. The hard questions are which metric matters and how to move it at scale. The speaker calls this a people challenge more than a technical one.

Agents also raise cost and control issues. Token efficiency is often unknown, and enterprises want to switch models without being **locked in**.

At UnitedHealth Group, the internal harness United AI Studio offers 117 models, about 20 of them internal open-weight small models, plus a token gateway. Daily use is roughly 10 to 25 billion tokens. Past a $1,000 personal token limit, the enterprise CTO told the speaker to stop experimenting and ship to production. About 92 cross-domain agents are registered so they can be monitored after deployment.

## Open Chinese models and the limits of public benchmarks

[▶ 00:13:27](https://www.youtube.com/watch?v=ZlLV9ZHauAM&t=807s)

GLM-5.2, an open-source model from a Chinese lab, is reported to reach frontier-level results on several benchmarks, especially in coding, at a much lower cost. It can also run on your own infrastructure. A separate study found that most models US companies use through OpenRouter are now Chinese open-source models.

Caveats from the speaker:

- It has not clearly matched frontier results on health benchmarks.
- It reportedly uses 5 to 10 times more tokens than the prior 4.8 version to reach the same outcome.
- At over 750 billion parameters, it is impractical to run at home.

Public benchmarks are a weak basis for decisions. Teams that know their own use cases well can often judge a new release within days.

A study comparing general-purpose LLMs with specialized clinical AI tools found the general-purpose models performed better.

## Value moves above the model layer

[▶ 00:18:34](https://www.youtube.com/watch?v=ZlLV9ZHauAM&t=1114s)

Models are commoditizing, so durable value sits above them: business context, specific use cases, the harness around the model, and possibly RL on top.

At Optum Health, which touches 20 million patients a year and employs 10,000 physicians, building a model is no longer rocket science. Training an open-weight model on proprietary data is feasible, but the team mostly refrains. It needs a harness that can swap models, because a specialized LLM vendor today may be replaced by a general model tomorrow.

The priorities follow from that:

- Focus on the experience and workflow, and embed interchangeable models into it.
- Training a frontier model on medical data takes time, compute and specialized data, while models improve weekly. Context, use-case detail and team alignment on redesigning work come first.

Benchmark debates also lag reality. Peer-reviewed papers often test models about 18 months old, because review takes that long. Teams turning models into daily value judge each use case regardless of model. People with a stake in a model's ranking react viscerally.

For perspective, the industry still processes 9 billion faxes a year, and some teams still use OCR because it is embedded in their workflow.

## Zero distance to the problem, and AI as a business

[▶ 00:24:44](https://www.youtube.com/watch?v=ZlLV9ZHauAM&t=1484s)

In large, fragmented organizations, most people are far from the end user, the patient, the receptionist at the provider clinic, or the moment of truth. Everyone abstracts, so work piles up in consensus layers, reports, and more data.

The fix is to put problem solvers, such as engineers and product managers, who can now build with Claude Code and Codex, directly in front of the problem. Have them join patient interactions and call-center shifts, and do side-by-side calls. This creates a rare kind of speed. The results are mixed, and many experiments fail, but this is where "100x faster" becomes possible.

- Form **Tiger teams** that own one process end to end, and equip them with top tooling.
- Use live prototypes to break inertia. After a competitor's app depressed a leadership meeting, someone gave Claude Code the login and company context and asked for a better experience. About 60 seconds later, an HTML file appeared that the team found even more impressive.

Governance is the other lever. Each month, the CEO, CIO, CTO, CFO, and CMO meet for six hours with about 50 people across businesses to review AI use cases, token spend, outcomes, and before-and-after NPS. In his consulting work, he advises health systems to copy this, sometimes every other week with the CFO and leadership team. Treating AI as an IT, innovation, or "AI guy" problem is the failure mode. Pace requires a CEO-level leadership agenda.

Health systems have historically not won by adopting technology early. The speaker believes AI is different. One host's read from advising clients: within two years, AI will either be a project or the business.

## Links & resources

- [Stanford AI in Healthcare series page, linked in the video description](https://stanford.io/3NEt7uE) — <https://stanford.io/3NEt7uE> · video description
