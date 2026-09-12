# Mayhem champion augment ratings

Warden presents augment recommendations as **S, A, B, C, D, F**, with S highest.
The grade uses the existing champion-specific source win-ratio average: S at
55% or above, A at 52–55%, B at 50–52%, C at 48–50%, D at 45–48%, and F below
45%. Each interval includes its lower bound and excludes its upper bound.
These thresholds define Warden's grading method; individual win percentages
are not displayed or published. Missing data remains unrated, never F.
The overlay also shows champion-specific pick rate when available. Pick rate is
popularity, not the chance of winning or a measure of strength. Missing champion
ratings fall back to an explicitly labeled fit estimate or global catalog tier.
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

The private ingestion stage computes S–F grades from eligible source win-ratio
averages. The **public stage exports only grade, pick rate, patch, source
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

The public format is version 2 (warden-win-ratio-grades-v2). Older numeric
version-1 ratings cannot distinguish the new grade boundaries and are rejected;
the new desktop instead recomputes from current bundled win-ratio data when
the new public feed is unavailable.

The desktop caches remote snapshots for six hours and retries outages after
15 minutes. It falls back to current bundled data, converts legacy private
snapshots to the same public rating shape, and rejects expired inputs or patches
older than its bundled game catalog. Inputs expire after seven days. An app
update is required to display the new format; older desktop releases may retain
old bundled displays until updated.

Tooltips explain the historical rating and list the patch and source sample
counts. They do not display source win rates or local win/loss scorelines.
Percentages in augment effect descriptions are ordinary game mechanics; the
restriction concerns outcome statistics.
