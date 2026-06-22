# Warden

**A clean, fast, ad-free League of Legends companion for Windows.**

Warden is a native desktop app that helps you draft, build, and play better — with a real-time overlay, lobby scouting, build/rune import, and post-game coaching. No Overwolf, no ads, low footprint. It runs alongside the League client and only does work while you're playing.

> This repository hosts Warden's **published builds and auto-update manifests**. The Windows installer and a signed `latest.json` (which the app reads to update itself) live in [Releases](../../releases). Source is maintained privately.
>
> **Website:** https://khaosstudio.com

---

## What Warden does

### In champ select
- **One-click (or automatic) rune, item, and summoner-spell import** for Summoner's Rift and ARAM — straight into the League client.
- **Build recommendations sliced by patch, rank, and role**, each shown with its data source and a confidence / sample-size indicator instead of a bare "highest win rate."
- **Composition-aware builds** that adapt to the actual enemy team — more magic resist into heavy AP, armor into AD, anti-heal into sustain comps — with each adjustment explained.
- **Pick & matchup suggestions** with short, scannable "why" reasoning (matchup, comp, power spike), not a wall of stats.

### Pre-game lobby scouting
- Scout **all 10 players** in Summoner's Rift and ranked: rank, win rate, most-played champions, role, and recent form.
- **Plain-English reads** ("aggressive laner", "one-trick", "on a loss streak") that turn raw stats into something actionable.
- Respects streamer mode and Riot's name-visibility settings.

### Live in-game overlay
- **Objective timers** — dragon, baron, and herald reminders.
- **Jungle & scuttle respawn timers.**
- **Enemy summoner-spell tracking** and **ultimate availability** estimates.
- **Anti-heal / Grievous Wounds reminders** when the enemy comp calls for it.
- **Damage calculator** — hover a candidate item and see how your auto-attack damage changes, computed from your own live stats.
- **Live benchmarking** — your CS/min, gold/min, kill participation, and vision measured against your bracket, in real time.
- **Jungle pathing** suggestions and **item-value-difference** readouts.
- Minimap-anchored timer layout and per-portrait ult hints, all **toggleable and repositionable**.

### Arena (the flagship)
- A **live 8-player / 4-team board** — the mode every other companion treats as an afterthought.
- **Per-champion best augments** from real Arena match data, with explanations for *why* an augment is strong.
- An **offered-augment picker**: see the best of what you were actually offered this round.

### After the game
- **Plain-English coaching** — your top 3 things to improve, framed against your rank, plus CS benchmarks, vision gaps, and a gold-lead timeline.
- **Arena post-game** with placement, augment review, and damage share.
- **Historical coaching** for any past game in your match history.

### Profile & stats
- A polished **player profile**: rank progression, per-champion stats, strengths/weaknesses, challenges, recently-played-with, and full match history.
- A **stats hub** with per-mode champion stats (SR / ARAM / Arena), tier lists, team synergies, and objective stats.
- A **weekly graded dashboard** that trends your performance by category over time.
- Optional **pro / high-elo build reference** — what the pros actually build, clearly labeled as such.

---

## Why Warden

- **Ad-free, always.** No ads in the app, the overlay, the loading screen, or your lobby.
- **Native and standalone.** Built on Tauri — no Overwolf, no second-app layer, a small RAM/CPU footprint, and a live resource indicator so you can see it.
- **Only runs with League.** The app ties its lifecycle to the game and gets out of the way when you're done.
- **Honest by design.** Recommendations show their sample size and let you filter to your own rank bracket. When Warden doesn't have the data, it says so — it never fabricates a number.
- **Built for Arena.** Deep, real-data Arena support that no other companion matches.

## Safety & Riot ToS

Warden is **passive and read-only**. It surfaces information you can already see and never automates gameplay or grants an unfair advantage. The one exception is the rune/item import in champ select — a sanctioned action through Riot's official League client API, the same mechanism other approved tools use. There are no ads in any Riot property, and the core experience is free.

## Install & updates

1. Download the latest Windows installer from [Releases](../../releases).
2. Run it and launch Warden alongside the League client.
3. Warden keeps itself up to date automatically via signed update manifests — no manual reinstalls.
