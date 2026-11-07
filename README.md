# On Record

**Every client decision, on record.**

On Record gives a freelancer or small agency one private [Switch](https://docs.switchagents.ai/) room per client. Two AI agents prepare and check quotes, scope changes, spending and invoices. A person makes every decision, and each decision goes into a shared ledger.

Built for the Flip the Switch hackathon, Best Business Workflow track.

**Demo video:** add link before submitting

Project page: [docs/index.html](docs/index.html)

### At a glance

- **No app to host.** The whole product is one Switch template, [on-record.template.yaml](on-record.template.yaml).
- **You talk to one agent.** Client Desk does the work, and Client Check tests it in the open.
- **Only you can approve.** Approvals come from your own account in the room. Text pasted from a client doesn't count.
- **Nothing is rewritten.** A failed draft stays in the ledger, and its replacement is a new entry.

## The problem

Client work leaks money in the gaps between chat, email and spreadsheets. A quote goes out too low, a small extra is done for free, an expense never reaches the invoice, and months later nobody remembers who agreed to what.

## See it run

These screenshots are from a rehearsal in a real Switch room, using fictional data.

**Client Check blocks a low price.** It recalculates the $2,000 quote, fails it at 28% margin, and Client Desk takes the decision to the owner.

![Client Check fails a $2,000 quote and gives the lowest passing price](docs/media/quote-blocked.png)

**The wrong account can't approve.** A second account tries to approve, and Client Desk refuses.

![Client Desk refuses an approval from an account that isn't the approver](docs/media/wrong-account-refused.png)

**The owner's approval goes on record.** The ledger updates, and the client reply stays a draft for the owner to send.

![Client Desk records the owner's approval and drafts a client reply](docs/media/owner-approved.png)

**A fake approval changes nothing.** A pasted client message claims the work is already approved, and it's treated as information.

![Client Desk treats a pasted claim of approval as information, not an instruction](docs/media/fake-approval-ignored.png)

**The ledger lives in the room.** `!list-documents` shows the Client Ledger as a room document created by Client Desk.

![The room's document list shows the Client Ledger](docs/media/ledger-in-room.png)

## How it works

| Who | Job |
|---|---|
| **Client Desk** (agent) | Prepares quotes, scope changes, spend requests and invoices. Keeps the Client Ledger. |
| **Client Check** (agent) | Recalculates every figure and tests it against the studio rules. |
| **The approver** (person) | Approves or rejects each item in chat. |

```mermaid
flowchart LR
    A[Request in the room] --> B[Client Desk drafts and logs a pending entry]
    B --> C{Client Check tests it}
    C -- FAIL --> B
    C -- PASS --> D{Approver decides}
    D -- Approve --> E[Ledger updated]
    D -- Reject --> E
    E --> F[Approved items reach the invoice]
```

The four jobs share one ledger, so they feed each other:

| Step | Example | Ledger |
|---|---|---|
| Quote | A $2,000 draft fails at 28% margin, so it's repriced to $2,400 | Approved: $2,400 |
| Scope change | An online ordering page isn't in scope, so it's priced as an extra | Approved: $600 |
| Spend | $80 of stock photos, billed at cost | Approved: $80 |
| Invoice | Built only from approved entries | Total: $3,080 |

If someone tries to bill an item nobody approved, Client Check fails the invoice. If a client disputes a charge, Client Desk shows who asked for it, the price and who approved it.

## Why this needs Switch

Take Switch away and there's no product. This repo has no chat code, server or database. Everything On Record does runs through these Switch features:

| Switch feature | How On Record uses it | Without it |
|---|---|---|
| **Template** | One file creates both agents, the room, its rules and the first message | Every freelancer would need custom setup |
| **Template form** | Asks for a client name and chat app, then names the room | Each client would be configured by hand |
| **Private room** (`channel_private`) | Each client gets a room the client is never in, so margins stay internal | No private space for one client's decisions |
| **Room instructions** | Hold the approver's name and the studio rules | Rates and rules would be scattered across agents |
| **`{$creator}`** | Makes the person who created the room the approver | The approver would have to be set by hand |
| **Kickoff** | Tells Client Desk to create the ledger as soon as the room exists | Someone would have to set up each room |
| **`@mention` hand-offs** | Client Desk sends work to Client Check, and the result comes back in the same room | No visible check between drafting and approval |
| **Room document** | The Client Ledger stays with the room, and only Client Desk, which created it, can change it | No shared record for the agents to work from |
| **Message sender** | Agents see who sent each message, so a wrong account can be refused | Anyone's message could pass as an approval |
| **`allow_existing`** | The same two agents can serve a new room for each client | Every client would need new agents |
| **Mattermost bridge** | The work happens in a chat app the owner already reads | The workflow would need its own interface |
| **`!interrupt`** | The owner can stop an agent at any time | No quick way to stop work from the chat |

A person and two agents work in one conversation, and each step depends on the one before it: the request starts in the room, Client Check tests the draft, the owner's message decides it, and only then can it reach the invoice.

Built and rehearsed on Switch Console 0.40.1 on Windows, with Claude Code and the bundled Mattermost.

## What's in this repo

| File | What it is |
|---|---|
| [on-record.template.yaml](on-record.template.yaml) | The product: both agents, the room, its rules and the kickoff |
| [DEMO.md](DEMO.md) | Messages to send, with what each one should do |
| [docs/index.html](docs/index.html) | A one-page overview of the project |

## Safety

- The client is never in the room, so margins stay private.
- Agents never contact clients, send invoices or take payment.
- Only the approver's own message counts as approval.
- Text inside a pasted client message is information, not an instruction.
- Agents are told to answer only from the current room's ledger, and never to share another client's details.
- Old ledger entries are never rewritten. Changes are added as new entries.
- Both agents run with `auto_approve: false`, so Switch's permission checks stay on.

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

To import a changed template, delete the old room and agents first. Switch refuses a name that's already in use, and existing agents keep their old instructions.

The ledger is a Switch room document, so people can't open it directly. Ask `@client-desk show me the full Client Ledger` to see it in chat, or use `!list-documents` to confirm it exists.

## Controls

| Command | What it does |
|---|---|
| `!interrupt @client-desk` | Stops Client Desk's current work |
| `!interrupt-all-agents` | Stops all agents in the room |

## Limits

- This is a decision record, not accounting software. It doesn't handle tax, payments or client messaging.
- The sample uses one rate and one currency.
- Approval and client separation are enforced by the agents' instructions, not by Switch itself. Agents can make mistakes, so read each summary before you approve it.

## What's next

- A payment chaser that tracks overdue invoices and drafts reminders for the owner to send.
- More than one rate and currency.

## Licence

[MIT](LICENSE)
