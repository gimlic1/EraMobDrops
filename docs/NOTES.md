# FFXI Addon — EraMobDrops

> [!NOTE]
> **Quick Reference**
> - **Path:** `C:\Windower\addons\EraMobDrops\`
> - **Author / Version:** DiscipleOfEris / `1.6.0`
> - **Commands:** `//mobdrops`, `//drops`
> - **Load Status:** On-Demand
> - **Origin:** Third-Party (`https://github.com/DiscipleOfEris/EraMobDrops.git`, branch `master`)

SQLite-backed target drop-table overlay and Treasure Hunter drop-rate calculator built from FFEra database dumps.

## What the Code Does
On `load`, `EraMobDrops.lua` opens `mobs_drops.db` in read-only mode via `sqlite3` (generated offline by `parse_db.py` from `sql/mob_droplist.sql`, `sql/mob_groups.sql`, and `sql/mob_spawn_points.sql`).

Every frame in `prerender`, it checks the current target (`'st'`, `'t'`, or `player`). When `target_id`, `TH_lvl`, or `scroll` changes and `target.index < 1024` (a mob), `getMobInfo()` queries `mobs` by `mob_id` and `drops` by `drop_id`, splitting entries into steals (`drop_type == 2`), grouped drops (`drop_type == 1`, normalized to sum to 1000), and normal drops (`drop_type == 0`).

`updateInfo()` recalculates drop percentages using `applyTH(item_rate)` (adding `(TH_lvl - 2) * 0.01` above TH2 and applying `1 - (1 - rate)^2` at TH1 or `^3` at TH2+), formats level range and respawn time, paginates with `scroll`/`pageSize`, and renders the `texts` box with `▲`/`▼` scroll indicators. Mouse events on the hovered box adjust TH level (left-click `+1`, right/middle-click `-1`) and scroll pages (mouse wheel).

## Commands & Usage
- `//drops item <item_name>` — Query `mobs_drops.db` for all mobs/zones that drop or steal `<item_name>` (supports auto-translate).
- `//drops mob <mob_name>` — Query all mobs matching `<mob_name>` and print their zone, level range, and drop rates.
- `//drops th+` / `//drops th-` — Increment or decrement the Treasure Hunter level (`0` to `maxTH`, default `4`).
- `//drops page <n>` / `//drops pagesize <n>` — Change the number of displayed drop lines per page.

## Events, Packets & Technical Hooks
- **Windower Events:** `load`, `unload`, `mouse`, `prerender`, `addon command`
- **Packets:** None
- **Dependencies / Libraries:** `config`, `texts`, `tables`, `strings`, `resources`, `logger`, `sqlite3`

## Local Settings & Customizations
No `data/settings.xml` exists on disk; uses defaults (`pos=(0, 0)`, `Consolas` size `10`, `pageSize=10`, `maxTH=4`).

## Related Notes
- `FFXI Windower Overview`
- `FFXI Windower Addons Catalog`
