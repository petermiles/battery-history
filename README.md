# battery-history

Historical battery usage for macOS, printed in the terminal. No third-party
dependencies (system `python3`, `pmset`, `system_profiler`).

## Usage

```
./battery-history            # full report
./battery-history --days 3   # limit to the last 3 days
./battery-history --sessions --limit 5   # only discharge sessions
./battery-history --daily    # only the per-day table
./battery-history --graph    # only the charge sparkline
./battery-history --json      # machine-readable dump
```

Put it on your `PATH` to call it by name:

```
cp battery-history ~/bin/ && export PATH="$HOME/bin:$PATH"
```

## What it reports

- **Header** — current charge %, charging state, max capacity, cycle count,
  condition, connected adapter.
- **Sparkline** — charge % across the window, with an `A`/`B` strip marking
  time on the adapter (`A`) vs on battery (`B`).
- **Per-day table** — time on battery, % drained, daily min/max charge.
- **Discharge sessions** — start, length, charge delta, drain rate, plus the
  average drain %/hr.

## Data sources and limits

- History comes from the `Using AC/Batt(Charge: N)` samples in `pmset -g log`.
  macOS retains roughly the last **7 days**, so that is the maximum depth.
- Health and adapter come from `system_profiler SPPowerDataType`.
- The log is sparse. Consecutive same-source samples separated by a long gap
  fold into one "session". A discharge session that shows a *positive* rate is
  such a gap artifact (the machine was really on the adapter through the gap),
  not a real multi-hour battery run. Genuine sessions have negative rates and
  drive the average.
