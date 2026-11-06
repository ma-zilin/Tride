<h1 align="center">Tride</h1>

<p align="center">
  Track your time. Ride the tide.
</p>

<p align="center">
  English · <a href="README.zh-CN.md">简体中文</a>
</p>

<p align="center">
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-MIT-blue.svg" alt="License: MIT"></a>
  <img src="https://img.shields.io/badge/status-design%20stage-lightgrey.svg" alt="Status: design stage">
</p>

## Overview

Tride (循刻) is a personal planning, time-tracking and review app. It keeps todos, schedules, time logs and AI-assisted analysis on the same data, so you do not have to switch between several tools. The app is meant to stay small and focused, and it does not run a language model locally: cloud AI is planned through the official DeepSeek API. Tride is intended to be released as open source.

## Status

Design stage: this repository currently contains documentation only, and there is no runnable build yet.

## Planned Features

None of these are implemented yet. They describe the first-release scope and are not acceptance results.

**Plan and organize**

- Today overview: today's todos, upcoming schedule, current timer and quick add in one place.
- Lightweight GTD structure: inbox, projects, next actions, waiting for, someday/maybe, calendar and completed.
- Tags, complete/abandon/delete actions, and a completion rate based on finished versus abandoned items.
- Kanban and quadrant views over the same todo data; kanban columns can be customised.

**Schedule**

- Built-in calendar with day, three-day and week views, showing class schedule, both sources combined, or regular events only.
- Import a class schedule from WakeUp CSV exported by existing school tools, with a preview and validation step before import.
- Recurring events (daily or selected weekdays, with an end date) and multiple reminders per item.

**Time tracking**

- Category-based time logs with an optional linked task; chronological view, later edits and backfilled entries.
- Two exclusive timing modes: single activity and parallel activities, with an explicit choice when switching.
- Daily time distribution and statistics for a day, month, year or custom range.

**AI assistance**

- Query real data and create or adjust schedules through conversation; the first write of each operation type requires confirmation.
- Review time logs and completion status over a period and suggest next steps grounded in that data.
- Remember personal preferences only after confirmation, and undo the most recent batch of AI changes to todos and schedules.

**Data**

- Backup and restore.

## Screenshots

There are no screenshots yet, because the app has no runnable build. This section will show the today overview, the kanban and quadrant views, and the time log once they are available.

## Getting Started

Tride cannot be installed or run yet. The sections below record what is already decided and what is still open.

### Prerequisites

The target platform is Android, phone first. The minimum Android version, development environment and toolchain are still to be decided.

### Installation

There is no installable build yet.

### Configuration

DeepSeek is planned as the only cloud AI provider, using the official API with a key supplied by the user; no model runs locally. The exact configuration format is not decided yet. Never commit a real key; a placeholder would look like this:

```text
DEEPSEEK_API_KEY=<your-key>
```

### Database

The storage design has not been finalised. Schema and initialisation steps will be documented here when available.

## Usage

There is no usable version yet, and no usage instructions can be given. The main workflows will be documented once they exist.

## Documentation

| Document | Contents |
| --- | --- |
| [docs/产品构想.md](docs/产品构想.md) | Product vision, decisions and first-release scope (Chinese) |
| [docs/开发架构设计.md](docs/开发架构设计.md) | Development workflow, roles, verification and evidence rules (Chinese) |

Other files under `docs/` are internal working notes and are intentionally not listed here.

## Roadmap

Future directions, not commitments and without dates: bring the workflow to university students; desktop and cross-device sync; in-app academic calendar login; richer statistics and charts; external calendar integration.

## Contributing

Tride is at an early design stage. Contribution guidelines have not been defined, and contributions are not open yet.

## License

[MIT](LICENSE) © 2026 Ma Zilin

## Acknowledgements

The task workflow takes the Getting Things Done (GTD) method as a reference, and the timing interaction is informed by Simple Time Tracker. This project is not affiliated with either of them.
