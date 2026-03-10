# Fully‑Automated Programming Agent Experiment


## Project Background

This started from [this Anthropic article](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents), where they built an automatic‑coding agent.

## The Long‑Running Auto‑Programming Agent

The complete development workflow is defined in CLAUDE.md:

1. **Initialize the environment** – run init.sh to install dependencies and start the dev server.
2. **Pick a task** – read task.json and choose a task with `"passes": false`.
3. **Implement the task** – follow the steps described in the task to add functionality.
4. **Test and verify** – run `npm run lint` and `npm run build` to ensure code correctness; open the app in a browser to test (requires Playwright MCP).
5. **Update progress** – log your work in progress.txt.
6. **Commit changes** – submit all modifications at once (including the task.json update).

The generation process was:

- Describe requirements to the AI and let it write architecture.md and task.json.
- Repeatedly ask the AI to complete the next task.
- When all tasks were done, ask the AI to fully test the project.

## Prerequisites

- Claude Code
- Playwright MCP (this is the only MCP the author installed for the project)

## Usage

let the AI rewrite task.json and progress.txt according to your own project needs.  
After that, the AI can generate the code.  
If the workflow breaks, have the AI modify CLAUDE.md.

### Option 1: Run via Claude Code (safest)

Start Claude Code manually and let the AI perform the next task step.

### Option 2: Use “dangerously skip permissions” mode (alternative)

Run Claude Code with `--dangerously-skip-permissions`; the AI can complete the next task without human confirmation.  
This was the author’s usual mode during the experiment.

```bash
claude -p --dangerously-skip-permissions
```

### Option 3: Use the automation script (not recommended)

Use run-automation.sh to loop the AI multiple times:

```bash
./run-automation.sh 10  # run it 10 times
```

> ⚠️ Warning: this method is the most dangerous and wastes the most resources.  
> It’s only for when you want AI working while you’re away from the computer.
