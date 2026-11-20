---
name: herdr-agent-team
description: Create and coordinate a one-level team of up to five child agents with Herdr. Use when the user asks for a Herdr team or Herdr-managed parallel agents. Requires HERDR_ENV=1.
---

# Herdr Agent Team

The current agent is the leader. Each new agent is a direct child of the leader.

Unless the user says otherwise, use the leader's runtime and active provider/model. Use the runtime's documented mode that lets the child run the tools needed for its task without per-action approval. This broad access is the user's default policy. It does not allow work outside the assigned task or the user's constraints. It does not prove that a mode bypasses operating-system or host limits. Mode names such as `auto`, `autopilot`, `yolo`, and `allow-all` do not guarantee the same behavior.

## Runtime and provider/model

Herdr's `--kind` option selects the **runtime**. The runtime is the CLI that runs the agent, such as `pi` or `copilot`.

The **provider/model** selects the inference provider and model ID inside that runtime.

If the user names a provider or model but does not request another CLI, keep the leader's runtime. Change only the provider/model. For example, “GitHub Copilot's Claude Haiku 5.5” in a Pi session means Pi with provider `github-copilot` and model `claude-haiku-5.5`. It does not mean the Copilot CLI.

Switch runtimes only when the user requests a different CLI. If a bare name such as “Copilot” or “Claude” could mean either a runtime or a provider/model, ask one short question before launch.

Check the model catalog when the user requests a provider/model override or when the active model is unknown. Do not guess model IDs. In Pi, run `pi --list-models <query>`. Select a model with `--provider <provider> --model <id>` or `--model <provider>/<id>`. Pass these flags after Herdr's `--` separator. For example:

```bash
herdr agent start <name> --kind pi --pane <id> -- --model github-copilot/claude-haiku-5.5
```

If the requested model is not available, tell the user. Ask before selecting another model.

## Preconditions

1. Check that this agent runs in a Herdr pane:

   ```bash
   test "${HERDR_ENV:-}" = 1
   ```

   If this check fails, do not control Herdr. Tell the user and ask them to continue from a Herdr pane.

2. Read and follow the `herdr` skill before using Herdr. Inspect the live pane layout. Use pane IDs returned by Herdr. Do not infer IDs or target the focused pane by default.
3. Identify the runtime from Herdr. Identify the active provider/model from the runtime's live status display, such as the Pi footer. Environment variables and process arguments can be stale after a model switch. If you cannot verify the active settings, ask instead of guessing.
4. Use a verified unattended permission mode for the runtime. Check that runtime's help or documentation when the runtime or its configuration is new or has changed. Reuse a verified mode when the runtime and configuration have not changed. Do not guess flags or claim that a mode bypasses host or operating-system limits. If the requested access is not available, explain the limit and ask. Do not switch runtimes or silently reduce access.
5. Give every child a unique name that follows Herdr's naming rules. Confirm that each target is an available shell pane. Never replace a running process or agent.

## Team size and roles

The limit is **five child agents**, in addition to the leader.

- If the user requests 1–5 children, use that number. Give each child a distinct task.
- If the user requests more than five, explain the limit. Ask whether to use up to five. Do not reduce the count without asking.
- If the user gives no count, use the smallest team that can cover independent tasks. Do not add agents to fill the limit.
- If the work has no useful parallel tasks, tell the user. Work as the leader unless the user requires a team.
- Choose roles that fit the work. Examples include implementer, tester, reviewer, and researcher. Assign work by separate outcomes, not by file count.
- Give each child its task, context, expected output, constraints, and dependencies.

## Pane layout

Keep the leader's pane where it is. All children report directly to the leader. Pane nesting is visual layout; it does not create agent hierarchy. Children must not run `herdr agent start`, `herdr pane split`, or create subagents or panes.

- Keep the leader pane immediately left of one child column. Other existing panes may be elsewhere.
- Create the first child by splitting the leader pane to the right. Never create a child below or to the left of the leader.
- Place all other children in the same right-side column, below the first child. Inspect `herdr pane layout` before each split. Choose a pane in the child column that keeps the stack balanced.
- After creating panes, inspect the layout again. Confirm that the leader is left of one vertical child column. No child may appear in a further-right column.
- Create the team only if each agent pane can be at least 40 columns wide and 12 rows high. If the layout cannot meet this minimum, stop and ask before changing direction, tabs, or workspaces. Do not silently lower the minimum or change the topology.
- Keep each child in the current working directory unless the user requests another directory. Do not create a workspace, tab, or worktree unless the user requests it.
- Keep focus in the leader pane with `--no-focus` unless the user requests otherwise.

Use the pane ID returned by each split to start the child. If a split, start, or wait operation fails or times out, inspect Herdr's live state before retrying. The operation may have succeeded.

If a child launch has definitely failed, close only the pane or agent created for that failed launch. For example, close it when the requested model is unavailable. A timeout alone does not prove failure. Inspect the pane before you close it.

## Delegation and coordination

1. Give each child a separate, bounded task. Avoid duplicate work unless you want an independent review. Keep simple work lightweight. For complex work, use clear phases: leader planning, parallel execution, verification, then leader integration and targeted fixes.
2. Submit tasks to all independent children before you wait for results. This allows them to work in parallel.
3. Children share the working directory. Assign different files to agents that edit files at the same time. If edits would overlap, sequence the work. Ask the user only when task scope or file ownership is unclear.
4. Use bounded waits, for example `agent prompt <name> <task> --wait --timeout <ms>`. If a wait times out or stalls, inspect `agent get` and `agent read` before retrying. Do not resubmit blindly.
5. Inspect blocked or unfamiliar approval prompts. Do not answer an approval prompt for the user. Report it and ask what to do.
6. Review and validate the children's actual outputs. Prompt submission or a status label alone does not prove that the work is complete.
7. Report the result, validation, blockers, and each child name and pane ID. Do not close panes, agents, tabs, or workspaces unless the user asks for cleanup.
