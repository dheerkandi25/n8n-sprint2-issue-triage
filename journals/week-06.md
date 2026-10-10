# Week 6 Build Journal

## Goal and context

Build a local n8n workflow that triages new GitHub issues from the public course repository, stores a durable ledger, writes an internal route log, ignores duplicate deliveries, and exposes a failed route-log state. The workflow is deliberately deterministic and does not use an LLM or outside notification service.

## Build notes

- Continued the existing `GithubIssueTirage` workflow in n8n and renamed it `Sprint 2 — GitHub Issue Triage`.
- Completed the required Data Table schemas. The starter tables had placeholder columns from the tutorial; the named columns used by the workflow were added.
- Corrected the Switch fallback and regular expressions, connected the help and other branches, and made each category-setting node preserve normalized fields.
- Added parallel existence checks, a pending ledger insert, route-log write, recorded/failed updates, and the exact intentional failure phrase.
- Used only harmless synthetic local events for preflight checks; the public issue tests and their execution IDs are in [test_evidence.md](../test_evidence.md).

## What I learned

The first preflight execution exposed a field-mapping error: Edit Fields nodes were dropping normalized fields before the ledger insert. Preserving the incoming fields fixed the `issue_key`, URL, title, reporter, and timestamps. The second preflight showed a recorded ledger row. Redelivery of the same key did not add another route-log row, and the intentional failure branch retained a readable failed ledger row.

## Extensibility observation

The ledger and route log separate event receipt from routing. A future route could be added by expanding the Switch and adding one fixed category/route pair, while retaining the duplicate and failure paths. A production extension would first add an atomic uniqueness guarantee for `issue_key` and an explicit retry/recovery workflow for rows left in `failed` or `pending`; the current parallel checks are meant for serial course tests.

## Next iteration

The local forwarding method is temporary. A deployed version would need a stable HTTPS webhook endpoint, signature verification, monitoring, backups, and an operational retry process. Those are outside this sprint's evidence requirements.
