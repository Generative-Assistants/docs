# Browser Agent API

## Prerequisites
* Download, install, and run [Sentius Copilot+](../platform-components/applications/sentius-copilot-plus.md) application
* Get access to Sentius Teach & Repeat Platform waitlist in the *developer role* by filling out this form
* Obtain API Key from your [Sentius Copilot+](../platform-components/applications/sentius-copilot-plus.md) application (via Settings --> API)

## Dialog Sessions

To integrate **browser agent** into your application directly (without Workflow Engine), you need to:

1. Create new [Dialog Session API](dialog-sessions.md)
2. Chat with **browser agent** with a prompt (see [Dialog Session API](dialog-sessions.md))

If you want to specify an ID of a **Saved Instruction** (also called "Skills" in the application), copy it from the saved instruction properties in the "Skills" section, and then specify it, as shown in the [Dialog Session API](dialog-sessions.md).

## Tasks

To oversee the status of tasks that are or were run by **browser agent**, visit [Tasks API](skills.md).