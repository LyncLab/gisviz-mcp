# GISviz — user handbook

You ask Claude for a chart in plain English and get back a finished
380 × 220 mm drawing sheet: title block, coordinate grid, scale bar, legend,
locator inset.

You never install the toolkit and never write a plotting command.

**You bring the data.** GISviz draws; it does not query anything. You run your
own query — in BigQuery, in Postgres, wherever you normally work — export the
result as CSV or Parquet, and send that file to the server. What you can plot is
exactly what your query returned.

---

## 1. Setting up, once

**Two things from the maintainer:**

1. Access to the company tailnet (Tailscale). Install it, sign in.
2. Your personal **service key** — one line beginning `gvz1.`

Open Claude Desktop → Settings → Developer → Edit Config, and add:

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

Restart Claude Desktop. **That is the whole installation** — one key, in one
place. There is no file to create and nothing else to configure.

**Your key only works on your machine.** It is tied to your tailnet address, so
forwarding it to a colleague gives them nothing — they need their own. Charts you
produce, and any request you file, are attributable to you.

---

## 2. Check it works

> **"Check my GISviz access."**

You get your name, days remaining on your service key, and confirmation the server
can draw. If something is wrong it says exactly what — take that message to the
maintainer rather than guessing.

Then, before you upload anything:

> **"Plot the GISviz demo data for Port Klang."**

The demo set is invented traffic for four ports. It lets you learn the tool
without touching real data. No vessel in it is real.

---

## 3. Bringing your data

### What the file must contain

**Required:** a longitude and a latitude column. Any of these names work, in any
capitalisation:

| we need | your column may be called |
|---|---|
| longitude | `LON`, `LONGITUDE`, `X` |
| latitude | `LAT`, `LATITUDE`, `Y` |

**Strongly recommended** — each one adds something, and without it that part of
the sheet simply is not drawn:

| column | what it gives you |
|---|---|
| `MMSI` | vessel tracks, and counts of distinct vessels |
| `UTC_TIME` (or `TIMESTAMP`, `BASEDATETIME`) | the date caption, date filtering, track order |
| `SHIPTYPE` (or `VESSELTYPE`) | per-type colours and the vessel-type legend |
| `HEADING`, `COURSE` | which way each chevron points |

You do not need to rename anything. Export what your query gives you.

### Getting it there

Ask Claude for the command:

> **"I want to upload a file — give me the upload command."**

It hands you a ready-to-run line with a **one-time link** in it:

```bash
curl -T ~/Downloads/my_query.parquet http://100.121.170.8:8787/upload/tk_…
```

Windows PowerShell is the same with `curl.exe`. Run it, and it answers
immediately with a **data id** like `ds_a1b2c3d4e5f6`, plus what it found in the
file — row count, the area it covers, the dates, the vessel types.

**Read that.** It is the cheapest check you will get, and it catches the two
mistakes that otherwise produce a blank sheet: missing position columns, and
longitude and latitude the wrong way round.

Then just talk to Claude:

> **"Plot ds_a1b2c3d4e5f6 for Port Klang at 1:300,000, drawing LL-DS-26-01-114-002."**

Or let it find the file for you — **"what data do I have on GISviz?"** lists your
uploads.

**Your service key never appears in that command.** It stays in your Claude
config; the link carries a one-time ticket instead. The link works once and
expires in 45 minutes — ask for a fresh one whenever you need it.

**Claude cannot run the command for you**, because the file is on your machine
and not on the server's.

**Parquet is better than CSV** — smaller to send, faster to read, and the types
survive. Both work.

Limits: **2 GB** per file; `.csv`, `.csv.gz`, `.tsv`, `.parquet`.

> **Uploads are deleted after 2 days**, like charts. The server is a workbench,
> not storage. Keep your own copy.

---

## 4. Saying where

Three ways. Use whichever matches how the job arrived.

**Name a port.** 54 Malaysian ports are built in — the gazetted port limits, so
the frame is the legal limit rather than a guess.

> *"Plot Kemaman for January."*  ·  *"…Langkawi…"*  ·  *"…Bintulu…"*

Ask **"what ports can you plot?"** for the full list. Spelling is forgiving:
*Klang*, *port klang* and *PORT KLANG* all work.

**Give an AOI.** When the job comes with a GeoJSON area, hand it over — paste it,
or point Claude at the file and let it pass the contents.

> *"Plot this AOI for March"* + the geojson

**Give coordinates**, or just use your data's own extent:

> *"Plot 100.76 to 101.77, 2.70 to 3.22."*
> *"Plot everything that's in my file."*

Whichever you use, the frame is **grown** to fill the sheet, never cropped — the
area you asked for is always fully inside the drawing. The result tells you which
area was used and where it came from, so there is no ambiguity.

---

## 5. Asking for a chart

Speak normally:

> **"Plot ds_a1b2c3d4e5f6 for 26–28 January at 1:300,000. Drawing number
> LL-DS-26-01-114-002, I'm the drafter, TC is checking."**

Three things are always required, and Claude should not invent them:

| | |
|---|---|
| **Title** | what the chart shows — "AIS Traffic Overview" |
| **Subtitle** | *where* — a location, never a date |
| **Drawing number** | your job's number |

They name a client on a document that leaves the building. The tool refuses to
render without them, deliberately — a sheet carrying someone else's details is
worse than no sheet.

---

## 6. What you can change

Say these in words; Claude maps them to the right setting.

| | say something like |
|---|---|
| **Which data** | *"use ds_a1b2…"* · *"my latest upload"* |
| **Where** | *"Kuantan"* · *"this AOI"* · coordinates |
| **When** | *"for January"* · *"26–28 Jan"* · *"first week of March"* |
| **Scale** | *"at 1:300,000"* |
| **Density** | *"sample it to 10%"* |
| **Vessel types** | *"cargo only"* (see below) |
| **Tracks** | *"show the vessel tracks"* |
| **Resolution** | *"600 dpi for the client"* |
| **Document control** | revision, drafter, checker initials |

**Ask for the scale.** Say a round number and you get exactly it — the area is
worked backwards from it. If you do not ask, the scale is whatever the area
happens to give, which is rarely round and often gets sent back at review.

**Sample busy ports.** A whole month of Port Klang draws as a solid mass of
colour with nothing readable. At 10% you see the same traffic structure, legibly.
The sample is spread across the whole period, not just the first day.

**Vessel types on the drawing sheet: one at a time.** The sheet can draw *cargo*,
*tanker*, *fishing*, *tug* or *passenger* — a single one. It cannot do "cargo and
tankers" or "everything except tugs". Filter that in your query before you upload,
which is both faster and on the record. (Density maps have no such limit — they
take several types, and can exclude.)

**Density maps.** *"H3 density at resolutions 9 to 11, counting distinct
vessels."* Be explicit about what each hexagon counts — AIS pings, distinct
vessels, or distinct voyages. They give very different maps, and picking the
wrong one has caused rework before.

---

## 7. What you cannot change

The sheet itself. Size, layout, panel arrangement and the vessel-type legend are
fixed so that every sheet in a bundle matches, and every measurement matches the
approved ArcGIS layout.

Also fixed: anything the sheet works out for itself — the printed scale, the grid
resolution, the locator inset. These are statements about what was actually
drawn, and letting them be typed in by hand is how a sheet ends up claiming a
scale it does not have.

If you need something outside this, see §10 — do not work around it.

---

## 8. Getting the file

Every chart comes back two ways:

- **A preview in the chat** — downscaled so it loads. Fine for deciding.
- **A download link** — the full-resolution PNG. This is the deliverable. Click
  it; it opens.

The link **works once and expires in 30 minutes**. Ask for the chart again and
you get a fresh one — it costs nothing, because the file is already rendered.
That is also why forwarding the link to a colleague achieves nothing: by the
time they click it, it is spent.

> **Charts are deleted from the server after 2 days.** Download anything you
> intend to keep. The server is shared and its disk is finite.

---

## 9. When something goes wrong

**"…needs a valid service key."** Yours is missing, mistyped or expired. Ask the
maintainer.

**"This service key is registered to … but the request came from …"** You are on a
different machine or network than the key was issued for. Ask for a reissue,
quoting the key id.

**"Which dataset?" / "No dataset ds_… for this service key."** You have not uploaded
anything yet, or that upload has expired — they last two days. Upload again (§3),
or ask *"what data do I have on GISviz?"*

**"This file cannot be plotted: no LON column…"** Your export is missing a
position column. Add it to the query and upload again; the message names every
spelling that would have worked.

**"Longitude and latitude look swapped."** Exactly that. Check the column names
in your query — this would otherwise have rendered an empty sheet with no clue
why.

**HTTP 410 on upload.** That upload link has already been used, or it is more
than 45 minutes old. Ask Claude for the command again — a fresh one costs
nothing.

**HTTP 401 on upload.** You are using an old command that had a key in it. Ask
Claude for the upload command again; the current one carries a one-time link and
needs no key.

**HTTP 413 on upload.** Over 2 GB. Narrow the query.

**"The server is busy."** One chart is drawn at a time. Wait, or ask for a lower
dpi.

**It is slow.** A first render is roughly 15–25 seconds; a 600 dpi final takes
minutes. Asking for exactly the same thing again is instant — it is cached.

**A sheet looks wrong.** Say so and ask for it again rather than editing the
image. If it is a tool problem, file it (§10).

---

## 10. Reporting a problem, or asking for something it cannot do

**Just tell Claude.**

> *"That came out wrong — report it as a bug."*
> *"File a request for a wind-rose overlay."*

Claude writes the report itself: what happened, what should have happened, how to
reproduce it, and the shape of the data it happened on. It was there and it knows
exactly which settings it passed, which is why it writes a better report than you
can. You get a pre-filled GitHub link, read it over, and press Submit. Nothing is
filed without you seeing it first.

The issue tracker is
**[github.com/LyncLab/gisviz-mcp/issues](https://github.com/LyncLab/gisviz-mcp/issues)**,
and `REPORTING.md` there explains what makes a report actionable.

**It is public**, so before you submit, check the text does not contain a client's
name, a confidential drawing number, or your service key. The tool never puts any
of those in a report — but you might, and it is worth a glance.

Please report rather than working around a limitation by hand. A workaround helps
you once; a filed issue fixes it for everyone.

---

## 11. Before a chart goes to a client

- **Title, subtitle and drawing number** read correctly.
- **Scale** is the round number you asked for.
- Rendered at **600 dpi**, not the draft resolution.
- **Downloaded** — it disappears from the server in two days.
- The data behind it is the query **you** ran, and you can say what it was.

If you sampled the data, say so wherever the chart is presented. A 10% sample is
an honest picture of the traffic pattern; it is not a count of vessels.

---

## Quick reference

| ask for | example |
|---|---|
| check access | *"Check my GISviz access"* |
| upload | *"Give me the upload command"* → run the `curl -T …` line it hands you |
| what you have | *"What data do I have on GISviz?"* |
| check a file | *"What's in ds_a1b2c3d4e5f6?"* |
| list ports | *"What ports can you plot?"* |
| a draft | *"Plot ds_a1b2… for Kuantan, drawing LL-DS-26-01-KU-001"* |
| tidy a busy one | *"Same again at 10% sample"* |
| the final | *"Now at 600 dpi"* |
| a density map | *"H3 resolutions 9–11, distinct vessels"* |
| free up space | *"Delete ds_a1b2c3d4e5f6 from the server"* |
| server busy? | *"What's the GISviz queue like?"* |
| something missing | *"File a request for …"* |
