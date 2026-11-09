# PerformanceMonitor (PSDK plugin) — by ralandel

In-game performance overlay and CSV recorder for Pokémon SDK. **Only active in debug mode** (game launched from Studio / with `debug`). In release builds nothing is hooked.

## Install

1. Put `PerformanceMonitor.psdkplug` in the `scripts/` folder of your project.
2. Start the game: the PSDK plugin manager installs it automatically.

To uninstall, delete the `.psdkplug` file from `scripts/` and start the game again.

## Keys

| Key | Action |
|---|---|
| **F5** | Cycle the overlay: compact → detailed → hidden |
| **F6** | Set / clear a reference (average of the last 5 s). The detailed overlay then shows the gain (`vs ref: -3.20ms (-25%)`) and a blue line on the per-second graph |
| **F7** | Start / stop a CSV recording in `perf_logs/` (one line per second). A summary is printed in the console when it stops. Works even with the overlay hidden |

The overlay has its own viewport owned by `Graphics` (z 999 998, just below the F2 FPS counter), so it stays above every menu and survives scene changes.

## What is measured

- FPS, average / 99th percentile / worst frame time, number of frames above 25 ms
- Ruby time vs render + VSync time
- `Game_Map#update` and `Spriteset_Map#update` time
- Tilemap redraws per second and their average cost
- `Game_Map#refresh` per second (each one re-evaluates every event page), active parallel interpreters, events, character sprites
- Objects allocated per frame, minor / major GC per second
- Two graphs: frame time of the last ~4 seconds, average per second over the last ~4 minutes

Map hooks are installed on the first frame, so they wrap every `prepend` of your project: the measured times include all your additions.

## Notes

- With VSync on, "Render+VSync" includes the wait for the screen: a low Ruby time with ~16 ms of render means you have headroom.
- The overlay measures itself a little (graph redrawn every 4 frames). When comparing two CSV files, keep the same overlay mode, or hide it while recording.
- Recommended method: record 30 s on the same path of your heaviest map before and after each change, then compare the CSV files.
- Settings (keys, font, CSV format...) are in `PerformanceMonitor::Config` (`001 Config.rb`).
- The CSV uses `,` as separator and `.` as decimal mark. For a French Excel, set `CSV_SEPARATOR = ';'` and `CSV_DECIMAL_MARK = ','`.

