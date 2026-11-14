# ProductizeAI-Architect

A Claude skill that turns a product description, PRD or workflow into a practical AI product redesign, with explicit decisions about what stays deterministic, where AI adds value, how much autonomy each action gets, and how to validate the result.

Created by [Irene Pylypenko](https://irenepylypenko.com/productizeAI). Also available as a ChatGPT plugin.

## What it does

Give it a workflow, product idea or PRD and say "Productize this." By default it returns a quick pass: one workflow, up to three changes. Ask for the full blueprint and you get:

- the before/after experience
- a mechanism and autonomy map (rules, predictive ML, generative AI or an agent, per step)
- the most consequential failure and its recovery path
- an evaluation plan linked to user outcomes, business KPIs and guardrails
- a cost model, with formulas where inputs are missing
- the first bounded experiment, and the question that could reverse the recommendation

It compares every AI step with the simplest non-AI alternative, checks whether a process fix removes the problem before adding AI, and won't design more autonomy than the evidence supports. It doesn't invent ROI, adoption numbers or validated thresholds.

## Design decisions

Each of these had a cheaper or more obvious alternative. Here is what I chose, what I turned down, and the failure each choice is there to prevent.

- **Every AI step is compared with a non-AI baseline.** Rejected: judging the AI redesign only against the current experience, which flatters AI because the current experience is usually the weakest option on the table. Prevents: recommending a model for a job that a rule, a form field or a scheduled reminder would do more cheaply and more reliably.
- **A process fix is checked before any AI is added.** Rejected: redesigning the workflow as given. Prevents: automating an approval or handoff that should have been deleted, which adds inference and review cost to a step with no value and makes it harder to remove later.
- **Autonomy is set per action, not per product.** Rejected: labelling the whole product a copilot or an agent. Prevents: a reversible draft and an irreversible external action (sending, paying, deleting) inheriting the same permission. Whether the system can do something and whether it is allowed to are kept as separate questions.
- **Requests for more autonomy than the evidence supports are not designed as asked.** Rejected: building what the user asked for, and also refusing outright. The skill designs the highest level the evidence supports and names the evidence that would justify the next one. Prevents: a blueprint that hands an accountable person's decision to an agent whose error rate nobody has measured.
- **No invented numbers.** Rejected: filling gaps with plausible ROI, adoption rates or pass thresholds so the output looks finished. Missing inputs become formulas and thresholds are labelled as proposed. Prevents: a made-up figure being copied into a business case and treated as validated, and a complete-looking document being mistaken for production readiness.
- **The skill is instructions only, with no server.** Rejected: a hosted service with connectors that read product documents directly. Prevents: confidential PRDs and roadmaps passing through a third party the user never vetted. It also lets the same method run in both Claude and ChatGPT.

## Example

[A full blueprint on a fictional wholesale bakery](examples/wholesale-bakery-order-changes.md) shows the method end to end, including where it recommends a process fix instead of AI and declines the agent the owner asked for.

## Install

**Claude Code.** Add the marketplace and install the plugin:

```
/plugin marketplace add codingyogini/productizeai-architect
/plugin install productizeai-architect@productizeai
```

**Claude (web, desktop, mobile).** Download [`productizeai-architect.zip`](https://irenepylypenko.com/productizeAI/productizeai-architect.zip), or zip the `plugins/productizeai-architect/skills/productizeai-architect/` folder from this repo. In Claude, go to Customize > Skills, click +, choose Create skill, then Upload a skill. "Code execution and file creation" must be on in Settings > Capabilities.

## Try it

```
Productize our onboarding workflow.

Redesign this SaaS workflow with AI and show what should stay deterministic.

Should this feature be a copilot, an agent or ordinary automation?
```

## Privacy

The skill is a set of instructions. It has no server, connectors, analytics or accounts, and sends data nowhere. Your conversations are processed by the AI provider you run it in, under that provider's policies. Full policy: https://irenepylypenko.com/productizeAI#privacy

## Support

https://irenepylypenko.com/productizeAI#support

## License

© 2026 Irene Pylypenko. All rights reserved. See [LICENSE](LICENSE) and https://irenepylypenko.com/productizeAI#terms
