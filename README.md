# Agent Skills

A collection of portable skills for AI coding agents.

## Skills

### [html-report](skills/html-report/SKILL.md)

Creates standalone HTML reports with a consistent visual style. Its canonical template is bundled at [`skills/html-report/report-template.html`](skills/html-report/report-template.html).

### [herdr-agent-team](skills/herdr-agent-team/SKILL.md)

Creates and coordinates a one-level Herdr team of up to five child agents, keeping every child pane to the leader's right and stacking siblings vertically.

## Install

Requires Node.js 22.20 or later. From the project where you want to use the skill, run:

```sh
npx skills add kimtaekkyun/agent-skills --skill html-report
npx skills add kimtaekkyun/agent-skills --skill herdr-agent-team
```

The CLI detects available agents and guides you through installation. By default, the skill is installed for the current project.
