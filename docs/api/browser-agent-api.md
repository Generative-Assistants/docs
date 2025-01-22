# Browser Agent API

## Prerequisites
1. Download, install, and run [Sentius Copilot+](../platform-components/applications/sentius-copilot-plus.md) application. The application interacts with the Chromium-based browser and must be installed on the machine which will execute the tasks.
2. Get access to Sentius Teach & Repeat Platform waitlist in the *developer role* by clicking Join Waitlist when running the Sentius Copilot+ Application. 
3. Obtain API Key from your [Sentius Copilot+](../platform-components/applications/sentius-copilot-plus.md) application (go to Settings, click API)

## Dialog Sessions

To integrate Sentius Copilot+ into your application directly (without Workflow Engine), you need to:

1. Create new [Dialog Session](dialog-sessions.md)
2. If you have particular tasks to solve, you may teach Copilot how to accomplish the task by recording a [Skill](skills.md). 
3. Send tasks to perform by sending messages to the created dialog session (see [Dialog Sessions](dialog-sessions.md)).
You may specify a particular Skill (saved instruction) to make sure the task is going to be solved in a particular way.

## Tasks

To oversee the status of tasks that are or were run by Sentius Copilot+, visit [Tasks API](skills.md).