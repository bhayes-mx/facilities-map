# Global Facility Map — data guide

The map reads **`facility-data.csv`** from the same folder as the page, at load
time. To change what the map shows: edit the sheet, export as CSV, replace
`facility-data.csv` in this repo. No code change, no rebuild.

## Files that must ship together

```
index.html            the map (renamed from "Global Facility Map v3.dc.html")
support.js            runtime — required
facility-data.csv     the facility data
assets/               logos, icons, photos
_ds/                  design system (fonts, tokens, components)
```

The map also loads two libraries from CDN (d3, topojson) and world geometry from
jsDelivr. It must be served over **http/https** — opening `index.html` from the
filesystem (`file://`) will block the CSV fetch.

## If the CSV fails to load

The map falls back to a copy of the data baked into the page, so visitors always
see a working map. A small red **FALLBACK DATA** chip appears next to
"Our Global Network", and the browser console explains why. If you see that chip
on the live site, the CSV is missing, misnamed, or malformed.

## Columns

Header text is matched loosely — case, extra spaces, and anything in
parentheses are ignored, so `Intro Copy (Overview)` and `intro copy` both work.
Column **order does not matter**. Blank rows are skipped.

| Column | Required | Notes |
|---|---|---|
| ID | — | Stable handle. Leave blank for a new site and one is generated from Short Name. |
| Marker Group | — | Sites sharing a value collapse into one map dot with a "N services" badge. Blank = its own dot. |
| Sort Order | — | Left-rail order. Blank sorts alphabetically after the numbered ones. |
| Region | ✓ | One of `americas`, `europe`, `japan`, `india` — must match a filter chip. |
| Latitude / Longitude | ✓ | Decimal degrees. Non-numeric = row skipped. |
| Site Name | — | Interior page title. Falls back to Short Name if blank. |
| Short Name | ✓ | Left rail + map label. **Row is skipped without it.** |
| City / Country | — | Shown under Short Name. |
| Employees | — | Digits only; commas and text stripped. |
| Focus Tag | — | Shown next to City in the rail. |
| Core Modality | — | Short badge on the interior page. |
| Intro Headline | — | Interior page H1. |
| Intro Copy (Overview) | — | Overview tab paragraph. |
| Contact Headline | — | Connect tab heading. |
| Address | — | Connect tab. |
| Interior Page Image | — | Full URL, or a repo-relative path like `assets/photos/x.jpg`. |
| Virtual Tour Video URL | — | **Blank hides the Virtual Tour tab entirely.** Vimeo share links, Brightcove URLs, and direct `.mp4` all work — see below. |
| Pillar 1–4 | — | Must match a pillar name (see list). Unrecognized values are dropped. |
| Capability 1–6 | — | Name, Icon, and Detail per capability. A blank Name skips that slot. |
| Capability N Icon | — | One of `vial`, `molecule`, `benzene`, `microscope`, `lightbulb`, `bioreactor`. Anything else falls back to `molecule`. |
| Capability N Detail | — | Accordion paragraph. |

### Pillar names

`Scientific Innovation` · `Specialized Expertise` ·
`Rigorous Quality Commitment` · `Sustainability` · `Operational Excellence`

### Video URLs

Paste the link as-is; the map normalizes it.

- `https://vimeo.com/1174154077/cee1f72eaa?fl=ip` → converted to a player embed
- `players.brightcove.net/…` → `https://` added if missing
- `…/file.mp4` → played in a native video element

## Things that will silently break a row

- Short Name blank, or Latitude/Longitude not a number → **row skipped**
  (console warns with the row number)
- Region not matching a filter chip → the site won't appear under that filter
- A renamed header → that whole field goes blank for every site
- Two sites with the same ID → the second gets `-2` appended

After any edit, load the page and check the browser console. Skipped rows and
load failures both log there.
