# Tips for building AI agents

*Learn how workflows differ from agents, how to write prompts and tool descriptions an agent can use, and where agents fit today.*

Source: [Tips for building AI agents](https://www.youtube.com/watch?v=LP5OCa20Zpg) · Barry Zhang, Erik, and Alex Albert · Anthropic · 18 min

## TL;DR

Teams building with LLMs often describe the same architecture under different names, which makes it hard to tell what to build. The talk separates fixed workflows, where code orchestrates each LLM call, from agents, where the model decides how many steps to run and loops toward a goal. Agents work best on valuable, complex tasks where errors are cheap to catch, such as search and coding. Knowing that split helps developers choose simpler designs and avoid wasted effort.

## Key takeaways

- A workflow is a fixed chain of LLM calls orchestrated by code, while an agent lets the LLM choose its own steps and loop until it reaches a resolution.
- Agent prompts are open-ended: they give the model tools such as web search, file edits, or code execution, plus a goal to work toward.
- Supply the context the model lacks by making it explicit in the prompt, the tool descriptions, and the environment, since tool descriptions share the context window with the prompt.
- Don't pair an elaborate prompt with bare tools that lack documentation or have parameters named `a` and `b`.
- Agents suit tasks that are valuable and complex, where the cost of an error or of monitoring for errors is low; search and coding are canonical examples.
- For agentic search, retrieve more documents than needed and then filter them down to trade precision for recall.
- Coding agents improve when tests give a pass or fail signal on each loop, and without good unit tests the iterations add noise instead of signal.
- Measure results before and after you add an agent, because unmeasured builds can hide failure or a simpler solution that works just as well.

## Workflows versus agents, and writing prompts the model can use

[▶ 00:00:00](https://www.youtube.com/watch?v=LP5OCa20Zpg&t=0s)

Anthropic's [Building Effective Agents](https://www.anthropic.com/research/building-effective-agents) separates two patterns. A **workflow** is a fixed chain of LLM calls orchestrated by code, where each prompt turns one input into one output, such as sorting a user question into one of five categories. An **agent** lets the LLM decide how many steps to run, looping until it reaches a resolution.

A workflow prompt is a straight line, with optional code that checks intermediate results. An agent prompt is open-ended: it gives the model tools, such as web search, file edits, or code execution, plus a goal to keep working toward.

Barry found agent trajectories on the OSWorld computer-use benchmark counterintuitive until he and a colleague acted as Claude. They closed their eyes, glanced at the screen for a second, and asked what Python code they would write. Agent design means supplying the context the model lacks and making it explicit in the prompt, tool descriptions, and environment.

Erik's frequent mistake is pairing elaborate prompts with bare tools: no documentation, parameters named `a` and `b`. Tool descriptions land in the same context window as the prompt, so they need prompt engineering too.

## Where agents fit, and why verification limits coding agents

[▶ 00:07:29](https://www.youtube.com/watch?v=LP5OCa20Zpg&t=449s)

Customer meetings kept describing the same architecture under different names, so the authors wanted shared definitions, diagrams and code. Models can now handle many of the agentic workflows seen in practice.

The overhyped part is applying agents to problems where much simpler systems work. As one speaker put it, don't go after a fly with a bazooka.

The underhyped part is small time savings. A task that takes a minute is not worth automating on its own, but automating it lets you run it a hundred times more often.

The sweet spot is a task that is **valuable and complex**, where the cost of an error, or of monitoring for errors, is low. Search and coding are canonical examples. For agentic search, you can trade precision for recall: retrieve more documents than needed, then filter them down.

Coding agents are partly **verifiable**. Tests return a pass or fail signal on every loop, so the model gets feedback it can use to converge. Without feedback, each iteration adds noise rather than signal. This works only with good unit tests, which most teams lack. SWE-bench results rose from very low to over 50% within a year. The next limit, in the speaker's view, is verification: adding tests for what matters in real-world code so the model can check its own work before returning to a human.

## Where agents are headed, and how to start building them

[▶ 00:12:47](https://www.youtube.com/watch?v=LP5OCa20Zpg&t=767s)

Barry built an environment where Claudes spin up other Claudes to play Werewolf, a text-based social deduction game. The open questions are emergent behavior, and whether multi-agent setups beat one agent with more resources. Neither speaker has seen multi-agent systems work in production.

Erik expects 2025 to bring business adoption, since cheap agents make tasks like updating documentation on every pull request practical.

Consumer agents are overhyped. Specifying vacation preferences takes nearly as much work as booking the trip yourself, verification is costly, and errors are risky. Models first need context built up over time.

Advice for developers:

- Measure results. Unmeasured builds hide failure, or a simpler solution that works just as well.
- Start as simple as possible.
- Some startups do everything in one LLM call; their orchestration code persists as models improve.
- If better models would erase your moat, you are building the wrong thing. Build so your product improves as models do.

## Links & resources

- [Building Effective Agents — Anthropic blog post the talk covers](https://www.anthropic.com/research/building-effective-agents) — <https://www.anthropic.com/research/building-effective-agents> · video description
