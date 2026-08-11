# GISviz — user handbook

You ask Claude for a chart in plain English and get back a finished
380 × 220 mm drawing sheet: title block, coordinate grid, scale bar, legend,
locator inset.

You never install the toolkit, never write a command, never handle a data file.

---

## 1. Setting up, once

**Two things from the maintainer:**

1. Access to the company tailnet (Tailscale). Install it, sign in.
2. Your personal **licence key** — one line beginning `gvz1.`

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

Restart Claude Desktop. That is the whole installation.

**Your key only works on your machine.** It is tied to your tailnet address, so
forwarding it to a colleague gives them nothing — they need their own. Charts you
produce, and any request you file, are attributable to you.

---

## 2. Check it works

> **"Check my GISviz access."**

You get your name, days remaining on your licence, and confirmation the server
can draw. If something is wrong it says exactly what — take that message to the
maintainer rather than guessing.

Then, before database access is arranged:

> **"Plot the GISviz demo data for Port Klang."**

The demo set is invented traffic for four ports. It lets you learn the tool
without spending a query. No vessel in it is real.

---

## 3. Saying where

Three ways. Use whichever matches how the job arrived.

**Name a port.** 54 Malaysian ports are built in — the gazetted port limits, so
the frame is the legal limit rather than a guess.

> *"Plot Kemaman for January."*  ·  *"…Langkawi…"*  ·  *"…Bintulu…"*

Ask **"what ports can you plot?"** for the full list. Spelling is forgiving:
*Klang*, *port klang* and *PORT KLANG* all work.

**Give an AOI.** When the job comes with a GeoJSON area, hand it over — paste it,
or point Claude at the file and let it pass the contents.

> *"Plot this AOI for March"* + the geojson

**Give coordinates.** If you already know the extent:

> *"Plot 100.76 to 101.77, 2.70 to 3.22."*

Whichever you use, the frame is **grown** to fill the sheet, never cropped — the
area you asked for is always fully inside the drawing. The result tells you which
area was used and where it came from, so there is no ambiguity.

---

## 4. Asking for a chart

Speak normally:

> **"Plot Port Klang traffic for 26–28 January at 1:300,000. Drawing number
> LL-DS-26-01-114-002, I'm the drafter, TC is checking. Leave the tugs out."**

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

## 5. What you can change

Say these in words; Claude maps them to the right setting.

| | say something like |
|---|---|
| **Where** | *"Kuantan"* · *"this AOI"* · coordinates |
| **When** | *"for January"* · *"26–28 Jan"* · *"first week of March"* |
| **Scale** | *"at 1:300,000"* |
| **Density** | *"sample it to 10%"* |
| **Vessel types** | *"cargo and tankers only"* · *"everything except tugs"* |
| **Tracks** | *"show the vessel tracks"* |
| **Resolution** | *"600 dpi for the client"* |
| **Document control** | revision, drafter, checker initials |

**Ask for the scale.** Say a round number and you get exactly it — the area is
worked backwards from it. If you do not ask, the scale is whatever the area
happens to give, which is rarely round and often gets sent back at review.

**Sample busy ports.** A whole month of Port Klang draws as a solid mass of
colour with nothing readable. At 10% you see the same traffic structure, legibly.
The sample is spread across the whole period, not just the first day.

**Density maps.** *"H3 density at resolutions 9 to 11, counting distinct
vessels."* Be explicit about what each hexagon counts — AIS pings, distinct
vessels, or distinct voyages. They give very different maps, and picking the
wrong one has caused rework before.

---

## 6. What you cannot change

The sheet itself. Size, layout, panel arrangement and the vessel-type legend are
fixed so that every sheet in a bundle matches, and every measurement matches the
approved ArcGIS layout.

Also fixed: anything the sheet works out for itself — the printed scale, the grid
resolution, the locator inset. These are statements about what was actually
drawn, and letting them be typed in by hand is how a sheet ends up claiming a
scale it does not have.

If you need something outside this, see §9 — do not work around it.

---

## 7. Getting the file

Every chart comes back two ways:

- **A preview in the chat** — downscaled so it loads. Fine for deciding.
- **A download link** — the full-resolution PNG. This is the deliverable.

The link works only for your licence. Sending it to a colleague will not work for
them.

> **Charts are deleted from the server after 2 days.** Download anything you
> intend to keep. The server is shared and its disk is finite.

---

## 8. When something goes wrong

**"…needs a valid licence key."** Yours is missing, mistyped or expired. Ask the
maintainer.

**"This licence is registered to … but the request came from …"** You are on a
different machine or network than the key was issued for. Ask for a reissue,
quoting the licence id.

**"No … credentials are set up for you."** Your database access has not been
provisioned yet. The message names exactly what the maintainer must run.

**"That query returned no AIS rows."** Your area and dates do not overlap any
data. Ask *"what data is there for this area in January?"* — that checks cheaply
before spending a render.

**"The server is busy."** One chart is drawn at a time. Wait, or ask for a lower
dpi.

**It is slow.** A first render is roughly 15–20 seconds plus the data pull.
Asking for exactly the same thing again is instant — it is cached.

**A sheet looks wrong.** Say so and ask for it again rather than editing the
image. If it is a tool problem, file it (§9).

---

## 9. Asking for something it cannot do

If Claude says the tool cannot do what you need, ask it to **file a request**. It
writes up what you wanted, why, and the exact settings you were using, and hands
you a pre-filled link — you press Submit.

Please use it rather than working around a limitation by hand. A workaround helps
you once; a filed request fixes it for everyone.

---

## 10. Before a chart goes to a client

- **Title, subtitle and drawing number** read correctly.
- **Scale** is the round number you asked for.
- Rendered at **600 dpi**, not the draft resolution.
- **Downloaded** — it disappears from the server in two days.

If you sampled the data, say so wherever the chart is presented. A 10% sample is
an honest picture of the traffic pattern; it is not a count of vessels.

---

## Quick reference

| ask for | example |
|---|---|
| check access | *"Check my GISviz access"* |
| list ports | *"What ports can you plot?"* |
| what data exists | *"What AIS is there for Kuantan in January?"* |
| a draft | *"Plot Kuantan for January, drawing LL-DS-26-01-KU-001"* |
| tidy a busy one | *"Same again at 10% sample"* |
| the final | *"Now at 600 dpi"* |
| a density map | *"H3 resolutions 9–11, distinct vessels"* |
| server busy? | *"What's the GISviz queue like?"* |
| something missing | *"File a request for …"* |
