# Agents

## Overview of an Agent
AI agent, by definition, is a software component that is connected to one or more tools, and capable of figuring out how to solve a particular task on its own with minimal to no human intervention - **autonomously**. 

## Browser Agent

[Browser Agent](agents/browser-agent.md) is our first and most advanced autonomous AI agent. It is designed to intelligently navigate websites to autonomously complete tasks like interacting with them or extracting data from them.

> For example, you can ask Sentius browser agent to find a quarterly financial results presentation of a public company:

<iframe width="560" height="315" src="https://www.youtube.com/embed/hzhXlfm8ELw?si=ZUA2eGAAb5oRaDL_" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Learn how to automate web browser tasks using **Browser Agent** in the [Browser Agent API](../api/browser-agent-api.md).

## OpenAPI Agent

[OpenAPI agent](agents/openapi-agent.md) is our second agent. It is designed to interact with a given web service's REST APIs as long as those APIs are described based on OpenAPI standard.

> For example, you can configure Sentius OpenAPI agent to ask a e-commerces shipping service like [FreightOS](https://ship.freightos.com/api/shippingCalculator) to calculate shipping estimates for moving goods from China to US.

Learn how to incorporate calls to OpenAPI-based REST API web services in the [Workflow Engine documentation](services/workflow-engine.md). 

## Prompt Agent

Our third AI agent is [Prompt Agent](agents/prompt-agent.md). It interacts with an LLM to perform tasks that do not require use of any tools, like providing a structured response to a question based on data obtained by other AI agents, or generating summaries etc.

> For example, you can ask Sentius prompt agent to look at the data collected by the **browser agent** and transform it into a form that is native for a given OpenAPI to be called by **OpenAPI agent**.

Learn how to incorporate calls to Prompt Agent in the [Workflow Engine documentation](services/workflow-engine.md).

# Future Releases

There are more AI agents currently in development, like **document agent** capable of finding answers to a given question within a provided document or set of documents, **desktop agent** designed to control desktop applications across Windows and macOS, with even more coming in the future.
