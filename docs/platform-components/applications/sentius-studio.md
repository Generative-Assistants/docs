# Sentius Studio

Sentius Studio is a native desktop application that allows creation, editing, testing, deployment, and monitoring (coming later in 2025) of complex workflows. Currently supports Windows 10+, with macOS support coming later in 2025.

# Logging In

To connect Sentius Studio to Sentius Teach & Repeat Platform, you currently need to: 
* have a **developer role**, 
* download [Sentius Copilot+](sentius-copilot-plus.md), 
* obtain API key in its settings,
* and then set it to the **Browser Agent API key** field in the Connected Services window that you can open by going to Settings --> Options --> Configure (Connected Services).

# Key Concepts

To start working with Sentius Studio, you should learn its key concepts:

## High-Level Concepts
* Projects - all work in Sentius Studio is organized by projects; for example, you may have a "Test Project" for experiments, "Production" for your production workflows, and more.
* Entities - all information Sentius Studio is organized into universal entities called **Entities**. You can create any kind of entity inside Sentius Studio. 
* Kinds - each entity has a type called **kind**. This type is a friendly way to describe the meaning of a particular entity. There is a hierarchy of **Kinds** built into Sentius Studio. For example, **Project** is an **Entity** of **Kind** **Project**.  
* Properties - each instance of an **entity** has properties; each **entity** has **Title** and **Description**. Depending on the type of the given **entity**, properties can vary. For example, a custom **entity** of **kind** Rocket can have a custom property called *number of engines*.
* Relationships - each instance of an **entity** can be connected to an instance of another **entity** using **relationship**. Like with properties, depending on the types of two given **entities**, available relationships between can vary. For example, a **Process** entity can have connection to **browser agent instruction** entity called *has substep*.
* Actions - each instance of an **entity** can be interacted with using **actions**. Like with properties and relationships, depending on the type of a given **entity**, available actions can vary. For example, you can **execute** a **browser agent instruction**, or **open** a **website** **entity** in your web browser.

## Projects & Views
Entities in Sentius Studio can be organized together using **Projects** and **Views**.

While **Projects** can be seen as an equivalent to the zoomable space provided by popular whiteboarding applications like Microsoft Whiteboard or Miro, **Views** are more specific **entities** that can be used to organize information in more specific ways:

* **Process** is a workflow representation of **Agent Instructions**; you can use it to create, edit, test, deploy, and monitor (coming later in 2025) multi-agent workflows
* **List** is, well, a list of entities; you can use **Lists** to create lists of your objects
* **Graph** is a graph representation of a number of entities interconnected with each other; you can use **Graphs** to explore connections between entities and save those explorations for later view

## Agents, Saved Instructions, and Workflows
* **Agents** - each supported **Agent** from [Sentius Agents](../agents.md) is represented by its own **Kind** in Sentius Studio:
  * [Browser Agent](../agents/browser-agent.md)
  * [OpenAPI Agent](../agents/openapi-agent.md)
  * [Prompt Agent](../agents/prompt-agent.md)
* **Workflows** - when you want to combine one or more **agents** into a multi-step **agentic workflow**, you can do that by generating a workflow using Sentius Process Designer's Copilot (coming later in 2025), by editing it, or by creating one manually from scratch.
* **Saved Instructions** - while the work inside the worklfow is performed by **agents**, you need to provide instructions to the [Workflow Engine](../services/workflow-engine.md) that explain it how that work should be done by the each given **agent**.

# Examples

## Concerts + Venue + Directions Demo Video

A simple example consisting of three **browser agent** steps is shown below:

<iframe width="560" height="315" src="https://www.youtube.com/embed/_EQNRE-0M5g?si=ZUA2eGAAb5oRaDL_" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## End-to-End Shipping Demo Video

A complex example involving different steps ran by different agents: **browser agent**, **OpenAPI agent**, and **prompt agent**, is shown below:

<iframe width="560" height="315" src="https://www.youtube.com/embed/zwLQnmHttGk?si=ZUA2eGAAb5oRaDL_" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

# Automatic Updates

Sentius Studio application is automatically updated on each start.

# Availability

* Windows 10+ (1904+) - x86_64, arm64 (via emulation)

# Future Releases

* macOS (coming later)