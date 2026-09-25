# Peakflo QA plugin

Remote Claude-style plugin for agentic QA and Playwright test authoring. This
repository contains instructions only. The application and QA harness remain in
private repositories, so the agent needs access to them before it can run tests.

## Install in 20x

Open Settings → Plugins → Marketplaces, choose **GitHub**, enter
`peakflo/peakflo-qa-plugin`, and add it as `peakflo`. In **Discover**, install
`peakflo-qa` and assign `peakflo-qa:agentic-qa` to the QA task or agent.

## Install in Claude Code

```sh
claude plugin marketplace add peakflo/peakflo-qa-plugin
claude plugin install peakflo-qa@peakflo
```

Set `PEAKFLO_AUTOMATED_TEST_PATH` to an authorized checkout of
`peakflo/peakflo-ui-integration-test`. The skill also explains how to clone and
start that repository on a designated QA VM.
