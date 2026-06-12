# Test Project Outline – Module D – WorldSkills Korea Training Manager

## Competition time

3 hours

## Introduction

This application is a **standalone frontend prototype** of a system that coordinates WorldSkills preparation for web developer competitors, built for **WorldSkills Korea**.

The final product will be a fullstack application with a REST API backend; in this module, however, only a **client-side prototype** must be built that:

- loads data from JSON files on startup;
- stores changes exclusively in the browser;
- can save and restore state via export and import.

The preparation process must be displayed and managed in a **visually clear** and **interactively manageable** way. On a single interface, the user can track 120 training days, schedule events, monitor progress, and view summary statistics.

## General Description of Project and Tasks

**Technology:** HTML, CSS, JavaScript (framework use permitted).

### In scope (this module)

- Monthly calendar view
- Day details in a modal (view and edit)
- Kanban board view (one week of tasks)
- Create, edit, delete, and reschedule events (in the day modal)
- Client-side enforcement of business rules
- `localStorage` (or equivalent browser persistence)
- JSON export and import
- Statistics page
- Light / dark theme, fullscreen mode

### Out of scope (later phase)

- Backend, REST API, database
- Multi-user operation, authentication
- Server-side validation and synchronization

### Glossary

| Term                | Meaning                                                                                                                         |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| **Training period** | The calendar interval of the full preparation phase (e.g. 2026-02-01 – 2026-08-31).                                             |
| **Training day**    | Any day currently in the `days` array (with at least one event). The array size changes dynamically (start: 120, maximum: 122). |
| **Training event**  | An activity scheduled on a day, with a defined duration.                                                                        |
| **Project task**    | A task from the MITS platform (`collection.json`).                                                                              |
| **Evaluation**      | Evaluation of a previously completed project task solution; also selectable from the MITS collection.                           |
| **Other event**     | A non-MITS event; fields: **name**, **description**, **duration** (e.g. theory session, meeting).                               |

### Data sources

| File                          | Role                                                             |
| ----------------------------- | ---------------------------------------------------------------- |
| `assets/data/training.json`   | Initial data: training days and their events.                    |
| `assets/data/collection.json` | MITS task catalog; used to select project tasks and evaluations. |
| Exported JSON                 | User-saved current state.                                        |

Provided assets include SVG icons under `assets/svgs/` for UI controls (calendar, kanban, export, import, theme, and similar).

## Requirements

The application must enforce business rules on **every relevant operation** and provide clear feedback on error.

### Business rules

1. Preparation covers the final intensive phase: **120 training days**.
2. Every training day falls within the **defined training period** (e.g. 2026-02-01 – 2026-08-31).
3. Each training day may have **minimum 1, maximum 3** events.
4. Every event **duration must be in whole hours**: **minimum 1 hour, maximum 9 hours**.
5. The total duration of all events on a day **must not exceed 9 hours** (sum of individual events).
6. Event types:
   - **(a)** training project task
   - **(b)** project task solution evaluation
   - **(c)** other event
7. Project tasks and evaluations may be selected **only from the MITS collection** (`collection.json`).
8. A MITS task (`name`) may appear on at most **2 training days**. On each such day it has exactly one `task` and one `evaluation` event (same `name`).
9. The `days` array contains **only days with at least one event**. If the last event on a day is deleted, the day **must be removed** from the `days` array.
10. The `days` array may contain **at most 122** entries. If there are already **122** days with events, a new event **cannot** be added to an empty day, and an event **cannot** be rescheduled to a day not yet in the `days` array. Events may still be added or rescheduled to existing days (subject to other rules).

### Initialization and persistence

- If there is **no** previously saved state in the browser, the application starts from `training.json`.
- All changes must be persisted; state must survive **page refresh** and **browser close**.
- **Export:** current state can be downloaded as a JSON file.
- **Import:** reading a JSON file **overwrites** the browser-stored state (confirmation recommended).

### Data model — `training.json`

The prototype assumes the initial `training.json` **`days` array contains exactly 120 entries**.

```json
{
  "version": 1,
  "trainingPeriod": {
    "start": "2026-02-01",
    "end": "2026-08-31"
  },
  "days": [
    {
      "date": "2026-02-03",
      "events": [
        {
          "type": "task",
          "name": "ES2023 S17 - Module D",
          "durationHours": 4,
          "kanbanStatus": "todo"
        },
        {
          "type": "evaluation",
          "name": "ES2023 S17 - Module D",
          "durationHours": 2,
          "kanbanStatus": "todo"
        }
      ]
    }
  ]
}
```

#### Day (`days[]`)

| Field    | Type     | Description                                         |
| -------- | -------- | --------------------------------------------------- |
| `date`   | `string` | ISO date (`YYYY-MM-DD`), within the training period |
| `events` | `array`  | Events for the day (1–3 items)                      |

#### Event types (`events[]`)

Common fields for all event types:

| Field           | Required | Description                             |
| --------------- | -------- | --------------------------------------- |
| `type`          | yes      | `"task"` \| `"evaluation"` \| `"other"` |
| `name`          | yes      | Event name (see per-type rules)         |
| `durationHours` | yes      | Whole hours, 1–9                        |

**`task`** and **`evaluation`** — additional field:

| Field          | Required | Description                            |
| -------------- | -------- | -------------------------------------- |
| `kanbanStatus` | yes      | `"todo"` \| `"inProgress"` \| `"done"` |

- `name`: the `name` field of the matching item in `collection.json` (MITS task reference).
- Other MITS task data (e.g `displayName`, `description`) is **not** stored in `training.json`; it is resolved at runtime from `collection.json` by `name`.

**`other`** — additional field:

| Field         | Required | Description       |
| ------------- | -------- | ----------------- |
| `description` | yes      | Event description |

- `name`: free text (e.g. "Theory session: REST API").

#### Runtime `days` array

After initialization, the `days` array **changes dynamically** relative to the starting state:

- empty days are removed (when the last event is deleted);
- rescheduling or adding an event may **add a new day** to the array if the target date is not yet present and `days.length < 122`;
- if `days.length === 122`, events may only be added or rescheduled to days already in the array.

Exported JSON follows the same structure.

### Application layout

The application uses a **single-page** layout with three main zones:

```
┌─────────────────────────────────────────────────────────────┐
│ Header: title + control buttons                             │
├──────────┬──────────────────────────────────────────────────┤
│ Sidebar  │ Work area                                        │
│          │ (calendar / kanban / statistics)                 │
└──────────┴──────────────────────────────────────────────────┘
```

#### Header

| Element               | Description                                   |
| --------------------- | --------------------------------------------- |
| **Application title** | E.g. "WorldSkills Korea Training Manager"     |
| **Theme toggle**      | Light / dark mode icon or switch              |
| **Fullscreen**        | Toggle fullscreen on / off                    |
| **Export**            | Download current state as JSON                |
| **Import**            | Load JSON state (overwrite with confirmation) |

#### Left sidebar

Navigation is a **vertical menu**; the active view is visually highlighted.

| Menu item            | View             |
| -------------------- | ---------------- |
| **Month** (Calendar) | Monthly calendar |
| **Kanban**           | Kanban board     |
| **Statistics**       | Statistics page  |

#### Work area

The **main content area** beside the sidebar, which changes according to the selected view:

- **Calendar:** monthly calendar grid with month navigation
- **Kanban:** weekly kanban board with week navigation
- **Statistics:** summary reports and warnings

### Monthly calendar view

The application contains **only a monthly calendar view** (no weekly or separate daily view). Within the training period, the user can **navigate between months** (previous / next month).

- Days of the month are visible within the training period (the full month grid may be shown; days outside the training period appear dimmed).
- Calendar cells show a **summary** view; details and editing **do not** happen in the calendar itself.

#### Day status — visual distinction

Each day's status is distinguished **visually only** (color, border, background); there is **no** "Today" or other text label.

| Status                           | Appearance (example)                     |
| -------------------------------- | ---------------------------------------- |
| **Past**                         | Dimmer / greyer cell background and text |
| **Today** (current calendar day) | Highlighted border                       |
| **Future**                       | Normal appearance                        |

#### Day cell content

On days with at least one event, the calendar cell displays:

| Element             | Display                                                                                                                                                                                                                                                                                                                                                                                                  |
| ------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Sequence number** | **Dynamically** calculated from the current `days` array: the day's **1-based sequence number** in ascending date order, with a **`#` prefix** (e.g. `#42`). Shown in the top-right corner of the cell. **Extra days** are included. If a day has **no events** (not in the `days` array), **no** sequence number is shown. Sequence numbers are **recalculated** after event delete / add / reschedule. |
| **Name**            | If all events share the same `name` (e.g. task + evaluation for the same module), **or** there is only **one** event: the event `name` value, **truncated to at most 2 lines** (overflow with `…`). If event `name` values **differ**: show **`Multiple different events`** instead.                                                                                                                     |
| **Total hours**     | Sum of `durationHours` for the day's events (e.g. `6h`).                                                                                                                                                                                                                                                                                                                                                 |
| **Type badges**     | Colored badges for event types present on the day: **`[T]`** = `task`, **`[E]`** = `evaluation`, **`[O]`** = `other`. Only types that actually occur are shown. Each badge is **color-coded** (e.g. `T` blue, `E` green, `O` purple — exact colors adapt to the theme).                                                                                                                                  |

Examples:

- One day: task + evaluation, both `"ES2023 S17 - Module D"` → sequence: `#42`, name: `ES2023 S17 - Module D`, hours: `6h`, badges: `[T]` `[E]`
- One day: single `other` event → name: the event `name` field, hours and `[O]` badge
- One day: different `name` values → name: `Multiple different events`, hours and badges still shown

On empty days (no events), the cell shows no summary, or only the day number / status indicator.

#### Day details — modal

Clicking a calendar cell opens a **modal** with that day's detailed data.

**Display in the modal:**

- the day's date and **dynamic sequence number** (e.g. `#42`);
- a detailed list of all events (type, `name`, `durationHours`, `kanbanStatus` — if relevant; `description` — for `other`);
- for MITS `task` / `evaluation` events, additional data resolved from `collection.json` (e.g. `displayName`) may be displayed.

**Editing in the modal** (calendar-related operations are permitted **only here**):

- **reschedule** an event to another day (target day must be within the training period; if not yet in `days`, a new entry is created — if `days.length < 122`),
- **delete** an event (if the last event is deleted, the day is removed from `days`),
- **add** a new event (same constraints).
- On reschedule and add: enforce 3 events / max. 9 hours per day; if `days.length === 122`, adding to or rescheduling to an empty (new) day is **forbidden**.
- When adding project tasks and evaluations, selection must be from the MITS collection.
- When adding other events: **name**, **description**, **duration** (whole hours, 1–9) are required.
- On invalid operations, the application **does not apply** the change and shows an error in the modal.

The modal can be **closed** (e.g. Esc, click outside, Close button); on close, the calendar refreshes to reflect the current state.

### Kanban board view

**Project task** and **evaluation** events appear in three columns. **Other events** do **not** appear on the Kanban board.

The Kanban shows **one week at a time**: only `task` and `evaluation` events from the currently selected week (Monday–Sunday interval) are visible. The user navigates with **previous / next week** buttons. On startup, the current week (or the week containing today) should be selected.

| Column         | Content                  |
| -------------- | ------------------------ |
| **ToDo**       | Tasks not yet completed  |
| **InProgress** | Tasks in progress        |
| **Done**       | Completed / closed tasks |

- Event cards can be moved between columns via **drag & drop**.
- Status changes are persisted.
- Each card shows at least the event type, `name`, duration, and the associated **day date**.

### Statistics page

Summary reports on preparation, at minimum:

- number of completed training days;
- total planned hours vs. completed hours;
- MITS task occurrence (how many training days each task appears on: 1× or 2× as task + evaluation pairs);
- additional metrics deemed useful (e.g. breakdown by event type).

Data is calculated from the current (persisted) state and updates after export/import.

#### Day coverage — warnings

The statistics page shows a **warning message** when:

| Condition           | Message (example)                                                      |
| ------------------- | ---------------------------------------------------------------------- |
| `days.length < 120` | "Warning: only {n}/120 training days have active events."              |
| `days.length > 122` | "Warning: the number of days ({n}) exceeds the allowed maximum (122)." |

Warnings do not block editing but remain visible while the condition holds. Coverage is checked from the current length of the `days` array — no separate date list storage is required.

### Display and usability

- **Light and dark theme** toggle in the header; choice persists after reload.
- **Fullscreen mode** can be toggled from the header.
- The application should be responsive: main features must be usable at least on desktop resolution.

### Non-functional requirements

- The application must work **offline** after initial load (no server required to run).
- Code should be readable; competitors may freely choose structure and tools.
- Validation errors must be **user-friendly** (e.g. "This day has reached the 9-hour limit", "Duration must be a whole hour between 1 and 9", "122 days already have events — cannot schedule to a new day").
- Import/export files must be human-readable, formatted JSON.

### Acceptance criteria

- [ ] Application starts from `training.json` and `collection.json` when no saved state exists.
- [ ] Application layout: header (title, theme, fullscreen, export, import), sidebar (Month, Kanban, Statistics), work area.
- [ ] Monthly calendar works; months are navigable; past/today/future days are visually distinct (no "Today" text label).
- [ ] Calendar cells show dynamic sequence number (`#42`), name (or `Multiple different events`), total hours, and colored `[T]`/`[E]`/`[O]` badges; days without events have no sequence number.
- [ ] Clicking a calendar cell opens a modal with day details.
- [ ] Adding, deleting, and rescheduling events in the modal works with 3 events / 9 hours rules.
- [ ] Duration must be whole hours only (1–9); fractional or 0 hours are rejected.
- [ ] A MITS task may appear on at most 2 training days (task + evaluation pair per day); a 3rd day is blocked.
- [ ] Kanban shows only project tasks and evaluations; one week at a time; week navigation works; drag & drop works; state persists after refresh.
- [ ] Export downloads, import overwrites saved state.
- [ ] Statistics page shows correct summaries.
- [ ] When the last event is deleted, the day is removed from `days`.
- [ ] If `days.length === 122`, adding to or rescheduling to empty days is blocked; existing days still allowed.
- [ ] Statistics page warns if `days.length < 120` or `days.length > 122`.
- [ ] Light/dark theme and fullscreen toggle work.

### Resolved design decisions

| Topic                  | Decision                                                                                                                                      |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| Duration unit          | Whole hours; minimum 1, maximum 9 hours per event.                                                                                            |
| Application layout     | Header + sidebar + work area.                                                                                                                 |
| Calendar view          | Monthly only; no weekly or separate daily view.                                                                                               |
| Today indicator        | Visual only (border/background); no "Today" text.                                                                                             |
| Day status             | Past: dimmer; today: highlighted; future: normal/slightly dim.                                                                                |
| Sequence number format | `#` prefix (e.g. `#42`).                                                                                                                      |
| Type display           | Colored `[T]` `[E]` `[O]` badges.                                                                                                             |
| Day details            | Shown in modal; editing happens there too.                                                                                                    |
| Kanban content         | Project tasks and evaluations only; other events excluded.                                                                                    |
| Kanban time range      | One week at a time; previous/next week navigation.                                                                                            |
| Event `name`           | Required for all types; MITS reference for tasks/evaluations, free text for `other`.                                                          |
| Month cell content     | `#n` sequence; only on days with events; `name` (max. 2 lines) or `Multiple different events`; total hours; colored `[T]`/`[E]`/`[O]` badges. |
| Other event fields     | `name`, `description`, `durationHours`.                                                                                                       |
| `days` array           | Dynamic; 120 entries on start; only days with events remain.                                                                                  |
| Day limit              | Max. 122; at 122 days, no add/reschedule to empty days.                                                                                       |
| Day coverage           | Statistics page warns if `days.length < 120` or `> 122`.                                                                                      |
| Event `id`             | Not in JSON; identification: `date` + array index.                                                                                            |

## Assessment

Evaluation will be conducted using **Google Chrome**. Business rule enforcement, data persistence, calendar and Kanban behaviour, and statistics accuracy will be reviewed in the running application and in the source code.

## Mark distribution

Points will be defined in `marking/marking-scheme.json` when the marking scheme is converted (Process 2). Preliminary WSOS sections:

| WSOS SECTION | Description                            | Points |
| ------------ | -------------------------------------- | ------ |
| 1            | Work organization and self-management  | TBD    |
| 2            | Communication and interpersonal skills | TBD    |
| 3            | Design Implementation                  | TBD    |
| 4            | Front-End Development                  | TBD    |
| 5            | Back-End Development                   | TBD    |
| **Total**    |                                        | TBD    |
