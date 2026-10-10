# GitHub issue triage runbook

## Purpose

Use this runbook for the harmless course repository at https://github.com/dheerkandi25/n8n-sprint2-issue-triage. The workflow runs on local n8n, forwards GitHub `issues` events through GitHub CLI, and records outcomes in `github_issue_ledger` and `github_issue_route_log`. Evidence is in [test_evidence.md](test_evidence.md).

## Normal operation

Start n8n on port 5678, publish `Sprint 2 — GitHub Issue Triage`, and run the `gh webhook forward` command in the README in a terminal kept open for the test. Open one exact test issue. Confirm the n8n execution reaches `Update route-log recorded`, the ledger row for `repository#number` has status `recorded`, and exactly one route-log row has the expected `route_name`. Check the issue link and execution ID in [test_evidence.md](test_evidence.md).

## Duplicate delivery

In repository Settings → Webhooks → the temporary webhook → Recent deliveries, redeliver the original bug issue delivery within GitHub's three-day window. Confirm a second n8n execution reaches `Update existing last seen`, the ledger still has one row for that issue key, `last_seen_at` has advanced, and the route-log still has one bug row. The before/after counts are in [test_evidence.md](test_evidence.md). Do not open another bug issue for this check.

## Route-log failure

Open the exact intentional-test issue in [test_evidence.md](test_evidence.md). Its title contains `intentional route-log failure test`, so `Simulate route-log failure` throws and its error output reaches `Update route-log failed`. Confirm the ledger row remains with status `failed` and the safe error note; there must be no route-log row for that key. For an unexpected failure, inspect the n8n execution and the `Record internal route` node, without copying raw error data into the public repository.

## Recovery action

Fix the cause in the local route-log node or table schema, then inspect the failed ledger row and check whether a route-log row already exists for its `issue_key`. If no row exists, insert one internal route-log row with the stored category, route name, and recovery timestamp; update the ledger to `recorded` and clear `error_note`. If the row exists, only update the ledger. Record the recovery in private operations notes. A webhook redelivery alone will only update `last_seen_at` because the key already exists; it will not retry routing.

## Safe shutdown

After capturing evidence and exporting the workflow, unpublish/deactivate the n8n workflow, remove or disable the temporary repository webhook in GitHub Settings → Webhooks, and press Ctrl+C in the GitHub CLI forwarding terminal. Confirm no forwarding process remains. The Data Tables and public issue evidence may remain. The shutdown action and time are recorded in [test_evidence.md](test_evidence.md).

## What this prototype does not test

The local forwarding terminal is not a production endpoint. The exercise does not test concurrent deliveries for the same issue key, long-term uptime, public endpoint security, automatic retry of failed route writes, or outside notifications. The two branch existence checks are sufficient for the serial course tests but do not make concurrent inserts atomic.
