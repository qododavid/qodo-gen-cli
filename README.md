# Qodo Gen CLI

The Qodo Gen CLI lets you interact with the Qodo platform from your terminal for automation, advanced AI workflows, or CI/CD integration.

Use tools like **Qodo Gen**, **Qodo Merge** and **Qodo Aware** directly via the command line.

## Features

- Run interactive AI agents from your terminal
- Choose specific AI models on the fly
- Integrate with your own tools and schemas
- Serve agents over HTTP with `--mcp` mode

## Installation

To use Qodo CLI, you’ll need [Node.js](https://nodejs.org/en/download) and [npm](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm) installed.

Then run:

```bash
npm install -g @qodo/gen
```

## Quick Start

### 1. Authenticate

```bash
qodo login
```

This will open a browser for you to authenticate.

After logging in, you'll receive an API key to use locally or in CI. Your API key will be saved in the `.qodo` folder in your home dir, and also displayed for your reference.

### 2. Run Qodo Gen Chat - **Interactive AI Chat**

```bash
qodo chat
```

You can use all Qodo tools in natural language:

```
Use Qodo Merge to summarize my latest git changes.
```

Exit anytime with `Escape`.

### 3. Create your own agent

Create an `agent.toml`:

```bash
qodo init
```

The `agent.toml` file is used to configure your own agent. Learn more on how to do this in [Qodo’s documentation platform.](https://docs.qodo.ai/qodo-documentation/qodo-gen/cli)

For example:

```text
description = ""                   # a description of what your agent does

instructions = """ """             # a prompt for the AI models explailing the required behavior

arguments = [{}, {}...]            # a list of possible arguments that can be given to the agent

mcpServers = """ """               # list of MCP servers used by the agent

available_tools = []               # list of MCP server names

execution_strategy = "act"         # plan lets the agent think through a multi-step strategy, act executes actions immediately

output_schema = """ {} """         # valid json of the wanted agent output

exit_expression = "include_tests"  # for CI runs, a condition used to determine if the agent run succeeded or failed

```

Run your agent with:

```bash
qodo my-command
```

When you run an agent with the `--mcp` flag, it starts a local HTTP server on **port 3000**. This turns the agent into a **standalone AI tool** that can receive and respond to requests over HTTP:

```jsx
qodo my-command --mcp
```

## Commands

```bash
qodo --help          # See all commands
qodo login           # Authenticate
qodo chat            # Start interactive agent session (Qodo Gen)
qodo init            # Generate a customizable agent template
qodo <command>       # Run your configured agent
qodo list-mcp        # List all available tools
qodo models          # List supported AI models
```

## Configuration

- `mcp.json`: lists all the shared tools across agents
- `agent.toml`: defines an agent, with tools and output schemas

## CI Integration

You can copy the API key shown after login into your CI/CD secrets. The key is tied to your user and subject to the same limits.

---

For full documentation, visit the [Qodo documentation website](https://docs.qodo.ai/qodo-documentation/qodo-gen/cli)).
