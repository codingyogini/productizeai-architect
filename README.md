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
