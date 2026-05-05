This folder contains the source for a Skilled Agent originally built for the Valet runtime. Changes should follow the Skilled Agent open standard.

## Setup

### Connectors

- **mercury**: The Mercury CLI. The agent runs read-only subcommands (`accounts list`, `credit list`, `treasury list`, `transactions list`, `invoices list`, `recipients list`, `cards list`, `customers list`) to compose the morning report and to answer ad-hoc Slack questions. The connector is the catalog entry; it installs the CLI on first run inside the sandbox. See `skills/mercury/SKILL.md` for the CLI's quirks (`PAGER=cat`, root-flags-before-resource, `--max-items` vs `--limit`).

### Channels

- **slack** (slack): The agent's per-agent Slack bot. Listens for @mentions and replies in-thread, and posts the morning report to whichever channels the bot has been invited to. Slack writes use the auto-injected outbound Slack connector.
- **cron** (cron): Fires the morning finance report at 7am Pacific, Monday through Friday (`0 7 * * 1-5`, `America/Los_Angeles`) — sized to land before the team's first standup. Declared inline in `valet.yaml`, so it's created automatically by the dashboard setup flow.

### Secrets

- **MERCURY_API_KEY**: A Mercury API token scoped to the workspace you want the agent to read. **Use a read-only token** — generate it at https://app.mercury.com/settings/tokens with read-only permissions. The token format is `secret-token:mercury_...`. Set it at the org level so other Mercury-powered agents can share it: `valet secrets set MERCURY_API_KEY=secret-token:mercury_... --org <org>`. The SOUL also refuses every mutating subcommand by name, but a read-only token enforces it at the API layer — belt and suspenders.

### External Setup

1. Generate a **read-only** Mercury API token at https://app.mercury.com/settings/tokens. Pick the workspace you want the agent to see and restrict permissions to read-only.
2. After deploy, invite the agent's Slack bot to whichever channel(s) you want the morning finance report in. The agent posts the report to every channel it's a member of — invite it to one focused finance channel, or several. If the bot has not been invited anywhere, the report is sent as a DM to the workspace install user with a one-line nudge to invite it somewhere.
3. Invite the bot to any additional channels where teammates should be able to @mention it for ad-hoc Mercury questions (e.g. an ops channel for *"what's our cash position this morning?"*).
4. The first cron fire is the next 7am Pacific weekday after deploy. To smoke-test sooner, @mention the bot in Slack with a question like *"what's our cash position this morning?"* — that exercises the Slack + Mercury path without waiting for the cron.

## Customizing

- **Change the schedule**: edit the `cron` and `timezone` on the `cron` channel in `valet.yaml`, then redeploy. The default `0 7 * * 1-5` (`America/Los_Angeles`) is sized to land in Slack before the West Coast team's first standup.
- **Control where the report posts**: invite or remove the bot from channels in Slack — that's the only signal the agent uses. There is no channel name in the configuration.
- **Tighten the read-only block list**: the SOUL `Never` section enumerates every mutating Mercury subcommand the agent refuses on sight. If Mercury ships new mutating subcommands, add them to that list and to your read-only token's denied scopes.
- **Tune the runway window**: the SOUL uses a trailing 90-day burn average. If your spend is lumpy (e.g. quarterly tax payments), edit the *Runway* step in `SOUL.md` to use a longer window or to exclude specific counterparties.
