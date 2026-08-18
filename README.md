# battery-history

Historical battery usage for macOS, rendered as a self-contained HTML report.
No third-party dependencies (system `python3`, `pmset`, `system_profiler`).

## Usage

```
./battery-history            # build the report and open it in your browser
./battery-history --days 3   # limit to the last 3 days (default 30; 0 = all)
./battery-history --stdout   # write the HTML to stdout instead of opening it
./battery-history --json     # machine-readable dump
```

Run it straight from GitHub:

```
curl -fsSL https://raw.githubusercontent.com/petermiles/battery-history/main/battery-history | python3
```

When output is piped or redirected, the report goes to stdout so you can save it:

```
curl -fsSL https://raw.githubusercontent.com/petermiles/battery-history/main/battery-history | python3 - > battery.html
```

Put it on your `PATH` to call it by name:

```
cp battery-history ~/bin/ && export PATH="$HOME/bin:$PATH"
```

## What the report shows

- **Header cards** — current charge %, charging state, max capacity, cycle
  count, condition, connected adapter.
- **Charge chart** — charge % across the window, with a color band marking time
  on the adapter, on battery, and asleep (no data).
- **Per-day table** — time on battery, % drained, daily min/max charge.
- **Discharge sessions** — start, length, charge delta, drain rate, plus the
  average drain %/hr.

## Data sources and limits

- History comes from the `Using AC/Batt(Charge: N)` samples in `pmset -g log`.
  macOS retains roughly the last **7 days**, so that is the maximum depth.
- Health and adapter come from `system_profiler SPPowerDataType`.
- The log is sparse and event-driven. A gap longer than one hour between two
  samples means no power events (the machine was asleep or off), so that time
  is not counted as runtime and it ends the current session. This keeps per-day
  totals under 24 hours and avoids phantom multi-day battery sessions.
