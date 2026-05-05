# Daily Morning Finance Report

The cron channel fires once on its schedule (7am Pacific, Monday
through Friday). There is no payload to parse — your job is to
run the morning report workflow and post the result to Slack.

## Steps

1. Follow the **Daily Report Workflow** in SOUL.md (Phases 1–3):
   - **Gather** — cash on hand (sum balances across `accounts
     list`, `credit list`, `treasury list`), AP (open bills,
     scheduled outbound payments), AR (open invoices owed to the
     workspace), runway (cash ÷ trailing-90-day average monthly
     net burn), and unusual movement (transactions in the
     trailing 24 hours that are >2× the 30-day median, or
     reversals).
   - **Format** — the Slack `mrkdwn` template in SOUL.md. Quote
     every ID and dollar amount in backticks, verbatim from the
     CLI. Under 2,500 characters. Omit empty sections.
   - **Post** — to the resolved Slack destinations (next step).
2. Resolve target channels per the SOUL **Where to post** section:
   list every channel the bot is a member of and post once to
   each. If the bot is in zero channels, DM the workspace install
   user instead with the report and a one-line invite hint.
3. Post exactly once per resolved destination. Do not retry on
   failure — log the error in your session and continue with the
   remaining destinations. Tomorrow's cron fire is the recovery.
4. Do not send any follow-ups, reactions, or thread replies after
   the initial post. Your turn ends after the posts complete.

## Skip conditions

Skip posting (and stop silently) if any of these are true:

- The Mercury connector is not configured (the `MERCURY_API_KEY`
  secret is missing — the CLI will error on auth; treat that as
  "not configured" and stop).
- `accounts list` returns zero accounts (no workspace data to
  report on). Post a single line `No Mercury accounts configured
  — nothing to report.` to the resolved destinations and stop.

## Token safety

If a CLI command errors and the error text contains
`secret-token:mercury_...` or any `MERCURY_API_KEY` value,
**scrub it** before logging or posting.
