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

The analysis merges two data sources: frame-by-frame tracking data containing player and ball positions at each timestamp, and event data with discrete actions like passes, shots, and ball receipts. The tracking data enables spatial metrics like pitch control and average positions, while the event data provides context about actions and outcomes.

## Project Structure

```
post-match-report/
├── fra_bel_postmatch.ipynb   # Main analysis notebook
├── data/                      # Tracking and event data
├── figures/                   # Individual visualizations
└── match_report.pdf           # Final output
```

## Installation

```bash
pip install -r requirements.txt
```
