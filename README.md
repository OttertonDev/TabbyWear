# TabbyWear

TabbyWear is a lightweight Wear OS companion app for [Tabby-Schedule](https://github.com/OttertonDev/Tabby-Schedule).  
It mirrors key Today widgets from the phone app onto the watch for quick, read-only status checks.

## Mirrored Widgets

| Widget | Data | Color |
|--------|------|-------|
| **Class Ring** | `classProgress: Float`, `classesLeft: Int`, `classTotal: Int` | Blue `#008CFF` / `#A6E1FF` |
| **Assignment Bar** | `homeworkDone: Int`, `homeworkTotal: Int` | Red `#DA6868` / `#F39292` |
| **Date Until** | `eventName: String`, `eventMillis: Long` | Green `#6FBC65` / `#BDEDB8` |

These are aligned with the corresponding Today widgets in Tabby-Schedule (`TodayRoute.kt`).

## Data Origin (from Tabby-Schedule)

- Class progress: `PlannerCalculations.classDayProgress()`
- Classes left / total: `TodayUiState.classesLeftToday`, `todayClasses.size`
- Homework done / total: `TodayUiState.homeworkCompleted`, `homeworkTotal` (Room / `SchoolDao`)
- Countdown event: `daysUntilEventName`, `daysUntilTargetMillis` (DataStore user prefs)

## Sync Overview

TabbyWear uses the **Wearable Data Layer API** (Bluetooth + Wi-Fi fallback handled by Google Play services).

- **Path:** `/tabbywear/today`
- **Payload:**
  - `classProgress: Float` (0.0–1.0)
  - `classesLeft: Int`
  - `classTotal: Int`
  - `homeworkDone: Int`
  - `homeworkTotal: Int`
  - `eventName: String` (`""` when unset)
  - `eventMillis: Long` (`0L` when unset)

### Sync Flow

Phone (Tabby-Schedule) pushes updates:

- on app open/resume
- on Room data changes (classes/tasks)
- on DataStore countdown preference changes
- periodically via WorkManager (every 15 minutes)

Watch (TabbyWear) listens for DataMap updates and renders the latest snapshot in the home UI.

## UI/Behavior Notes

- Show a **“waiting for phone”** state until first data arrives.
- If `eventMillis == 0L`, hide Date Until info or show `—`.
- Match colors/labels with Tabby-Schedule for visual consistency.
- Watch app is **read-only** (no data entry/editing).
- The overdue widget from phone is intentionally not shown on watch.
