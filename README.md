# GISviz MCP — connect

Produce MTA-spec maritime drawing sheets by asking Claude in plain English.
This repository is the **connection guide only**. The plotting toolkit and the
chart layers stay on LyncLab's server.

**You bring the data.** GISviz draws; it does not query anything. Run your own
query wherever you normally run it, export CSV or Parquet, and upload that. What
you can plot is exactly what your query returned.

You need two things from the maintainer:

1. Access to the LyncLab tailnet (Tailscale)
2. Your personal **service key** — one line beginning `gvz1.`

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

## Send your data

Ask Claude: **"I want to upload a file — give me the command."** It hands you a
ready-to-run line with a **one-time link** in it:

```bash
curl -T ~/Downloads/my_query.parquet http://100.121.170.8:8787/upload/tk_…
```

Run it. You get back a **data id** — `ds_a1b2c3d4e5f6` — and a summary of what
the file holds. Then just say *"plot ds_a1b2c3d4e5f6 for Port Klang."*

**Your service key stays in your Claude config and nowhere else.** The upload
link carries a one-time ticket instead, so there is no key file to create and no
secret on your command line. The link works once and expires in 45 minutes — ask
for another any time, it costs nothing.

**Claude cannot run the command for you**: the file is on your machine, not the
server's. Everything after it is conversation.

Your file needs **LON and LAT** columns (`LONGITUDE`/`LATITUDE`/`X`/`Y` also
work). `MMSI`, `UTC_TIME`, `SHIPTYPE`, `HEADING` and `COURSE` each add something
to the sheet. Up to 2 GB, as `.csv`, `.csv.gz`, `.tsv` or `.parquet`.

> Uploads and charts are both **deleted after 2 days**. Keep your own copies.

---

## Two locks, not one

**Tailscale controls reach.** `100.121.170.8` is a tailnet address. A device that
is not on the tailnet cannot route to it at all — which is why this guide can be
public without exposing anything.

**Your service key controls use.** It is signed, expires, and is **tied to your
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
| `REPORTING.md` | how to report a problem so it gets fixed |

---

## Quick start, once connected

> *"What data do I have on GISviz?"* — your uploads, newest first

> *"What ports can you plot?"* — 54 Malaysian ports, by name

> *"Plot ds_a1b2c3d4e5f6 for Kuantan at 1:150,000, drawing number LL-DS-26-01-KU-001."*

> *"That's too dense — sample it to 10%."*

> *"Now at 600 dpi for the client."*

You will always be asked for a **title, subtitle and drawing number**. These name
a client on a document that leaves the building, so the tool refuses without them.

Charts are **deleted from the server after 2 days** — download anything you need
to keep.

---

## If something is wrong

The error messages are written to be acted on. Two worth recognising:

**"This service key is registered to … but the request came from …"** — you are on a
different machine or network than the key was issued for. Ask for a reissue,
quoting the key id in the message.

**"No dataset ds_… for this service key."** — you have not uploaded anything yet, or
that upload has expired after two days. Upload again.

**"This file cannot be plotted: no LON column…"** — your export is missing a
position column. The message names every spelling that would have worked.

**"Longitude and latitude look swapped."** — exactly that. Caught before it
wasted a render on a blank sheet.

---

## Something wrong? Tell Claude to report it

> *"That came out wrong — report it as a bug."*

Claude writes the report: what happened, what should have happened, how to
reproduce it, and the shape of the data it happened on. You get a pre-filled
GitHub link and press Submit. Nothing is filed without you seeing it.

**[REPORTING.md](REPORTING.md)** explains what makes a report the maintainer can
act on — and what must never go in a public issue.
