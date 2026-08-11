# Reporting a problem

**Short version: tell Claude what went wrong and ask it to report it.** It was
there, it knows the arguments it passed, and it will write the report and hand
you a link. You press Submit.

> *"That grid caption came out blank — report that as a bug."*

Claude calls `report_problem`, which fills in a GitHub issue with what happened,
what should have happened, how to reproduce it, and the shape of the data it
happened on. You get a link. Read it, press Submit. Nothing is filed without you
seeing it first.

If it is not broken but **missing** — you wanted something the tool cannot
express — say so and Claude calls `request_feature` instead.

Both go to this repository's [issue tracker](https://github.com/LyncLab/gisviz-mcp/issues).

---

## If you would rather write it yourself

Use the templates: **[New issue](https://github.com/LyncLab/gisviz-mcp/issues/new/choose)**.

---

## What makes a report the maintainer can act on

The maintainer cannot see your conversation, your screen, or your data. A report
that cannot be reproduced usually cannot be fixed. So:

**1. What happened, concretely.** Not "the chart looks wrong" — *"the scale bar
says 1:297,400 when I asked for 1:300,000"*. Quote any error message in full.

**2. What you expected instead.** Sometimes the tool is right and the
expectation is the interesting part. Say what you thought would happen.

**3. How to reproduce it**, as steps someone else can follow:

> 1. Upload a CSV with LON, LAT, UTC_TIME, SHIPTYPE
> 2. Ask for: plot_sheet, place = Kuantan, scale = 150,000, dpi = 600
> 3. The scale bar reads 1:148,000

Naming the **tool** and the **arguments** is the single most useful thing in a
report. Claude knows both — that is why it is better placed to write this than
you are.

**4. The data's shape.** Row count, extent, time span, which columns. Claude
attaches this automatically from `inspect_data` when you give it the `data_id`.

**5. Which chart.** The drawing number, if it got that far.

---

## What NOT to put in an issue

This tracker is **public**. Before you press Submit, check the pre-filled text
does not contain:

- **Your service key** — it starts `gvz1.`. The tool never puts it in a report;
  do not paste it in yourself. If one ever ends up in an issue, tell the
  maintainer so it can be reissued.
- **Client names or drawing numbers** for confidential jobs. Describe the job as
  "a client sheet" instead. The reproduction rarely depends on whose it is.
- **AIS records.** Row counts, bounding boxes and column names are enough, and
  are all the tool attaches. Never paste vessel positions.
- **Anything from a dataset you would not email to a stranger.**

If the problem genuinely cannot be described without confidential detail, say so
in the issue and send the detail to the maintainer directly.

---

## Things worth reporting

Anything that surprised you, but especially:

- A sheet that is **wrong** rather than ugly — a wrong scale, a wrong location, a
  missing layer, a caption that disagrees with the drawing.
- Anything that made you **doubt the output**. A chart you cannot trust is worse
  than no chart, and that judgement is yours to report.
- An error message you could not act on. "It said X and I had no idea what to do"
  is a real bug in the message, and worth its own issue.
- Something that worked yesterday and does not today.
- A step in the handbook that did not work as written.

Small annoyances count. A workaround helps you once; a filed issue fixes it for
everyone.

---

## What happens next

Issues are labelled `from-studio`. The maintainer works from that label. When a
fix lands, **you do not need to update anything** — the server is updated in one
place, and your next request uses the fixed version.
