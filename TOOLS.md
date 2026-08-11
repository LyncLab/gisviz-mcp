# GISviz MCP — tool reference

Generated from the live server on 11 August 2026. Do not edit by hand — regenerate with `studio/gen_tools_doc.py`.

Server: `gisviz` · 11 tools

You do not call these directly. Describe what you want in English and Claude picks the right one — the reference exists so you (and your AI) can see what is possible.

## `drop_dataset`

Delete one of the user's uploaded datasets from the server now, rather than waiting for it to expire. Use it when they ask, or when they are done with a large file.

| argument | type | required | what it does |
|---|---|---|---|
| `data_id` | string | **yes** | The dataset to delete, from list_datasets |

## `inspect_data`

Check an uploaded dataset before plotting it: how many rows, what area and time span it actually covers, which vessel types are present, and whether it has the columns a sheet needs. Cheap — call it before any expensive render.

| argument | type | required | what it does |
|---|---|---|---|
| `data_id` | string | **yes** | From list_datasets, e.g. 'ds_a1b2c3d4e5f6'. Use 'demo' for the bundled sample. |

## `list_datasets`

The user's uploaded AIS files, and how to upload another. Call this before any plot: every render needs a data_id from here. This server holds no database credentials — the user brings their own query results.

Takes no arguments.

## `list_layers`

List the chart layers bundled with this install — coastline, bathymetry, port limits, traffic separation scheme, subsea cables.

Takes no arguments.

## `list_places`

Every Malaysian port that can be plotted by name, from the gazetted port limits bundled with the toolkit. Use this when the user names a place, to confirm the spelling before rendering.

Takes no arguments.

## `list_presets`

Named areas of interest that are already set up, with the map scale each is normally drawn at. Prefer list_places — these are a small legacy subset.

Takes no arguments.

## `plot_density`

Produce an H3 hexagon density map. count_basis matters: 'pings' counts AIS messages, 'mmsi' counts distinct vessels, 'trajid' counts distinct voyages. Ask the user which they mean — delivering the wrong one has caused rework before.

| argument | type | required | what it does |
|---|---|---|---|
| `resolutions` | list of integer | **yes** | H3 resolutions, e.g. [9, 10, 11] |
| `aoi_geojson` | object | no | Your own area as GeoJSON — a Feature, a FeatureCollection or a bare geometry. Use this when the job comes with an AOI file. |
| `bbox` | array | no |  |
| `count_basis` | `pings` · `mmsi` · `trajid` | no | What each hexagon counts |
| `data_id` | string | no | Which of the user's uploaded datasets to plot, from list_datasets. Use 'demo' for the bundled synthetic sample. |
| `dpi` | integer (72–1200) | no |  |
| `end` | string | no |  |
| `exclude_ship_types` | array | no |  |
| `place` | string | no | A Malaysian port by name, e.g. 'Kuantan' or 'Port Klang'. The frame comes from that port's gazetted limit. See list_places. |
| `preset` | string | no |  |
| `ship_types` | array | no |  |
| `start` | string | no |  |

## `plot_sheet`

Produce the house drawing sheet: 380x220 mm, title block, coordinate grid, scale bar, legend. This is the normal deliverable. title, subtitle and drawing_no are required — ask the user for them, never guess, because they name the client on a document that leaves the building.

| argument | type | required | what it does |
|---|---|---|---|
| `drawing_no` | string | **yes** | Drawing number, e.g. LL-DS-26-01-XXX-XXX-XXX |
| `subtitle` | string | **yes** | WHERE — a location, not a date, e.g. 'Port Klang, Selangor, Malaysia' |
| `title` | string | **yes** | What the chart shows, e.g. 'AIS Traffic Overview' |
| `aoi_geojson` | object | no | Your own area as GeoJSON — a Feature, a FeatureCollection or a bare geometry. Use this when the job comes with an AOI file. |
| `bbox` | array | no | [lon_min, lon_max, lat_min, lat_max], if you already know the extent |
| `checker` | string | no | Checker's initials |
| `data_id` | string | no | Which of the user's uploaded datasets to plot, from list_datasets. Use 'demo' for the bundled synthetic sample. |
| `dpi` | integer (72–1200) | no | 200 to iterate (seconds), 600 or 1200 for a final (minutes, large file) |
| `drafter` | string | no | Drafter's initials |
| `end` | string | no | ISO date/time, exclusive |
| `exclude_ship_types` | array | no | Not supported on the drawing sheet — exclude them in your query, or use plot_density. |
| `place` | string | no | A Malaysian port by name, e.g. 'Kuantan' or 'Port Klang'. The frame comes from that port's gazetted limit. See list_places. |
| `preset` | string | no | A built-in preset name (legacy; prefer place) |
| `revision` | string | no | Revision code |
| `sample_percent` | number | no | Plot this percent of pings (1-100). Representative across the whole window, not the first N rows. |
| `scale` | string | no | Dictate a round map scale, e.g. '300,000'. Without it the scale is whatever the area gives, which review often rejects. |
| `ship_types` | array | no | Draw only this type. The sheet takes ONE of cargo, tanker, fishing, tug, passenger — for any other selection, filter it in the query you upload. |
| `start` | string | no | ISO date/time, inclusive |
| `with_tracks` | boolean | no | Draw vessel tracks under the chevrons |

## `request_feature`

Ask the maintainer for something this tool cannot do. Use it instead of improvising a workaround, so the gap gets fixed properly for everyone.

| argument | type | required | what it does |
|---|---|---|---|
| `want` | string | **yes** | What the user was trying to produce, in their words |
| `why` | string | **yes** | Who asked and what happens without it |
| `settings` | object | no | The settings they were using |

## `server_status`

Server load: how many renders are running and how many are queued. Check this if a render is taking longer than expected.

Takes no arguments.

## `whoami`

Who am I, what can I do, and when does my access expire. Call this first — everything else refuses without a valid service key.

Takes no arguments.

