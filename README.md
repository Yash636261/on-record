# On Record

**When a client says "I already asked for that", your agent shows what was actually agreed.**

On Record gives a freelancer or small agency two [Switch](https://docs.switchagents.ai/) rooms per client. In a shared room, an agent answers the client's questions about scope and price from an agreed record. In a private room, two agents prepare and check the work. A person makes every decision.

Built for the Flip the Switch hackathon, Best Business Workflow track.

**Demo video:** [Watch it on YouTube](https://youtu.be/z5IhkReUPoc)

**Project page:** [docs/index.html](docs/index.html)

### At a glance

- **No app to host.** The whole product is one Switch template, [on-record.template.yaml](on-record.template.yaml).
- **Clients get answers from the record.** Client Record shows what was agreed, with item IDs and prices.
- **Your margins stay private.** Client Record isn't in the private room, so it can't see your costs.
- **Only you can change the record.** A client message saying something was agreed doesn't count.

## The problem

Scope and price get agreed across calls, chats and emails. Later, a client says "I already told you I wanted that", and both sides remember it differently. The freelancer either argues or builds it for free.

Say you agreed to build like and dislike buttons. The client comes back and asks where the save button is. They're sure they asked for it. Without a shared record, nobody can settle it.

## How it works

Each client gets two rooms.

| Room | Who's in it | What it's for |
|---|---|---|
| **Client room** | The owner, the client and **Client Record** | What was agreed, and any questions about it |
| **Desk room** | The owner, **Client Desk** and **Client Check** | Pricing, margins and decisions |

| Agent | Job |
|---|---|
| **Client Record** | Keeps the Project Record, answers from it, and logs anything it can't answer as a query for the owner. |
| **Client Desk** | Prepares quotes, scope changes, spend requests and invoices. Keeps the private Client Ledger. |
| **Client Check** | Recalculates every figure and tests it against the studio rules. |

```mermaid
flowchart TD
    A[Owner adds the agreed scope to the record] --> B[Client asks a question]
    B --> C{Is it in the record?}
    C -- Yes --> D[Client Record answers with item IDs]
    C -- No --> E[Client Record logs a query and tags the owner]
    E --> F[Client Desk prices it in the private room]
    F --> G{Client Check tests it}
    G -- FAIL --> F
    G -- PASS --> H{Owner decides}
    H -- Approve --> I[Owner adds it to the record]
    I --> B
```

| Step | Example | Result |
|---|---|---|
| Quote | A $2,000 draft fails at 28% margin, so it's repriced | Approved: $2,400 |
| Record | The owner adds like and dislike buttons to the record | R-001 |
| Confusion | The client asks where the save button is | "The record has no request for this." Query logged |
| Change | The save button is priced in the private room | Approved: $300 |
| Clear-up | The owner adds it to the record | R-002, agreed total $2,700 |
| Invoice | Built only from approved entries | Total: $2,700 |

The agent never says "you didn't ask for that". The client may have asked somewhere the record can't see, so it says the record has no request for it. If the client has proof, the owner decides.

## See it run

[![The On Record demo video](docs/media/client-save-button.png)](https://youtu.be/z5IhkReUPoc)

These screenshots are from a real run in Switch, using fictional data. Select the first one to watch the demo.

**The client asks for something that was never agreed.** Client Record shows the agreed scope, says the record has no request for it, and logs a query for the owner.

**The client can't change the record.** A message saying the owner already agreed doesn't count.

![Client Record refuses to change the record when the client says the owner agreed](docs/media/client-fake-approval.png)

**Margins stay private.** Client Record isn't in the desk room, so it has no information about costs.

![Client Record says it has no information about costs or margins](docs/media/client-margin-refused.png)

**Both sides see the same record.** After the owner adds the paid change, the agreed total is $2,700.

![Client Record shows both agreed items and a $2,700 total](docs/media/client-agreed-total.png)

**Only approved work is billed.** Client Check recalculates the invoice before it's posted.

![Client Check passes the invoice and Client Desk posts it at $2,700](docs/media/final-invoice.png)

**A low price is blocked in private.** Client Check fails the $2,000 quote at 28% margin.

![Client Check fails a $2,000 quote and gives the lowest passing price](docs/media/quote-blocked.png)

**The wrong account can't approve.** In a rehearsal, a second account tried to approve and Client Desk refused.

![Client Desk refuses an approval from an account that isn't the approver](docs/media/wrong-account-refused.png)

## Why this needs Switch

Take Switch away and there's no product. This repo has no chat code, server or database. Everything On Record does runs through these Switch features:

| Switch feature | How On Record uses it | Without it |
|---|---|---|
| **Template** | One file creates three agents, two rooms, their rules and the first messages | Every freelancer would need custom setup |
| **Template form** | Asks for the client's name, chat account and chat app | Each client would be configured by hand |
| **Room group** | Keeps each client's two rooms together | Two rooms would be set up separately |
| **Private room** (`channel_private`) | Keeps the desk room away from the client | No private space for pricing |
| **Room membership** | Keeps Client Record out of the desk room, so it can't reach margins | The client's agent could see internal costs |
| **Room instructions** | Name the owner and client, and hold the studio rules | Rules would be scattered across agents |
| **`{$creator}`** | Makes the person who created the rooms the owner | The owner would have to be set by hand |
| **`user` parameter** | Adds the client's account to the client room | The client would be invited by hand |
| **Kickoff** | Tells the agents to create the record and ledger | Someone would have to set up each room |
| **`@mention` hand-offs** | Client Desk sends work to Client Check, and the result comes back in the same room | No visible check before the owner decides |
| **Room document** | Stores the Project Record and the Client Ledger, and only the agent that created each one can change it | No shared record to answer from |
| **Message sender** | Agents see who sent each message, so a client can't approve their own request | Anyone's message could change the record |
| **Mattermost bridge** | The work happens in a normal team chat | The workflow would need its own interface |
| **`!interrupt`** | The owner can stop an agent at any time | No quick way to stop work from the chat |

People and agents work in the same conversations, and each step depends on the one before it: the client's question starts in the room, the owner's message decides it, and only then can it reach the record and the invoice.

Both rooms were built and rehearsed on Switch Console 0.40.1 on Windows, with Claude Code and the bundled Mattermost.

## What's in this repo

| File | What it is |
|---|---|
| [on-record.template.yaml](on-record.template.yaml) | The product: three agents, two rooms, their rules and the kickoffs |
| [DEMO.md](DEMO.md) | Messages to send, with what each one should do |
| [docs/index.html](docs/index.html) | The project page, including setup and the demo steps |

## Safety

- The client is never in the private desk room.
- Client Record never prices, negotiates, promises or approves.
- Only the owner's own message can change the record.
- A client message saying something was approved doesn't count.
- Agents are told to answer only from the current room's record, and never to share another client's details.
- Old items are never rewritten. Changes are added as new items.
- All agents run with `auto_approve: false`, so Switch's permission checks stay on.

## Run it

You need:

- [Switch Console](https://github.com/sandbox-quantum/switch/releases/latest)
- Docker Desktop or Rancher Desktop
- Claude Code, signed in

Then:

1. Follow the [Switch quickstart](https://docs.switchagents.ai/switch-rooms/getting-started).
2. On the server's **Home** page, link your Mattermost account.
3. In Switch Console, open **Templates**, select **Import template** and load `on-record.template.yaml`.
4. Select **Use**, enter a client name and the client's chat account, and create it. The bundled Mattermost has a `user` account you can use as a test client.
5. Sign in to Mattermost as your linked account. It's the only one that can approve. Use a second window for the client account.
6. Follow [DEMO.md](DEMO.md), one message at a time.

The template sets Claude Code as the provider, because the provider picker in Switch Console's template form is unavailable on Windows ([issue #1511](https://github.com/sandbox-quantum/switch/issues/1511)). To use another provider, change `provider` in the template.

The rooms use fictional sample rates. To use your own, change `STUDIO RULES` in the template.

To add another client, run the template again and choose **Existing agent** for all three agents.

To import a changed template, delete the old rooms and agents first. Switch refuses a name that's already in use, and existing agents keep their old instructions.

The record and ledger are Switch room documents, so people can't open them directly. Ask `@client-record show the full Project Record` in the client room, or `@client-desk show me the full Client Ledger` in the desk room. Use `!list-documents` to confirm they exist.

## Controls

| Command | What it does |
|---|---|
| `!interrupt @client-record` | Stops one agent's current work |
| `!interrupt-all-agents` | Stops all agents in the room |

## Limits

- This is a decision record, not accounting software. It doesn't handle tax, payments or legal agreements.
- The record only knows what was added to it. Agreements made elsewhere need adding by the owner.
- The sample uses one rate and one currency.
- Approval rules are enforced by the agents' instructions, not by Switch itself. Agents can make mistakes, so read each summary before you act on it.

## What's next

- A payment chaser that tracks overdue invoices and drafts reminders for the owner to send.
- More than one rate and currency.

## Licence

[MIT](LICENSE)
