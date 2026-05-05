The Mercury CLI requires you to get UUIDs to use the service - make sure to list and capture the UUIDs from accounts, credit, and treasury to get all 'accounts'. 

─── Running commands non-interactively ──────────────────────────────────────────────────────────────

Always invoke the CLI with this shape:

  PAGER=cat mercury <root-flags> <resource> <subcommand> <subcommand-flags>

Three rules that bite if you get them wrong:

1. Disable the pager. When stdout is a TTY, mercury pipes output through
   `$PAGER` (default `less`) and hangs waiting for keypresses. There is no
   `--no-pager` flag. Prefix every invocation with `PAGER=cat` (or pipe to
   `| cat`).

2. Root flags go BEFORE the resource, subcommand flags go AFTER.
   `--format`, `--format-error`, `--transform`, `--transform-error`,
   `--raw-output` / `-r`, `--debug`, `--yes` / `-y`, `--base-url`,
   `--api-key`, `--environment` are root flags on `mercury` itself —
   placing them after the subcommand makes them unknown flags.

3. `--limit` is API page size, NOT a result cap. List commands
   (e.g. `transactions list`) use auto-pagination and will keep fetching
   pages regardless of `--limit`. Use `--max-items N` to cap total
   results. Use `--format raw` to skip auto-pagination and get exactly
   one API page envelope.

Canonical example — last 10 transactions, newest first, as JSON:

  PAGER=cat mercury --format json transactions list --max-items 10 --order desc

For line-delimited JSON (one record per line, friendlier for piping):

  PAGER=cat mercury --format jsonl transactions list --max-items 10 --order desc

─── Usage ───────────────────────────────────────────────────────────────────────────────────────────
╭─────────────────────────────────────────────────────────────────────────────────────────────────╮
│   mercury <resource> <subcommand> [OPTIONS]                                                     │
╰─────────────────────────────────────────────────────────────────────────────────────────────────╯

─── Options ─────────────────────────────────────────────────────────────────────────────────────────
╭─────────────────────────────────────────────────────────────────────────────────────────────────╮
│       --debug             Enable debug logging                                                  │
│   -y, --yes               Skip confirmation prompts                                             │
│       --base-url          Override the base URL for API requests                                │
│       --format            Output format (auto|json|jsonl|pretty|raw|yaml|explore)               │
│       --format-error      Error format (auto|json|jsonl|pretty|raw|yaml|explore)                │
│       --transform         Transform output with a GJSON expression                              │
│       --transform-error   Transform error output with a GJSON expression                        │
│   -r, --raw-output        If the result is a string, print it without JSON quotes. This can be  │
│ useful for making output transforms talk to non-JSON-based systems.                             │
│       --api-key           Mercury API token (from dashboard settings)                           │
│       --environment       API environment: sandbox or production                                │
│   -h, --help              show help                                                             │
│   -v, --version           print the version                                                     │
╰─────────────────────────────────────────────────────────────────────────────────────────────────╯

─── Auth ────────────────────────────────────────────────────────────────────────────────────────────
╭─────────────────────────────────────────────────────────────────────────────────────────────────╮
│   login                   Sign in to Mercury in your browser                                    │
│   logout                  Sign out and delete saved tokens                                      │
│   status                  Show Mercury sign-in status per environment                           │
╰─────────────────────────────────────────────────────────────────────────────────────────────────╯

─── Resources ───────────────────────────────────────────────────────────────────────────────────────
╭─────────────────────────────────────────────────────────────────────────────────────────────────╮
│   accounts                List and view bank accounts                                           │
│   cards                   List debit and credit cards for an account                            │
│   categories              List expense categories                                               │
│   credit                  List credit accounts                                                  │
│   customers               Create, update, and manage customers                                  │
│   events                  List and inspect API events                                           │
│   invoices                Create, update, and manage invoices                                   │
│   org                     View organization details                                             │
│   payments                Send money, request approvals, and transfer between accounts          │
│   recipients              Add, update, and manage payment recipients                            │
│   safes                   List and download SAFE agreements                                     │
│   statements              Download account statements as PDF                                    │
│   transactions            Search, update, and attach files to transactions                      │
│   treasury                View treasury accounts and transactions                               │
│   users                   List and view organization team members                               │
│   webhooks                Set up and manage webhook endpoints                                   │
╰─────────────────────────────────────────────────────────────────────────────────────────────────╯

─── Utility ─────────────────────────────────────────────────────────────────────────────────────────
╭─────────────────────────────────────────────────────────────────────────────────────────────────╮
│   upgrade                 Upgrade mercury to the latest release                                 │
╰─────────────────────────────────────────────────────────────────────────────────────────────────╯

  v0.6.4
