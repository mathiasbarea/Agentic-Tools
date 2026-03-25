# OpenClaw Multi Agents Setup

In OpenClaw, an agent is an isolated “brain” with its own:

- workspace
- state directory (`agentDir`)
- auth profiles
- sessions
- optional runtime defaults

This means multi-agent setups are primarily about:

- isolation
- routing
- specialization

They are **not** automatically a delegation system by themselves.
If you want `main` to delegate to another first-level agent, that must be explicitly allowed and intentionally instructed.

## 📖 Step-by-Step Guide

This guide describes a practical, documentation-aligned way to set up additional agents in OpenClaw.

It is optimized for a common pattern:

- `main` stays as the general coordinator
- other agents are created for specialized roles
- coding agents use **ACP** runtime (more on this below)

# 🚨 IMPORTANT NOTICE

> [!WARNING]
> **ACP is currently not working as expected on DM / non-thread surfaces.**
>
> **Date:** March 20, 2026

ACP sub-agents may successfully execute their assigned tasks, but the final response does **not always return correctly to the originating chat surface**.

<br>

## 🌱 Starting Architecture

When creating additional agents in OpenClaw, follow these principles:

1. **Keep `main` as the coordinator**
- Let `main` remain the general-purpose assistant.
- Use additional agents for specialized work.

2. **Give each agent a clear role**
- Avoid creating multiple agents that “do everything.”
- Prefer roles like:
- `coding`
- `research`
- `ops`
- `content`

3. **Keep agents isolated**
- Each agent should have its own:
- workspace
- `agentDir`
- session store
- Do **not** reuse the same workspace or `agentDir` across multiple agents.

4. **Use ACP only when it actually helps**
- ACP is ideal for coding harnesses such as Codex, Claude Code, Gemini CLI, etc.

5. **Start without direct routing unless needed**
- Let `main` invoke the specialist first.
- Only add channel or thread bindings later if they are clearly useful.

<br>

## 🤖 First Specialized Agent

For most technical setups, the best first additional agent is:

- **Agent ID:** `coding`
- **Role:** programming, debugging, refactoring, codebase inspection
- **Runtime:** ACP
- **ACP harness:** Codex (This is the one I use, you can use a different one)
- **Mode:** persistent

Using `coding` as the agent ID is usually better than `codex`, because the role remains stable even if the underlying coding harness changes in the future.

<br>

## 🚀 1- Let's get started

Create the Agent with the OpenClaw Helper, the command below creates a `coding` agent:

```bash
openclaw agents add coding
```

Once you're done, verify your agent was successfuly created:

```bash
openclaw agents list
```

When asked about setting up a channel, skip it, you won't be speaking directly to the coding agent.

Make sure your `agents.list` array in your `openclaw.json` looks something like this:

```json
{
  "agents": {
    "list": [
      {
        "id": "coding",
        "name": "coding",
        "workspace": "C:\\path\\to\\.openclaw\\workspace-coding",
        "agentDir": "C:\\path\\to\\.openclaw\\agents\\coding\\agent",
        "model": "openai-codex/gpt-5.4",
        "runtime": {
          "type": "acp",
          "acp": {
            "agent": "codex",
            "backend": "acpx",
            "mode": "persistent",
            "cwd": "C:\\path\\to\\your\\repo"
          }
        }
      }
    ]
  }
}
```

### Meaning of Each Field

- `id: "coding"`  
  Unique internal identifier for the agent. This is the value OpenClaw uses for routing, bindings, spawning, and references such as `agentId`. It should remain stable once the agent is in use.
- `name: "coding"`  
  Human-readable display name for the agent. Unlike `id`, this is mainly for readability and presentation.
- `workspace: "C:\\path\\to\\.openclaw\\workspace-coding"`  
  The agent’s workspace directory. This is the agent’s “home” for workspace files such as `AGENTS.md`, `SOUL.md`, `USER.md`, notes, and other local files. In normal OpenClaw operation, the workspace is also the default working directory for tools and workspace context.
- `agentDir: "C:\\path\\to\\.openclaw\\agents\\coding\\agent"`  
  The agent’s state directory. This is separate from the workspace and is used for per-agent state such as auth profiles, model registry data, and related configuration. It should not be shared between agents.
- `model: "openai-codex/gpt-5.4"`  
  The default model OpenClaw uses for this agent. This can be a simple `provider/model` string, or a richer object with `primary` and fallback models.
- `runtime`  
  Defines the default runtime behavior for this agent. In this example, the agent is configured to use ACP by default rather than the normal OpenClaw runtime.
- `type: "acp"`  
  Tells OpenClaw that this agent should use the ACP runtime.
- `acp.agent: "codex"`  
  Selects the ACP harness target. With the `acpx` backend, built-in harness aliases include values such as `codex`, `claude`, `gemini`, and others.
- `acp.backend: "acpx"`  
  Selects the ACP backend plugin that OpenClaw will use to run the external harness.
- `acp.mode: "persistent"`  
  Configures the ACP session as persistent rather than one-shot. This is better suited for iterative coding work, follow-up instructions, and longer-lived coding sessions.
- `acp.cwd: "C:\\path\\to\\your\\repo"`  
  Sets the requested working directory for the ACP runtime itself. This is usually the repository or project folder where Codex should operate.

**CWD**
I recommend using the coding agent’s workspace as the cwd, unless the agent is intended to work exclusively within a single project.

**Mode**
ACP supports both one-shot and persistent workflows.

For a coding agent, persistent is usually the best default because it supports:

-   iterative debugging
-   follow-up fixes
-   test/fix/retest loops
-   longer coding sessions with continuity

Use persistent behavior for the dedicated coding agent unless you specifically want every task to be isolated and stateless.

<br>

## 👤 2- Define the Agent’s Role, Boundaries, and Working Style

Before configuring advanced runtime behavior, define the agent clearly.

Do not stop at a vague role label like "coding agent" or "research agent".
Specify:

- what the agent is responsible for
- what it should avoid
- how it should work
- what good output looks like

- `AGENTS.md` for operational rules and workflow discipline
- `SOUL.md` for deeper behavioral identity and long-lived guidance
- `USER.md` for optional user-specific preferences when relevant
- `IDENTITY.md` [GIVE ME TEXT FOR THIS]

You can find examples of those files for a coding agent [here](/Coding-Agent)

<br>

## 💻 3- Install ACPX and configure the ACP

Use this command in your terminal to install ACPX:

```bash
openclaw plugins install acpx
```

Use this command to enable the plugin in your config file:

```bash
openclaw config set plugins.entries.acpx.enabled true
```

Make sure `acpx` is allowed in your `plugins.allow` array within your `openclaw.json` file, it should look something like this:

```json
"plugins": {
  "allow": [
    "acpx"
  ]
}
```

To use ACP with `acpx`, first make sure `acpx` is included in the `plugins.allow` array of your `openclaw.json` file:

```json
"plugins": {
  "entries": {
    "acpx": {
      "enabled": true,
      "config": {
        "permissionMode": "approve-all",
        "nonInteractivePermissions": "fail"
      }
    }
  }
}
```

You should also have an `acpx` entry inside the `plugins` object, similar to this:

```json
"plugins": {
  "acpx": {
    "source": "path",
    "spec": "acpx",
    "sourcePath": "C:\\path\\to\\node_modules\\openclaw\\extensions\\acpx",
    "installPath": "C:\\path\\to\\node_modules\\openclaw\\extensions\\acpx",
    "installedAt": "2026-03-20T23:28:42.852Z"
  }
}
```

In addition, `acp` should be configured as a top-level object in your `openclaw.json` file:

```json
"acp": {
  "enabled": true,
  "backend": "acpx",
  "defaultAgent": "codex",
  "allowedAgents": ["codex"],
  "maxConcurrentSessions": 8
}
```

[!IMPORTANT]
allowedAgents does not refer to your OpenClaw agent name (such as coding).
It refers to the ACP target harness id, for example: "codex", "claude", "pi", "gemini", and so on.

<br>

## 🤖 4- Allow `main` to Invoke the New Agent

If you want `main` to delegate work to the new first-level agent, configure the allowlists explicitly.  
OpenClaw supports per-agent spawn restrictions, allowing you to control which agents each agent is permitted to invoke.

Below is an excerpt from an `openclaw.json` configuration where `main` is allowed to spawn the `coding` agent.

```json
{
    "id": "main",
    "subagents": {
        "allowAgents": ["coding"]
    }
}
```

You can also allow spawning any agent by using a wildcard:

```json
"allowAgents": ["*"]
```
<br>

## Teach `main` When to Use the Specialist

Configuration alone is not enough.  
Good delegation depends on instructions.

`main` should be explicitly told when to use the `coding` agent.

Take the text below and paste it into your main agent’s AGENTS.md file:

```markdown
## Specialist Delegation

### Delegating to the `coding` Agent

Use the `coding` agent for technical work, delegate to `coding` via `sessions_spawn`.

Default spawn pattern for `coding`:
- mode: "session"
- cleanup: "keep"

delegated technical work should be asynchronous, the main session should remain responsive.


Delegate to `coding` when the task involves:

- writing or analyzing code
- implementing non-trivial features
- debugging failures or regressions
- tracing bugs across multiple files
- refactoring with care
- analyzing architecture or code structure
- fixing tests or running test/fix/retest loops
- technical investigation that benefits from sustained coding context
- repository exploration that is more than a quick read

Prefer delegating to `coding` when:

- the task is large enough that technical execution should be isolated
- the work is iterative
- command execution is likely to matter
- the problem requires careful engineering judgment
- a coding harness or persistent technical context would help

Do **not** delegate to `coding` for:

- simple factual answers you can provide directly
- casual conversation

When delegating:

- give `coding` a clear technical objective
- include relevant constraints, files, or expected outcomes
- let `coding` focus on execution rather than broad user-facing coordination

After delegation:

- synthesize the result for the user in your normal voice
- keep ownership of the overall conversation
- use `coding` as a specialist, not as the default front-door assistant
```

<br>

## 📝 5- Test with a Small Controlled Task

Start by testing the agent with a small, controlled task.

### Good First Test Tasks

Ask your main agent to:

- inspect a repository and summarize its structure
- summarize failing tests
- propose a fix for a small bug
- explain a module or code path

Once the task is complete, ask OpenClaw to run `/subagents list`.
Your new agent should appear in the recent activity.

<br>

## ✅ That’s It

At this point, your setup should be ready:

- your `coding` agent exists as a separate first-level agent
- ACP is configured for that agent
- `acpx` is installed and enabled
- `main` is allowed to invoke `coding`
- `main` has explicit delegation instructions
- the setup has been validated with a small controlled task

From here, the best next step is to iterate gradually.

Start with small technical tasks, verify delegation behavior, and only add more routing or specialization once the foundation is working reliably.

As your system grows, you can repeat the same pattern for other specialist agents such as:

- `research`
- `ops`
- `content`

Keep roles clear, keep agents isolated, and let `main` remain the coordinator.
That usually leads to the cleanest and most maintainable multi-agent setup.