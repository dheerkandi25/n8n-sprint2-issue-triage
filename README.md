# GitHub Issue Triage with local n8n

Repository: https://github.com/dheerkandi25/n8n-sprint2-issue-triage (public)

This repository contains harmless course test issues and evidence for a local, event-driven n8n workflow. GitHub CLI forwards `issues` webhooks from this repository to `http://localhost:5678/webhook/sprint2-issue-triage`. The n8n workflow uses two Data Tables: `github_issue_ledger` for durable state and `github_issue_route_log` for internal routing evidence. It sends no external notification and uses no AI classifier.

## Workflow

`GitHub issue webhook` → `Opened action?` → `Normalize issue` → `Classify title` → one of four `Set … route` nodes. Each route then checks whether `issue_key` exists. An existing delivery updates only `last_seen_at`. A new delivery inserts a `pending` ledger row, tests the intentional failure phrase, writes the internal route log on the normal branch, and updates the ledger to `recorded`. Either route-log error branch updates the ledger to `failed` with a safe note.

The opened-action gate accepts `body.action == opened`. Edits, closes, and reopens stop before any Data Table write. The normalized fields are `repository_full_name`, `issue_number`, `issue_key` (`repository.full_name + '#' + issue.number`), `issue_url`, `title`, `title_lower`, `reporter`, `opened_at`, and `last_seen_at`. Route nodes add `category` and `route_name` while preserving those normalized fields.

| First matching title rule, case insensitive | Category | Route name |
| --- | --- | --- |
| `\b(bug\|error)\b` | `bug` | `bug-triage` |
| `\b(feature\|request)\b` | `feature` | `feature-requests` |
| `\b(help\|question)\b` | `help` | `support` |
| No rule matches | `other` | `general-triage` |

The rule order is significant. The workflow uses the lowercased title and the first matching Switch output.

## Local setup

1. Run self-hosted n8n on `http://localhost:5678` and verify both Data Tables contain the columns shown in the [workflow export](sprint2_github_issue_triage.json).
2. Publish the workflow in n8n. Its production Webhook path is `/webhook/sprint2-issue-triage` and method is `POST`.
3. Sign in to GitHub CLI and install `cli/gh-webhook` if needed. While testing, run:

   ```sh
   gh webhook forward --repo=dheerkandi25/n8n-sprint2-issue-triage --events=issues --url=http://localhost:5678/webhook/sprint2-issue-triage
   ```

4. Create the exact harmless issues listed in [test_evidence.md](test_evidence.md), one at a time, verifying each execution and Data Table row before the next.
5. After evidence capture, unpublish the workflow, remove or disable the temporary GitHub webhook, and stop the forwarding command. See [runbook.md](runbook.md).

The exported JSON is a submission-safe copy. It contains no credential values. The working workflow stays in the local n8n instance; the export is not a backup of Data Table rows.

## Evidence

See [test_evidence.md](test_evidence.md) for issue links, n8n execution IDs, route outcomes, duplicate delivery, edited-action gate, and the intentional failure case. [own_vs_rent.md](own_vs_rent.md) compares this self-hosted design with Zapier, and [journals/week-06.md](journals/week-06.md) records the build decisions.
