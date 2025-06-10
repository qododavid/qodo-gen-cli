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
qodo mcp-list        # List all available tools
qodo models          # List supported AI models
```

## Configuration

- `mcp.json`: lists all the shared tools across agents
- `agent.toml`: defines an agent, with tools and output schemas

## CI Integration

You can copy the API key shown after login into your CI/CD secrets. The key is tied to your user and subject to the same limits.

### GitHub Action

This repository includes a GitHub Action that makes it easy to integrate Qodo into your GitHub workflows.

#### Basic Usage

```yaml
- uses: qodo-ai/qodo-gen-cli@main
  with:
    prompt: "review"
```

By default, this uses the example `agent.toml` included with the action. To use your own agent configuration, specify the `agentfile` input:

```yaml
- uses: qodo-ai/qodo-gen-cli@main
  with:
    prompt: "review"
    agentfile: "./my-custom-agent.toml"  # Path relative to your repository root
```

#### All Options

```yaml
- uses: qodo-ai/qodo-gen-cli@main
  with:
    prompt: "your prompt here"              # Required: The prompt or command to run
    model: "claude-4-sonnet"               # Optional: Specify AI model
    agentfile: "path/to/agent.toml"        # Optional: Custom agent file (default: examples/agent.toml from action)
    qodo-version: "latest"                 # Optional: @qodo/gen version (default: latest)
    key-value-pairs: |                     # Optional: Additional parameters
      coverage_threshold=80
      test_framework=jest
```

#### Key-Value Pairs

You can pass additional parameters in two formats:

**JSON Format:**
```yaml
key-value-pairs: |
  {
    "coverage_score_threshold": "0.8",
    "max_issues": "10",
    "include_suggestions": "true"
  }
```

**Multiline Format:**
```yaml
key-value-pairs: |
  test_framework=jest
  coverage_threshold=90
  parallel=true
```

#### Complete Example

**Example: Automated Test Coverage Generation with Label Trigger**

This example shows how to create a test coverage bot that automatically generates tests for uncovered code when a specific label is added to a PR:

```yaml
name: Test Coverage Bot
on:
  pull_request:
    branches:
      - main
    types:
      - labeled

permissions:
  pull-requests: write
  contents: write

jobs:
  coverage:
    # Only run if:
    # 1. PR has the qodo-cover label
    # 2. PR is open (not closed or draft)
    if: |
      contains(github.event.label.name, 'qodo-cover') &&
      github.event.pull_request.state == 'open' &&
      github.event.pull_request.draft == false
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
        
      - name: Run Qodo Coverage Bot
        uses: qodo-ai/qodo-gen-cli@main
        with:
          prompt: "qodo-cover"
          # agentfile: "${{ github.workspace }}/agent.toml"
          key-value-pairs: |
            desired_coverage=90
        env:
          QODO_API_KEY: ${{ secrets.QODO_API_KEY }}
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          PR_NUMBER: ${{ github.event.pull_request.number }}
```

In this example:
- The workflow triggers when a label is added to a PR
- It only runs if the label is "qodo-cover" and the PR is open
- It uses the `qodo-cover` prompt to:
  - Analyze which changed files need test coverage
  - Generate appropriate tests for uncovered code
  - Create a follow-up PR with the new tests targeting the original PR branch
- Sets a desired coverage threshold of 90%
- Requires both `QODO_API_KEY` and `GITHUB_TOKEN` secrets
- Passes the PR number as an environment variable for the bot to reference

**Note:** 
- Make sure to set your `QODO_API_KEY` in your repository secrets.
- allow GitHub Actions to create pull requests. This setting can be found under: **Settings > Actions > General > Workflow permissions** (near the bottom of the page). 

---

For full documentation, visit the [Qodo documentation website](https://docs.qodo.ai/qodo-documentation/qodo-gen/cli)).
