# fab-ast

A skill for planning a build, assigning suitable work to Astra, and checking the files it produces. It works with Claude Code and Codex according to the tools available in each host.

Formerly named `fab-sol`. The default executor is `gpt-6.2-astra`; helper requests use `gpt-6-luna`. A different executor requires an explicit choice and a working route.

## What it does

1. Separates work the current assistant can do from work suited to delegation.
2. Writes a spec with exact outputs, limits and acceptance checks.
3. Records the exact task or process so it can be monitored and stopped safely.
4. Checks the resulting files and runs relevant tests before reporting completion.

In Claude Code, the tested route is Claude Code → local CLIProxyAPI → Astra. In Codex, the assistant checks whether direct work or a permitted native agent is appropriate. Loading this skill in Codex does not automatically launch a proxy session.

## Install

Clone this repository into a new folder, then place its `SKILL.md` inside a `fab-ast` folder in your host's local skills directory. Back up an existing installation before replacing it. In Claude Code, invoke `/fab-ast`; in Codex, select the skill through its skill picker. Menu behavior can vary by app version.

Example request:

> Use fab-ast to build a CSV summary tool in a temporary folder. Check the files and run the tests. Report who did what, the executor model and the results.

## Requirements

The Claude route needs a configured [CLIProxyAPI](https://github.com/router-for-me/CLIProxyAPI), authorized credentials, supported model IDs and the relevant Claude Code tools. The skill checks these before delegation. Model access and account eligibility depend on your setup.

The command in `SKILL.md` is a template. Check current CLI flags, models, context size and required tool grants before running it. Keep credentials local. Never disable permissions to make a dispatch work.

## Test evidence

On 22 November 2026, the owner supplied a Claude Code report of an Astra build that created a CSV utility, sample data and tests in 61 seconds across 4 turns. It reported exit 0, zero permission denials, 14 passing unit tests and 9 separate checks. Codex inspected the saved files and independently reran all 14 unit tests successfully.

The requested and reported model was `gpt-6.2-astra`, with a 272,000-token context, through Claude Code 2.1.311 and CLIProxyAPI 8.1.0. Model attribution comes from Claude's usage report; the proxy logs did not identify the model. This was a small build. Long-build behavior, subscription charges and savings were not measured.

## Updates and upstream projects

This repository publishes the active reusable instructions from the owner's shared local skill. Private run records, revision notes, credentials and packaged local artifacts are excluded. The public export is reviewed and synced as part of the skill's maintenance workflow. It is not a timed background service.

- Skill changes: [commit history](https://github.com/thekhairulakbar/fab-ast/commits/main/).
- Proxy updates: [CLIProxyAPI releases](https://github.com/router-for-me/CLIProxyAPI/releases).
- Claude Code updates: [official repository](https://github.com/anthropics/claude-code).
- Codex updates: [official repository](https://github.com/openai/codex).

Removing the old `fab-sol` installation does not affect access to these projects. New releases and model IDs require a reviewed update and a real test; this skill does not install upgrades automatically.

Created by [thekhairulakbar](https://github.com/thekhairulakbar).
