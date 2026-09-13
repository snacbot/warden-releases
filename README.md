# Mayhem champion augment ratings

Warden presents augment recommendations as **S, A, B, C, D, F**, with S highest.
Grades compare measured augments **for the same champion and rarity**. They
describe relative choices, so a champion with a lower overall result baseline
can still have S-tier augments. They do not represent outcome probabilities.
The bottom-to-top percentile bands are F below 5, D from 5, C from 15, B from 30,
A from 60 and S from 85. Each band includes its lower bound. Ties share their
midrank; a cohort of identical observations receives B rather than arbitrary
grades based on ID or order. Each rarity requires at least ten eligible
observations. Unknown catalog IDs and thinner cohorts produce no measured grade.
Individual win percentages are not displayed or published. Missing data is
never converted to F.
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
statistics, and report at least 255 games. ARAMGG rows must also identify the
same patch as their parent response; mixed-patch carryovers are excluded.
The ingestion cache namespace was reset to `verified-row-patch-v1` so old
unverified rows cannot return through the outage fallback. Repeated polls replace observations.
Source datasets can overlap, so their game counts are never added together.
Mirrors and sources that repeat global data on champion pages are excluded.

The private ingestion stage retains the source observations and their arithmetic
average for validation. Grade calculation weights each eligible observation by
`games / (games + 255)`, then divides the weighted ratio sum by the weight sum.
A minimum-size sample has weight 0.5; weight approaches 1 for large samples.
Thus a large provider has at most twice the influence of a minimum-size sample,
instead of equal influence or dominance from pooling game counts. One source
retains its original estimate at this stage. Before ranking, each estimate is
shrunk toward the median estimate for that champion/rarity using the mean of
its per-source reliability weights. Counts are never summed. Scores are rounded
to the input precision (four decimal places) before ties and percentiles are
calculated. Pick rate is not a scoring input. These are bounded reliability
heuristics, not calibrated confidence intervals or proven prediction accuracy.
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

The public format is version 3, method `warden-champion-rarity-percentiles-v1`.
Version 1 and 2 public grades are rejected: they do not contain the private
observations needed to convert absolute grades into relative ones. The desktop
regrades current bundled private snapshots using its ARAM rarity catalog when
the new public feed is unavailable. This prevents old cached absolute grades
from overriding the new method. A missing catalog cannot fabricate a cohort;
valid version-3 remote grades remain usable without the local catalog.

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

## Calibration checks

Run `pnpm exec tsx scripts/check-mayhem-calibration.ts` to compare current
patch-matched inputs against the dated reference observations in `docs/research/`.
The script records agreement with exposed METAsrc recommendations, old/new grades
and all-champion coverage in `.ingest-cache/mayhem-calibration-report.json`.
The additional Ashe/Leona/Garen sample was collected after the thresholds were
chosen. Both samples expose mostly top recommendations; they are not a complete
tier-scale or prediction-accuracy benchmark. Mobalytics' global expert grades
are contextual evidence, not champion-specific scoring inputs. No external
letter grades or champion-specific overrides are hardcoded into the formula.
