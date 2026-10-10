# Sprint 2 test evidence

Repository: https://github.com/dheerkandi25/n8n-sprint2-issue-triage

The n8n workflow runs locally at `http://localhost:5678/workflow/uP6n2Ylf9PMjwXxm`. Its execution links and Data Tables are local evidence; GitHub issue links are public. All issue titles and bodies below are harmless course content.

## Required opened issues

| Test | Issue and exact title | Expected classification and route | n8n execution | Ledger and route-log verification |
| --- | --- | --- | --- | --- |
| 1 | [Bug: blank screen after logout](https://github.com/dheerkandi25/n8n-sprint2-issue-triage/issues/1) | `bug` → `bug-triage` | [#14](http://localhost:5678/workflow/uP6n2Ylf9PMjwXxm/executions/14) | One ledger row for `dheerkandi25/n8n-sprint2-issue-triage#1` with `recorded`; one `bug-triage` route-log row |
| 2 | [Feature request: export task list to CSV](https://github.com/dheerkandi25/n8n-sprint2-issue-triage/issues/2) | `feature` → `feature-requests` | [#15](http://localhost:5678/workflow/uP6n2Ylf9PMjwXxm/executions/15) | One ledger row for `#2` with `recorded`; one `feature-requests` route-log row |
| 3 | [Help: change the public webhook address](https://github.com/dheerkandi25/n8n-sprint2-issue-triage/issues/3) | `help` → `support` | [#16](http://localhost:5678/workflow/uP6n2Ylf9PMjwXxm/executions/16) | One ledger row for `#3` with `recorded`; one `support` route-log row |
| 4 | [Documentation: clarify local setup](https://github.com/dheerkandi25/n8n-sprint2-issue-triage/issues/4) | `other` → `general-triage` | [#17](http://localhost:5678/workflow/uP6n2Ylf9PMjwXxm/executions/17) | One ledger row for `#4` with `recorded`; one `general-triage` route-log row |

## Redelivery of Test 1

The original Test 1 GitHub delivery had GUID `516583d4-c4f2-11f1-8b4c-4553d46cb943`. GitHub's Recent Deliveries UI accepted a redelivery at 21:35:32 UTC; the delivery API marked it `redelivery=true`. The running `gh webhook forward` session did not produce an n8n execution for that attempt. To verify the n8n duplicate guard, the public fields of the same issue were replayed once to the local production Webhook. [Execution #21](http://localhost:5678/workflow/uP6n2Ylf9PMjwXxm/executions/21) reached `Update existing last seen` and produced zero items on `If ledger row does not exist`. The original ledger row remained `recorded`; `last_seen_at` advanced from about 17:34:06 to 17:37:07 local time. The route-log table still showed exactly one row for `dheerkandi25/n8n-sprint2-issue-triage#1`. No second GitHub issue was created. This is a local replay verification after a GitHub redelivery that the CLI did not forward, rather than a fully observed end-to-end redelivery.

## Edited-action gate

The body of [Test 4](https://github.com/dheerkandi25/n8n-sprint2-issue-triage/issues/4) was edited to append exactly: `Edited only to test that non-opened issue actions do not enter triage.` [Execution #19](http://localhost:5678/workflow/uP6n2Ylf9PMjwXxm/executions/19) showed one item on the false output of `Opened action?` and no downstream Data Table nodes. The ledger and route-log counts did not increase.

## Intentional route-log failure

Fifth issue title: `Documentation: intentional route-log failure test`.

Body: `Temporary course test of the internal route-log failure path. No user action is required.`

[Issue #5](https://github.com/dheerkandi25/n8n-sprint2-issue-triage/issues/5) generated [execution #20](http://localhost:5678/workflow/uP6n2Ylf9PMjwXxm/executions/20). The trace reached `Simulate route-log failure` and `Update route-log failed`. The output retained one ledger row for `dheerkandi25/n8n-sprint2-issue-triage#5` with `category=other`, `route_name=general-triage`, `route_log_status=failed`, and `error_note=Route-log write failed; review the route-log node configuration and execution details.` The route-log table had no row for `#5`.

## Local preflight, separate from GitHub evidence

Before GitHub CLI forwarding, harmless synthetic events were sent to the local production Webhook to validate behavior. Execution `#9` recorded a bug row with a populated `issue_key`; execution `#10` redelivered that same key without a second route-log row; execution `#11` reached `Update route-log failed` and retained a readable failed ledger row. An edited-action event was also sent. These `local-validation/...` records do not count as the required GitHub issue tests.

## Shutdown

At approximately 21:38 UTC on 10 October 2026, the n8n workflow was unpublished and n8n restarted so deactivation took effect. GitHub CLI forwarding was stopped with Ctrl+C. A subsequent GitHub repository webhook listing returned `0` hooks. The five public issues and the two local Data Tables remain as evidence.
