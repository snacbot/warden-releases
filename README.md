# Mayhem champion augment ratings

## Context evidence and shadow evaluation (September 17 update)

The private importer now preserves ARAMKit stage rows separately, availability
metadata, its all-stage champion baseline, dataset version and generation time.
Every contribution retains its own pick rate. ARAMGG generation time and observed
source-family identity are retained; unknown lineage and popularity denominators
remain unknown. Known shared source families count once. Proxy outcome inputs are
excluded. Generation age is checked independently of retrieval age, so polling an
old dataset does not renew it. The cache namespace is now `context-evidence-v2`.
Legacy snapshots remain readable but cannot reconstruct per-source popularity.

The compatible v4 feed stays at `consensus/<champion>.json`. A separate strict v5
envelope is published at `consensus/context/<champion>.json`; it contains separately
keyed all-stage and stage-specific categorical snapshots. Neither envelope exports
outcome values or internal ranking scores. For v4 compatibility, the contribution
timestamp is conservatively the older of retrieval and known generation time.
Global tiers consume only all-stage ratings, so stage rows never add extra votes.

`getMayhemOfferRatings` can select a verified stage only when every offered ID has
a grade in that stage. Otherwise the whole offer uses the all-stage fallback.
The current OCR capture provides no verified stage, so the live overlay continues
using all-stage ratings and explains that scope in its localized tooltip. Level,
card position and owned-augment count are not stage evidence. Automatic stage
selection and alternate scoring have **not** been enabled. The v5 stage calculation
currently uses the existing formula for comparison, not a validated new model.

Developer commands:

```sh
pnpm diagnose:mayhem --champion=Blitzcrank --augment=1103 --patch=16.18
pnpm diagnose:mayhem --champion=Blitzcrank --augment=1103 --stage=2
pnpm ingest:mayhem --shadow
pnpm check:mayhem-experiments --shadow
pnpm diagnose:mayhem --shadow --champion=Blitzcrank --augment=1103 --stage=2
```

Shadow ingestion writes to `.ingest-cache/mayhem-shadow/consensus`; it does not
replace bundled inputs. Single-champion runs write their status under the cache
instead of overwriting the all-champion health report. Experiment results go to
`.ingest-cache/mayhem-experiments.json`. The normal polling job also runs the
experiment report against its new inputs, without changing scoring configuration.
Rich stage evidence stays in `.ingest-cache/mayhem/context`, outside installer
resources; bundled snapshots retain all-stage evidence. Publishing and diagnostics
join the rich snapshot only when champion, patch, poll time and all-stage rows
match exactly. Offline clients without context data use the all-stage fallback.

The report compares v4, performance-only, 25% popularity and provider-normalized
ranking over common same-rarity coverage. The latter requires each provider's own
popularity and two providers, and is explicitly experimental while denominator and
lineage remain unknown. It reports missing coverage, exact-tier agreement, ordinal
distance, S precision/recall on labeled rows, severe disagreements, pairwise order,
stage coverage, source-dropout changes and paired champion-bootstrap intervals.
Top-only labels cannot measure false S recommendations or pairwise ordering.

`docs/research/mayhem-benchmark-split.json` declares 15 calibration and 15 held-out
champions. Existing reference files are calibration evidence. Supply new dated
reference labels with `--reference=<file>` (the existing patch/rows/name/rarity/tier
format, all-stage only; explicit stage/build labels are rejected); compare providers
in separate runs. No held-out labels are fabricated,
and a missing benchmark never authorizes model promotion. Future-patch references
must match the observations; older references do not become current automatically.

The September 17 implementation did not add an unverified external feed, hardcode
Bread And Butter to S, or promote a scoring candidate. At stage 2 the current
formula still gives that augment B because of low popularity, while the
performance-only candidate gives S. Stage ingestion alone is not a complete fix.

Warden presents augment recommendations as **S, A, B, C, D, F**, with S highest.
Grades balance measured performance and pick popularity **for the same champion and rarity**. They
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
calculated. Performance and pick-rate midrank percentiles receive equal weight.
The combined score is rounded to six decimal places and ranked within the same
cohort before applying the letter bands. Equal evidence produces equal grades;
equal performance can be distinguished by popularity. Pick rate describes use,
not selection probability when offered, and can reflect availability and fashion.
It is therefore only one of two signals, not proof of strength. These are bounded reliability
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

The public format is version 4, method `warden-champion-recommendations-v1`.
Version 1, 2 and 3 public grades are rejected: they do not contain the private
observations needed to compute the new recommendation scores. The desktop
regrades current bundled private snapshots using its ARAM rarity catalog when
the new public feed is unavailable. This prevents old cached absolute grades
from overriding the new method. A missing catalog cannot fabricate a cohort;
valid version-4 remote grades remain usable without the local catalog.

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
The additional Ashe/Leona/Garen sample predates the recommendation-score change.
Both samples expose mostly top recommendations; they are not a complete
tier-scale or prediction-accuracy benchmark. Mobalytics' global expert grades
are contextual evidence, not champion-specific scoring inputs. No external
letter grades or champion-specific overrides are hardcoded into the formula.

The September 13 recommendation review adds a later Swain/Vayne/Nami sample.
See `docs/research/mayhem-recommendation-review-2026-09-13.md` for source methods,
before/after agreement and limitations. This is closer alignment with observed
recommendations, not a claim that most providers share our formula.
