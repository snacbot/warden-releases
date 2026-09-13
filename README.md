# Mayhem champion augment ratings

Warden presents augment recommendations as **S, A, B, C, D, F**, with S highest.
The grade uses a sample-reliability-weighted champion-specific source ratio: S at
55% or above, A at 52–55%, B at 50–52%, C at 48–50%, D at 45–48%, and F below
45%. Each interval includes its lower bound and excludes its upper bound.
These thresholds define Warden's grading method; individual win percentages
are not displayed or published. Missing data remains unrated, never F.
The pick overlay displays only the tier, with no source counts, popularity
captions or ingestion details. Missing champion ratings fall back to a fit
estimate or global catalog tier, explained in the tooltip; absent data is Unrated.
All offered cards remain visible.

These are Warden ratings. The presentation follows the tier badges visible on
Blitz's public Mayhem pages; Warden does not claim to reproduce Blitz's private
formula or to have Riot's approval. Riot's published League policy prohibits
augment win-rate displays. Product and feature review is still required:
https://developer.riotgames.com/docs/lol#game-policy

## Evidence and delivery

The maintainer job polls ARAMGG, ARAMKit and Mayhem:Meta every six hours. Inputs
must identify the requested champion and patch, declare champion-specific
statistics, and report at least 255 games. Repeated polls replace observations.
Source datasets can overlap, so their game counts are never added together.
Mirrors and sources that repeat global data on champion pages are excluded.

The private ingestion stage retains the source observations and their arithmetic
average for validation. Grade calculation weights each eligible observation by
`games / (games + 255)`, then divides the weighted ratio sum by the weight sum.
A minimum-size sample has weight 0.5; weight approaches 1 for large samples.
Thus a large provider has at most twice the influence of a minimum-size sample,
instead of equal influence or dominance from pooling game counts. One source
retains its original estimate. This is a bounded reliability heuristic, not a
calibrated confidence interval or a proven prediction-accuracy improvement.
Freshness, patch, champion, minimum sample and duplicate-source checks still apply.
The **public stage exports only grade, pick rate, patch, source
URLs, retrieval dates and per-source game counts**. It exports no win percentages,
wins/losses, raw source responses or precise performance scores. The desktop
ranks the public tiers and uses its champion-fit heuristic to break ties.
Local observed win/loss counts no longer determine Mayhem's displayed rating.

Run the polling stage with pnpm ingest:mayhem, then prepare publication with
pnpm exec tsx scripts/publish-mayhem-ratings.ts. The second command writes the
allowlisted public snapshots into .ingest-cache/public-mayhem. Publication
replaces the current JSON files on the mayhem-data branch of
snacbot/warden-releases. Git history is preserved. Application source and
credentials are never published.

The public shape remains version 2, with method `warden-sample-weighted-grades-v1`.
Existing `warden-win-ratio-grades-v2` public snapshots remain readable until refreshed;
private snapshots are regraded from their individual source observations.
Older numeric
version-1 ratings cannot distinguish the new grade boundaries and are rejected;
the new desktop instead recomputes from current bundled win-ratio data when
the new public feed is unavailable.

The desktop caches remote snapshots for six hours and retries outages after
15 minutes. It falls back to current bundled data, converts legacy private
snapshots to the same public rating shape, and rejects expired inputs or patches
older than its bundled game catalog. Inputs expire after seven days. An app
update is required to display the new format; older desktop releases may retain
old bundled displays until updated.

Tooltips explain the rating's scope without source counts or ingestion details.
They do not display source win rates or local win/loss scorelines.
Percentages in augment effect descriptions are ordinary game mechanics; the
restriction concerns outcome statistics.
