# Test Project Outline – Module D – GIFTS Training Manager

## Competition time

3 hours

## Introduction

Build a **standalone frontend prototype** for **GIFTS** (Global Institute for Transferring Skills), an affiliated organization of the Human Resources Development Service of Korea (HRDKorea) that manages WorldSkills Korea, to coordinate preparation for web technologies training. Training activities focus on project tasks from **MITS** (Marketable IT Skills, https://skillsit.eu), a large collection of standardized test projects adapted from national and international skills competitions. The full product will include a REST API backend; this module is **client-side only**: load JSON on startup, persist changes in the browser, and support JSON export/import.

The **GIFTS Training Manager** helps training coordinators and competitors plan and monitor a **120-day** WorldSkills web technologies training programme. Using the application, they can:

- view and edit the training schedule on a **monthly calendar** — add, move, or remove events per day through a day-details modal;
- track **project tasks** and **evaluations** from the MITS catalog, plus custom **other** events (meetings, mental training, and similar);
- follow task progress on a **weekly Kanban board** (ToDo, InProgress, Done);
- review **statistics** — completed days, planned vs completed hours, scheduling gaps, and day-coverage warnings.

The application loads an initial schedule and the MITS task catalog from JSON files, **persists all changes in the browser**, and supports **export and import** of the schedule so plans can be backed up or shared. Built-in **business rules** enforce daily event limits, hour limits, and how often each MITS task may be scheduled.

**Technology:** HTML, CSS, JavaScript (framework permitted).

**In scope:** monthly calendar (modal editing), weekly Kanban, statistics, business-rule validation, `localStorage` (or equivalent), export/import, light/dark theme, fullscreen.

**Out of scope:** backend, API, database, authentication, multi-user, server-side validation.

**Data files and assets:** `assets/data/training.json` (initial schedule), `assets/data/collection.json` (MITS task catalog), SVG icons in `assets/svgs/`, GIFTS logo (`assets/images/gifts-logo.png`) and design files (`assets/design/`).

**Terms:** _Training period_ — preparation date range (e.g. 2026-02-01 – 2026-08-31). _Training day_ — a date in the `days` array (starts at 120 entries, max 122). _Project task_ / _evaluation_ — from `collection.json`. _Other event_ — custom event with `name`, `description`, `durationHours`.

## General Description of Project and Tasks

### Application layout

Single-page layout with four regions: **header**, **sidebar**, **work area**, and **footer**.

Design reference images in `assets/design/` may be used as a guide, but **pixel-perfect reproduction is not required**. You may apply your own visual design as long as it meets the functional and layout requirements in this document.

```
┌─────────────────────────────────────────────────────────────┐
│ Header: logo, title + control buttons                       │
├──────────┬──────────────────────────────────────────────────┤
│ Sidebar  │ Work area (calendar / kanban / statistics)       │
│          │                                                  │
├──────────┴──────────────────────────────────────────────────┤
│ Footer: HRDKorea copyright · Powered by MITS → skillsit.eu  │
└─────────────────────────────────────────────────────────────┘
```

#### Header

| Element                  | Description                                                                                                        |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------ |
| Application logo & title | GIFTS logo with Training Manager title                                                                             |
| Theme toggle             | Light / dark mode (persists after reload)                                                                          |
| Fullscreen               | Toggle fullscreen on / off                                                                                         |
| Export                   | Download the current training schedule as a JSON file (see [Export and import](#export-and-import))                |
| Import                   | Load a previously exported JSON file and replace the stored schedule (see [Export and import](#export-and-import)) |

#### Sidebar

Vertical navigation menu; the **active view is visually highlighted**.

| Menu item            | View                         |
| -------------------- | ---------------------------- |
| **Month** (Calendar) | Monthly calendar grid        |
| **Kanban**           | Weekly Kanban board          |
| **Statistics**       | Summary reports and warnings |

#### Work area

Main content beside the sidebar — content changes with the selected view:

- **Calendar:** monthly grid with previous / next month navigation
- **Kanban:** three columns for the selected week with week navigation
- **Statistics:** metrics and day-coverage warnings

Use provided SVG icons from `assets/svgs/` where appropriate (calendar, kanban, export, import, theme, navigation, and similar).

#### Footer

| Element     | Description                                                              |
| ----------- | ------------------------------------------------------------------------ |
| Copyright   | HRDKorea copyright notice                                                |
| Attribution | **Powered by MITS** — link to [https://skillsit.eu](https://skillsit.eu) |

#### Theme colors

Use the GIFTS logo palette as the application theme colors:

| Color  | Hex       | Suggested use                           |
| ------ | --------- | --------------------------------------- |
| Blue   | `#4462b1` | Primary UI accents, `task` badges/chips |
| Green  | `#59a149` | `evaluation` badges/chips               |
| Purple | `#6f2a90` | `other` badges/chips, secondary accents |

Apply consistently across the interface (header, sidebar, buttons, type indicators). Both light and dark themes should preserve recognizable brand colors.

## Requirements

Enforce all rules below on every relevant operation; show clear errors when a change is invalid.

### Business rules

1. Preparation covers **120 training days** within the defined **training period**.
2. Each day: **1–3 events**; each event **1–9 whole hours**; daily total **≤ 9 hours**.
3. Event types: **(a)** project task, **(b)** evaluation, **(c)** other.
4. Tasks and evaluations must be chosen **only** from `collection.json`.
5. Each MITS project task from `collection.json` may be scheduled **at most twice** as a `task` event and **at most twice** as an `evaluation` event. Each collection entry has a **unique** `name` that identifies it (used in place of an `id`); scheduled events reference their collection entry via that `name`. Scheduling the same collection task as the same event type a **third time** must be blocked.
6. `days` contains **only days with events**; deleting the last event **removes** the day.
7. `days` has **at most 122** entries. At 122 days, **no** new empty day may be added (no add/reschedule to a date not yet in `days`); events may still be added or moved to **existing** days.
8. **Duration** must be **whole hours** only (1–9); fractional or 0 hours are rejected.

### Persistence

- No saved browser state → load `assets/data/training.json` and `assets/data/collection.json`.
- All changes survive refresh and browser close.

### Export and import

Header buttons let users back up and restore the training schedule. `collection.json` is **not** included — it remains a read-only catalog loaded from `assets/data/`.

#### Export

- Triggered from the **Export** button in the header.
- Downloads the **current persisted schedule** as a JSON file (browser file save).
- JSON uses the same structure as `training.json`: `version`, `trainingPeriod`, and `days` with events (`type`, `name`, `durationHours`, `kanbanStatus`, and `description` for `other` events).
- Output is **human-readable** (formatted / pretty-printed).
- Event objects must **not** include runtime-only fields (e.g. UI-generated `id` values).

#### Import

- Triggered from the **Import** button in the header.
- Opens a file picker for a JSON file previously exported from the app (or compatible with the data model).
- Shows a **confirmation** dialog before overwriting the stored schedule; canceling leaves the current data unchanged.
- On confirm: replace persisted state with the imported data, enforce business rules, and **refresh all views** (calendar, Kanban, statistics).
- Reject invalid files with a clear error message (e.g. malformed JSON, missing required fields, or data that violates business rules).

### Data model

Initial `training.json` has **exactly 120** entries in `days`.

```json
{
  "version": 1,
  "trainingPeriod": { "start": "2026-02-01", "end": "2026-08-31" },
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

**Day:** `date` (ISO `YYYY-MM-DD` in period), `events` (1–3 items).

**All events:** `type` (`task` | `evaluation` | `other`), `name`, `durationHours` (1–9).

**`task` / `evaluation`:** `kanbanStatus` (`todo` | `inProgress` | `done`); `name` must match an entry in `collection.json` — each collection entry has a **unique** `name` that serves as its identifier. Do **not** store other MITS fields (`displayName`, `description`, `estTime`) in `training.json` — resolve them at runtime via that `name`.

**`other`:** `description` required; `name` is free text.

**Dynamic `days`:** remove empty days; add a day when scheduling to a date not yet in `days` if `days.length < 122`; exported JSON uses the same structure.

### Monthly calendar

**Monthly view only** (no separate week or day view). On startup, the calendar shows the **current month**. Navigate months within the training period. The full month grid may be shown; days **outside** the training period appear **dimmed**. Cells show a **summary** only — all viewing and editing happens in the **modal**.

#### Day status (visual only)

| Status     | Appearance                         |
| ---------- | ---------------------------------- |
| **Past**   | Dimmer / greyer background or text |
| **Today**  | Highlighted border                 |
| **Future** | Normal appearance                  |

#### Day cell content

On days **with events** (in the `days` array), each cell shows:

| Element           | Display                                                                                                                                                                          |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Sequence `#n`** | 1-based index in ascending date order in `days`; `#` prefix (e.g. `#42`); top-right corner; recalculated after add / delete / reschedule; **not shown** if the day has no events |
| **Name**          | If all events share the same `name`, or there is only one event: show `name`, max **1 lines** with `…` overflow. If names differ: **`Multiple different events`**                |
| **Total hours**   | Sum of `durationHours` (e.g. `6h`)                                                                                                                                               |
| **Type badges**   | `[T]` task, `[E]` evaluation, `[O]` other — only types present; color-coded per GIFTS theme colors (blue / green / purple)                                                       |

Examples: task + evaluation same name → `#42`, `ES2023 S17 - Module D`, `6h`, `[T]` `[E]`; mixed names → `Multiple different events` with hours and badges.

On **empty days** (no events): no summary, only the day number.

#### Day details modal

Opens on cell click.

**Display:** date, sequence number, and the full event list for that day. For each event show `type`, `name`, and `durationHours`. For `task` and `evaluation` events, also show `displayName` from `collection.json` and `kanbanStatus`. For `other` events, show `description`.

**Editing:**

- **Delete:** click the trash icon on an event.
- **Reschedule:** each event shows a date field (inline editing or date picker) and a **Move** button. Click **Move** to apply the new date. Target date must be within the training period. If the target date is not yet in `days`, add a new day entry when `days.length < 122`. At `days.length === 122`, rescheduling to a date **not already in `days`** is forbidden.
- **Add:** form within the modal.
  1. Select event `type`.
  2. **`task` or `evaluation`:** show a MITS task picker listing only `collection.json` entries with **fewer than 2** assignments of the selected type; each option displays the entry's `name`. Default duration: `estTime` from `collection.json` for `task`; **2 hours** for `evaluation` (editable).
  3. **`other`:** require `name`, `description`, and `duration` (whole hours, 1–9).
- Enforce per-day limits (**1–3** events, **≤ 9** hours total). `durationHours` must be a whole hour between **1** and **9**.
- Invalid changes are rejected with an error message in the modal.
- **Save** and **Cancel** buttons apply or discard edits.

Close via **Esc**, click outside, or a **Close** button — the calendar refreshes on close.

### Kanban board

**Tasks and evaluations only** — `other` events do **not** appear on the board.

**One week** (Monday–Sunday) at a time; **previous / next week** buttons; on startup select the current week or the week containing today.

| Column         | Maps to `kanbanStatus` |
| -------------- | ---------------------- |
| **ToDo**       | `todo`                 |
| **InProgress** | `inProgress`           |
| **Done**       | `done`                 |

Drag & drop between columns updates status and persists (including after page refresh).

#### Kanban card layout

Each card shows:

| Element       | Display                                                                                                                                                  |
| ------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Type chip** | Rounded badge at the top left: **`TASK`** — theme blue background, white uppercase text; **`EVALUATION`** — theme green background, white uppercase text |
| **Title**     | `displayName` from `collection.json` (bold), resolved at runtime via `name`                                                                              |
| **Name**      | Collection entry `name`                                                                                                                                  |
| **Details**   | Associated day date and duration, e.g. `2026-06-17 · 3h`                                                                                                 |

### Statistics

At minimum:

- number of completed training days (days where relevant work is marked done);
- total planned hours vs. completed hours;
- MITS scheduling status: list only `collection.json` entries with fewer than 2 `task` assignments or fewer than 2 `evaluation` assignments, showing the current count for each;
- additional useful metrics (e.g. breakdown by event type).

Data from persisted state; updates after import/export.

**Day-coverage warnings** (non-blocking; stay visible while the condition holds):

| Condition           | Message (example)                                                      |
| ------------------- | ---------------------------------------------------------------------- |
| `days.length < 120` | "Warning: only {n}/120 training days have active events."              |
| `days.length > 122` | "Warning: the number of days ({n}) exceeds the allowed maximum (122)." |

### Usability and non-functional

- **Light/dark theme** and **fullscreen** in the header; theme choice persists after reload.
- **Responsive layout:** main features usable at least on desktop resolution.
- User-friendly validation messages (e.g. "This day has reached the 9-hour limit", "Duration must be a whole hour between 1 and 9", "122 days already have events — cannot schedule to a new day").

## Assessment

Evaluation in **Google Chrome** — business rules, persistence, calendar, Kanban, statistics, and source code.

## Mark distribution

Total: **21 points**. See `marking/marking-scheme.json` for aspect-level detail.

| WSOS SECTION | Description                            | Points |
| ------------ | -------------------------------------- | ------ |
| 1            | Work organization and self-management  | 2.5    |
| 2            | Communication and interpersonal skills | 1.75   |
| 3            | Design Implementation                  | 5.0    |
| 4            | Front-End Development                  | 11.75  |
| **Total**    |                                        | **21** |
