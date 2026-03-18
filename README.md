# Agentic-Tools

Welcome to my personal collection of skills and tools for AI agents. This repository contains a mix of community-driven integrations and my own custom-built modules designed to easily expand the capabilities of any autonomous agent system.

<br>

## ⚡ Essential Tools & Infrastructure

These are foundational tools I highly recommend for orchestrating agent workflows efficiently.

* **[Lobster](https://github.com/openclaw/lobster):** A workflow shell that lets OpenClaw run multi-step tool sequences as a single, deterministic operation. It's essential for saving tokens, preventing endless loops, and adding explicit human-in-the-loop approval checkpoints to your pipelines.

<br>

## 🌐 Third-Party Skills

A curated collection of third-party modules and community tools I rely on. I currently run and test all of these within [OpenClaw](https://github.com/openclaw/openclaw).

* **[Google Workspace CLI](https://github.com/googleworkspace/cli):** Allows the agent to use Drive, Gmail, Calendar, and every Workspace API. 40+ agent skills included.

<br>



## 🛠️ Custom Skills

These are modules I have created specifically for my workflows. 

* **[TikTok Slideshow Creator](https://github.com/mathiasbarea/tiktok-slideshow-creator):** A pipeline skill that allows AI agents to generate complete slideshows, from brainstorming the script idea to creating visual assets and text overlays.

<br>

## 🚀 Skills Installation

Installing these skills is straightforward. Simply provide your AI agent with the skill's GitHub URL and ask it to install it for you.

**If you are using OpenClaw, you can choose between two scopes:**
* **Global Installation:** `.openclaw/skills` (available to all agents).
* **Workspace Installation:** `<workspace>/skills` (restricted to a specific agent).