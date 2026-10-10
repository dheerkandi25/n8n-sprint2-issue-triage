# Own versus rent: self-hosted n8n and Zapier

## Decision for this sprint

Use self-hosted n8n for the course build. It provides a local Webhook, deterministic Switch rules, Data Tables, inspectable executions, and an internal error branch in one workflow. GitHub CLI forwarding is suitable for a short local test session. For an always-on service, the operator would need a stable reachable endpoint, backups, monitoring, and security controls.

## Evidence and assumptions

As checked on 10 October 2026, [Zapier's pricing page](https://zapier.com/pricing) lists Free at $0/month with 100 tasks and two-step Zaps, and Professional starting at $19.99/month with multi-step Zaps and webhooks. Its [Free plan help page](https://help.zapier.com/hc/en-us/articles/32337438839565-What-s-included-in-Zapier-s-Free-plan) also lists 2,500 table records. [n8n's pricing page](https://n8n.io/pricing/) describes a self-hosted Community Edition and lists its paid hosted Starter plan at €20/month billed annually for 2,500 monthly executions. These are published starting prices, not quotes for a specific account.

For a comparison, assume 100 new issues per month, each requiring at least three successful data actions (ledger insert, route-log insert, status update), plus some duplicate deliveries. At a simple one-task-per-action estimate, that is at least 300 Zapier tasks per month before duplicates, above Free's 100-task allowance. Zapier's actual task use depends on the chosen actions and current task rules, so confirm in its builder before purchase. Professional starts at $19.99/month, with the selected task tier possibly costing more.

The n8n Community software license has no subscription price in this local course setup, but self-hosting is not free of operating cost. For illustration, a dedicated 10 W device running continuously at $0.15/kWh uses `0.010 kW × 24 × 30 = 7.2 kWh`, or about $1.08/month in electricity. That excludes hardware, Internet, backups, maintenance time, and any public hosting. The present localhost test uses an already-running computer, so no new hosting purchase is required.

| Consideration | Self-hosted n8n here | Zapier alternative |
| --- | --- | --- |
| Build flexibility | Explicit branch logic and Data Tables in one workflow | Multi-step logic and webhooks require a paid plan |
| Monthly software cost for this example | No Community subscription; operator pays infrastructure and time | At least Professional's published starting price; task tier must be checked |
| Operations | User runs n8n, CLI forwarding, backup, and monitoring | Zapier hosts the automation platform |
| Evidence | Local executions and tables are inspectable | SaaS logs and tables depend on plan and account |
| Portability | JSON workflow export; Data Table rows need separate backup | Rebuild/migrate Zaps and table data if leaving |

The choice could change for a team that values managed uptime and support more than local control. This sprint's evidence is a prototype, not a production cost or reliability benchmark.
