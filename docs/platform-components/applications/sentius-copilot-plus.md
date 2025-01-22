# Sentius Copilot+

Sentius Copilot+ is an Electron-based desktop application that controls web browsers (and, later, desktop apps) across Windows, macOS, and Ubuntu. Practically speaking, Sentius Copilot+ hosts a part of the **browser agent** that connects to a web browser on your computer, Google Chrome by default.

Sentius Copilot+ application can run in two modes:

* manual mode
* unattended mode

# Logging in

To log into Sentius Copilot+ application, you need to have a Google or Microsoft Account. It can be either a personal or a work-issued one.

# Running

## Manual Mode

You can interact with Sentius Copilot+ application manually by:

* asking it to perform new tasks for you
* showing it how to do tasks for you by recording and saving instructions for them in it
* asking it to perform tasks based on the saved instructions

## Unattended Mode

You can integrate your Sentius Copilot+ application to execute browser automation tasks into your existing application by obtaining your API key and using it to call [Browser Agent API](../../api/browser-agent-api.md).

# Examples

## Quarterly Presentations Lookup Demo

In this demo, you can see how you can use *zero-shot* capability of the **browser agent** running inside the **Sentius Copilot+** application to find quarterly financial results presentations for investors on the websites of three different publicly traded companies using the same prompt:

<iframe width="560" height="315" src="https://www.youtube.com/embed/hzhXlfm8ELw?si=ZUA2eGAAb5oRaDL_" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## Recorder Demo

In this demo, you can see how you can use *recorder* capability of the **browser agent** running inside the **Sentius Copilot+** application to teach it how to find vaccines needed to visit a given country on the CDC website, and then execute that saved instruction inside it:

<iframe width="560" height="315" src="https://www.youtube.com/embed/_BLZnT5yry4?si=ZUA2eGAAb5oRaDL_" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

# User Roles

Currently, Sentius Teach & Repeat Platform supports three user roles:

* end-user
* developer
* tenant administrator (coming soon)

To integrate **browser agent** (that uses your Sentius Copilot+ application instance), you need to have a **developer role**.

# Automatic Updates

Sentius Copilot+ application is automatically updated on each start.

# Availability

* Windows 10+ - x86_64 (Intel, AMD), arm64 (Qualcomm)
* macOS - x86_64 (Intel), arm64 (M1+)
* Ubuntu - x86_64 (Intel, AMD)