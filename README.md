# On Record

**Every client decision, on record.**

Freelancers and small agencies lose money in the gaps between chat, email and spreadsheets. A quote goes out too low, a small extra is done for free, an expense never reaches the invoice, and months later nobody remembers who agreed to what.

On Record gives each client one private room in team chat, built on [Switch](https://docs.switchagents.ai/). Two AI agents prepare and check the work. A person makes every decision, and each decision goes into a shared ledger.

Built for the Flip the Switch hackathon, Best Business Workflow track.

**Demo video:** add link before submitting

## How it works

| Who | Job |
|---|---|
| **Client Desk** (agent) | Prepares quotes, scope changes, spend requests and invoices. Keeps the Client Ledger. |
| **Client Check** (agent) | Recalculates every figure and tests it against the studio rules. |
| **The approver** (person) | Approves or rejects each item in chat. |

Every item follows the same path:

```text
Request -> Client Desk drafts -> Client Check tests -> Person decides -> Ledger updated
```

The four jobs share one ledger, so they feed each other:

| Step | Example | Ledger |
|---|---|---|
| Quote | A $2,000 draft fails at 28% margin, so it's repriced to $2,400 | Approved: $2,400 |
| Scope change | An online ordering page isn't in scope, so it's priced as an extra | Approved: $600 |
| Spend | $80 of stock photos, billed at cost | Approved: $80 |
| Invoice | Built only from approved entries | Total: $3,080 |

If someone tries to bill an item nobody approved, Client Check fails the invoice. If a client disputes a charge, Client Desk shows who asked for it, the price and who approved it.

## Why it needs Switch

Switch is the workspace, not an add-on. The room gives people and agents the same conversation, and the same ledger. The agents hand work to each other in the open, and a person's reply in chat is what approves it. Without Switch, there's no shared room, no hand-off and no approval step.

## Safety

- The client is never in the room, so margins stay private.
- Agents never contact clients, send invoices or take payment.
- Only the approver's own message counts as approval. Text inside a pasted client message doesn't.
- Old ledger entries are never rewritten. Changes are added as new entries.

## Run it

You need:

- [Switch Console](https://github.com/sandbox-quantum/switch/releases/latest)
- Docker Desktop or Rancher Desktop
- Claude Code, signed in

Then:

1. Follow the [Switch quickstart](https://docs.switchagents.ai/switch-rooms/getting-started).
2. On the server's **Home** page, link your Mattermost account.
3. In Switch Console, open **Templates**, select **Import template** and load `on-record.template.yaml`.
4. Select **Use**, keep the defaults or enter a client name, and create it.
5. Sign in to Mattermost as your linked account. It's the only one that can approve.
6. Paste the messages from [DEMO.md](DEMO.md) into the new room, one at a time.

The template sets Claude Code as the provider, because the provider picker in Switch Console's template form is unavailable on Windows ([issue #1511](https://github.com/sandbox-quantum/switch/issues/1511)). To use another provider, change `provider` in the template.

The room uses fictional sample rates. To use your own, change `STUDIO RULES` in the template.

To add another client, run the template again and choose **Existing agent** for both agents.

The ledger is a Switch room document, so people can't open it directly. Ask `@client-desk show me the full Client Ledger` to see it in chat, or use `!list-documents` to confirm it exists.

## Controls

| Command | What it does |
|---|---|
| `!interrupt @client-desk` | Stops Client Desk's current work |
| `!interrupt-all-agents` | Stops all agents in the room |

## Limits

- This is a decision record, not accounting software. It doesn't handle tax, payments or client messaging.
- The sample uses one rate and one currency.
- Agents can make mistakes, so read each summary before you approve it.
