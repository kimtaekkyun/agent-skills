# Agent Skills

A collection of portable skills for AI coding agents.

## Skills

### [html-report](skills/html-report/SKILL.md)

Creates standalone HTML reports with a consistent visual style. Its canonical template is bundled at [`skills/html-report/report-template.html`](skills/html-report/report-template.html).

### [herdr-agent-team](skills/herdr-agent-team/SKILL.md)

Creates and coordinates a one-level Herdr team of up to five child agents, keeping every child pane to the leader's right and stacking siblings vertically.

### [jev-cli](skills/jev-cli/SKILL.md)

Bundles [`scripts/jev-cli`](skills/jev-cli/scripts/jev-cli) and guides AI agents to send one or several Noul, Choice, and Score questions in a single System One request.

## Install

Requires Node.js 22.20 or later. From the project where you want to use the skill, run:

```sh
npx skills add kimtaekkyun/agent-skills --skill html-report
npx skills add kimtaekkyun/agent-skills --skill herdr-agent-team
npx skills add kimtaekkyun/agent-skills --skill jev-cli
```

The CLI detects available agents and guides you through installation. By default, the skill is installed for the current project.

The `jev-cli` skill bundles `scripts/jev-cli`, so installing the skill installs both its instructions and utility. It needs Python 3 and no `pip install`. The agent runs the bundled script by its path inside the installed skill directory; no global `PATH` setup is needed. Set `TYPESAFE_API_KEY` in the environment before use.
