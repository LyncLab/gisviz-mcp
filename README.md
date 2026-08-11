# GISviz MCP — connect

Produce MTA-spec maritime drawing sheets by asking Claude in plain English.
This repository is the **connection guide only**. The plotting toolkit, the chart
layers and the data stay on LyncLab's server.

You need two things from the maintainer:

1. Access to the LyncLab tailnet (Tailscale)
2. Your personal **licence key** — one line beginning `gvz1.`

---

## Connect

Claude Desktop → Settings → Developer → Edit Config:

```json
{
  "mcpServers": {
    "gisviz": {
      "url": "http://100.121.170.8:8787/mcp",
      "headers": { "Authorization": "Bearer gvz1.…your key…" }
    }
  }
}
```

Claude Code:

```bash
claude mcp add --transport http gisviz http://100.121.170.8:8787/mcp \
  --header "Authorization: Bearer gvz1.…your key…"
```

Restart, then ask **"Check my GISviz access."**

---

## Two locks, not one

**Tailscale controls reach.** `100.121.170.8` is a tailnet address. A device that
is not on the tailnet cannot route to it at all — which is why this guide can be
public without exposing anything.

**Your licence controls use.** It is signed, expires, and is **tied to your
machine**. Forwarding the line to a colleague gives them nothing; they need their
own. Everything you produce is attributable to you.

---

## Staying current

**Tool changes reach you automatically.** MCP sends the tool list on every
connection, so when the maintainer adds a capability your client sees it the next
time it connects. Restarting Claude is enough — there is nothing to install and
no version to match.

**This repository is for you and your AI to read**, not for capability discovery.
`TOOLS.md` is generated from the running server, so it describes what is actually
there rather than what someone remembered to write down.

If you want your assistant to catch up on what changed:

> *"Re-read https://github.com/LyncLab/gisviz-mcp/blob/main/TOOLS.md and tell me what's
> new since we last used GISviz."*

Nothing here needs to be pulled for the tool to work. It is documentation.

---

## What is here

| file | for |
|---|---|
| `README.md` | connecting — this page |
| `TOOLS.md` | every tool and argument, generated from the live server |
| `HANDBOOK-USER.md` | how to use it well — read this once |

---

## Quick start, once connected

> *"What ports can you plot?"* — 54 Malaysian ports, by name

> *"Plot Kuantan for January at 1:150,000, drawing number LL-DS-26-01-KU-001."*

> *"That's too dense — sample it to 10%."*

> *"Now at 600 dpi for the client."*

You will always be asked for a **title, subtitle and drawing number**. These name
a client on a document that leaves the building, so the tool refuses without them.

Charts are **deleted from the server after 2 days** — download anything you need
to keep.

---

## If something is wrong

The error messages are written to be acted on. Two worth recognising:

**"This licence is registered to … but the request came from …"** — you are on a
different machine or network than the key was issued for. Ask for a reissue,
quoting the licence id in the message.

**"No BigQuery credentials are set up for …"** — your data access has not been
provisioned yet. The message names exactly what the maintainer must run.

If the tool cannot do what you need, ask Claude to **file a request** — it writes
up what you wanted and the settings you were using, and hands you a pre-filled
link.
