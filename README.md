# Post-Match Report Generator

This project was developed in April 2025 as part of a technical interview with the Royal Belgian Football Association (RBFA). The goal was to create an automated post-match report that combines tracking and event data to analyze Belgium's performance, condensed into a single-page PDF.

## Match Analyzed

The report analyzes **France vs Belgium** from the UEFA Euro 2024 Round of 16, where France won 1-0 with an own goal by Vertonghen in the 85th minute.

## What the Report Shows

The final output is a one-page PDF divided into three main sections, each providing different tactical insights about Belgium's performance.

### Field Control

This section examines how Belgium controlled space throughout the match. A heatmap shows where Belgium players received the ball most frequently, revealing their preferred areas of possession. Next to it, a pitch control chart displays the percentage of the pitch dominated by each team over the 90 minutes. This metric is calculated using Voronoi tessellation: at each frame, the pitch is divided into regions based on which player is closest to each point, and the total area controlled by each team is computed.

### Defensive Behavior

The defensive analysis focuses on Belgium's shape when under pressure. The first visualization shows the average position of each Belgium player when France had the ball in Belgium's defensive half, with ellipses representing positional variance. This is calculated only until minute 63 (before the first substitution) to keep the analysis consistent. The second visualization examines counterpressing behavior: it tracks where Belgium players moved in the 2 seconds immediately after losing possession in the attacking half, showing their tendency to press centrally.

### Offensive Behavior

The offensive section breaks down Belgium's attacking patterns. A shot map displays all shots taken, with circle sizes proportional to xG values. A pass flow diagram divides the pitch into a grid and draws arrows between zones based on passing frequency, highlighting the team's main build-up routes. Finally, a visualization of passes into the penalty area shows where Belgium created danger.

## Technical Approach

The analysis merges two complementary data sources:

- **Tracking data** — frame-by-frame positional data (~25 fps) containing x/y coordinates for all 22 players and the ball throughout the match. This enables spatial metrics like pitch control via Voronoi tessellation, average defensive positions, and counterpress movement patterns.
- **Event data** — discrete match actions (passes, shots, ball receipts, etc.) enriched with StatsBomb-style attributes including xG values and pass end locations. This provides context about actions and outcomes.

Both datasets are stored as pandas DataFrames in pickle format. The tracking data follows [kloppy](https://github.com/PySport/kloppy)'s standardized column naming convention (`home_PlayerName_x`, `away_PlayerName_y`, etc.) with coordinates in the UEFA standard system (105×68 m, centered at the origin before coordinate shifting). The event data uses StatsBomb's 120×80 coordinate system and is rescaled internally by the notebook.

## Data Requirements

> **Note:** This notebook requires proprietary tracking and event data that is not publicly available. The `data/` folder is excluded from version control.

To run the notebook with your own data, you need two pickle files placed in the `data/` directory.

### 1. Tracking data

A pandas DataFrame with one row per frame (25 fps), containing:

| Column | Description |
|---|---|
| `period_id` | Match period (1 or 2) |
| `timestamp` | Frame timestamp as `timedelta` |
| `frame_id` | Frame index |
| `ball_state` | `"In"` or `"Dead"` |
| `ball_owning_team_id` | Possessing team name |
| `ball_x`, `ball_y`, `ball_z` | Ball coordinates (UEFA 105×68, centered at origin) |
| `home_PlayerName_x/y` | Home player coordinates, one column pair per player |
| `away_PlayerName_x/y` | Away player coordinates, one column pair per player |

The easiest way to produce this format is to load tracking data with [kloppy](https://github.com/PySport/kloppy) and export it via `dataset.to_pandas()`. Update the `TRACKING_DATA_FILE` path in the config cell at the top of the notebook.

### 2. Event data

A pandas DataFrame compatible with the StatsBomb event schema, requiring at minimum:

| Column | Description |
|---|---|
| `type` | Event type string (e.g. `"Pass"`, `"Shot"`, `"Ball Receipt*"`) |
| `possession_team` | Possessing team name |
| `location` | `[x, y]` in StatsBomb coordinates (120×80) |
| `pass_end_location` | `[x, y]` for pass destination |
| `shot_statsbomb_xg` | Expected goals value for shots |
| `shot_outcome` | Shot result string (e.g. `"Goal"`, `"Saved"`) |

Update the `EVENT_DATA_FILE` path in the config cell accordingly. StatsBomb open data can be loaded directly with [statsbombpy](https://github.com/statsbomb/statsbombpy) or [kloppy](https://github.com/PySport/kloppy), but note that **open data does not include tracking data** — sections relying on it (Defensive Shape, Counterpress, Pitch Control) require a proprietary tracking feed.

## Project Structure

```
post-match-report/
├── fra_bel_postmatch.ipynb   # Main analysis notebook
├── data/                      # Tracking and event data (not included)
├── figures/                   # Individual visualizations (generated)
├── match_report.pdf           # Final one-page PDF report
└── requirements.txt
```

## Installation

```bash
pip install -r requirements.txt
```
