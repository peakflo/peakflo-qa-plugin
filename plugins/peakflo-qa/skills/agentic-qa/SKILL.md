---
name: agentic-qa
description: Verify Peakflo coding changes with peakflo/peakflo-ui-integration-test and generate executable local Playwright specs for affected UI flows.
---

# Agentic QA

## Install and run the QA stack

Repo: `https://github.com/peakflo/peakflo-ui-integration-test.git`. Clone it **once** on the designated QA VM (`git clone <repo-url>`) and set `PEAKFLO_AUTOMATED_TEST_PATH` to its absolute path. If unset, use the current checkout only when its `package.json` name is `peakflo-ui-integration-test`; otherwise locate or clone the QA repo. Read its `AGENTS.md`, `README.md`, and `docs/onboarding.md`.

First install needs Git, Node 24 for QA, Node 22 for the app, Yarn 1, MongoDB 8, PostgreSQL 17, and access to the private app repos/packages. From the QA repo root:

```sh
nvm install 22
nvm install && nvm use
npm ci
npm run setup
```

Setup clones/builds pinned `peakflo-web` and `upload-functions` siblings when absent. To test a different code checkout, set `PEAKFLO_WEB_ROOT` or `UPLOAD_FUNCTIONS_ROOT` before setup/start. Configure `.env.local` for Slack, then run `npm run doctor && npm start`. Without Slack credentials, use `npm run doctor -- --no-slack` and `npm start -- --no-slack`. Wait for the literal `READY` line; doctor success alone is insufficient. For standalone agentic requests without Slack, run `npm run qa -- request "<requirements>"` and `npm run qa:worker` in another terminal.

Keep **one shared Slack listener** on the designated VM and **one live stack on default ports per VM**. Reuse that stack across coding workspaces; separate QA worktrees are useful for editing different branches, but do not start another listener or run concurrent browser suites against the shared stack. The queue serializes jobs and `playwright.local.config.ts` sets `workers: 1`. For parallel browser runs, use separate isolated VMs/stacks with independent ports and databases, and disable Slack on extra stacks. A stack serves one app/backend revision at a time; switch and restart between branch-specific runs.

## Use in a 20x coding task

Attach this skill to a QA subtask after implementation. Give it the parent task ID, sourced acceptance criteria, changed repo, exact commit/branch, and QA repo. The 20x agent should author/reuse specs and run checks directly; do not invoke `qa:worker` from inside another 20x agent. Run static checks in the task workspace, then serialize browser execution on the dedicated QA VM against the exact app revision. Return per-criterion status and evidence before the parent task moves to review. The skill alone does not install this trigger or runner.

## Verify each coding task

Preserve the requested outcomes; current app behavior is not the expected rule. Run `npm run test:preflight` and `npm test -- --list`. Search `test/INDEX.md`, `test/index.json`, `test/regression/local/`, and `test/requests/` for reusable assertions.

For missing UI coverage, inspect the live DOM, then add `test/requests/<id>/plan.local.json` and `test/requests/<id>/playwright/*.local.spec.ts`. Follow `test/requests/LOCAL-QA-PILOT/plan.local.json`, import the shared local `test`/`expect` fixtures, and register scenario IDs in both indexes. Use stable accessible locators, isolated synthetic data, correct roles, and assertions on the requested result and persistence. Exploration alone is not a pass.

Run `npm test -- <spec-path>`, `npm run test:unit`, and `npx tsc --noEmit`; run full local `npm test` when warranted. For changes without a browser flow, use relevant non-browser checks. Report passed, failed, blocked, and untested outcomes with evidence; never count a blocked start or unexecuted spec as passed.
