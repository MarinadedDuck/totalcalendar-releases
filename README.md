# totalcalendar-releases

The Sparkle update feed for TotalCalendar, served by GitHub Pages and read by the
app through `https://updates.total-calendar.com/appcast.xml` (the
`totalcalendar-distribution` Worker proxies this repo's `appcast.xml` and edge-caches
it for 5 minutes). Update zips are **not** kept here: they live in the Cloudflare R2
bucket `totalcalendar-downloads` under `updates/`, served by the same Worker at
`https://updates.total-calendar.com/updates/TotalCalendar-<version>.zip`.

| Path | What |
|---|---|
| `appcast.xml` | The live feed. Every `<item>` is tied to one uploaded zip by `length` and `sparkle:edSignature`. |
| `release-notes/<version>.html` | Cumulative notes, linked from each item's `sparkle:releaseNotesLink`. |

## Rules (PME's v1.03 incident, 2026-03-20)

- **Upload the zip to R2 first**, then add its item here, so the feed never names a
  zip that is not there.
- **Insert the new `<item>` at the top of `<channel>`. Never replace the file** with
  `generate_appcast`'s output, and never edit an existing item.
- **No deltas.**
- **Release notes are cumulative** in the newest version's file.
- `sparkle:version` (the build number) only ever goes up.

To stop a bad release: `git checkout <last-good-commit> -- appcast.xml`, commit,
push, and wait about two minutes for Pages plus five for the Worker's cache.

`scripts/build-release.sh` in the TotalCalendar repo builds the zip and prints the
item; the steps are in `docs/PHASE8_SPEC.md` §2 there.
