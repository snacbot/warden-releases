# Mayhem champion augment source averages

`pnpm ingest:mayhem` polls ARAMGG, ARAMKit, and Mayhem:Meta for every champion.
The **Mayhem source polling** GitHub Actions workflow runs every six hours, at
00:43, 06:43, 12:43, and 18:43 UTC, and can also be dispatched manually.
Use `pnpm ingest:mayhem --champion=Thresh` for a focused local diagnostic.

Each champion/augment gets the arithmetic mean of the eligible source win rates.
Each source gets one vote regardless of its sample size; overlapping match
populations are possible, so sample counts are retained separately and never
added together. This is a source average, not a pooled match win rate or a
prediction for the player's current build. Repeated polls replace observations;
they do not increase their weight.

Eligibility requires the requested champion, champion-specific win and pick
rates, at least 255 reported games, and a matching patch. Riot's 16.x API labels
and sites' 26.x display labels are normalized. The target is the newest patch
known to the local game catalog or ARAMKit's version manifest. Mayhem:Meta's
actual page title determines its patch: its server may ignore the requested
patch and return an older page. A single eligible source is explicitly labeled
as one source. Missing rows never become zeroes.

ARAMMayhem.net is excluded because it derives data from ARAMGG.
ARAMMayhem.com is excluded because its champion pages repeat global augment
win rates. PlayARAM is excluded pending a verified accessible public feed:
automated requests returned HTTP 403. No challenge bypass is attempted.

Sources:

- ARAMGG: https://aramgg.com/en/champion-stats/412
- ARAMKit manifest: https://data.aramkit.com/data/versions.json
- Mayhem:Meta: https://mayhemmeta.com/champions/thresh
- ARAMMayhem.net methodology: https://www.arammayhem.net/methodology/

Normalized last-successful source responses are cached under
`.ingest-cache/mayhem/`, including through Actions cache. Failed polls retain
their original timestamps; inputs older than seven days are excluded. Source
fetch timestamps mean last successful retrieval, not the age of individual
matches. The status report records failures, observed patches, and coverage at
`data/augments/thirdparty/consensus/status.json`. Partial source outages can
publish the remaining eligible data; a complete outage fails the job.

Per-champion snapshots in `data/augments/thirdparty/consensus/` include each
contributing rate, sample count, patch, source URL, and retrieval time. Writes
are atomic per file. The workflow copies only these public-source statistics
and this README to the `mayhem-data` branch of `snacbot/warden-releases`, using
the existing `RELEASES_REPO_TOKEN`. No application source or credentials are
published. The desktop agent reads those snapshots from that public data
branch, caching successful reads for six hours and retrying failures after
15 minutes. It falls back to recent bundled data when offline, revalidates the
average and timestamps on read, and refuses snapshots older than its bundled
game patch. The first app update is required to install this reader; subsequent
polls reach it without requiring a new Windows release. `WARDEN_MAYHEM_BASE_URL`
can override the snapshot directory for development. Other data transports are
unchanged. Tests use the local transport unless a mock URL is supplied.

The overlay displays one decimal place and the number of contributing sources.
Its tooltip includes the patch and individual sample counts. Existing locally
observed evidence retains its established precedence over external rates.
