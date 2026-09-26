# Trace
Privacy-first Windows desktop activity visualiser. Trace runs quietly in the system tray, tracks how your mouse moves around your desktop, and turns it into a heatmap of where you actually work.
**Everything stays on your machine.** No keystrokes, no screenshots, no clipboard, no cloud, no accounts.
![Trace logo](Assets/trace-logo.png)
---
## Features
### Heatmap
- Density-based rendering with smoothing (not dots) at up to 1280 cells wide
- **Heat**, **Clicks**, **Combined** and **Ghost Desktop** modes
- Today / Yesterday / Last 7 days / Pick a day
- Per-monitor selection with mixed-DPI support
- Hourly timeline scrubber – drag to watch the day build up
- Live cursor indicator
- Export to PNG
### Desktop overlay
- Projects the heatmap over your real desktop
- Click-through – never captures mouse input
- Always on top of normal windows
- Floating control strip with opacity slider and Close button (or press **Esc**)
- Updates live while open
### Dashboard
- Active time, clicks, mouse distance, scroll notches, context switches
- Hourly activity timeline with peak hour
- Per-application usage with share bars
- View any archived day
### Activity
- Per-app breakdown: active time, share, clicks, switches, interactions
- Drill-down detail pane with a per-app heatmap
- Day stepper
### History
- Every recorded day, newest first
- Totals: days recorded, total/average active time, clicks, busiest day
- Open any day's Dashboard, Activity or Heatmap
### Settings
| Section | Options |
|---|---|
| Tracking | Start with Windows · Start tracking automatically · Cursor sample interval · Idle timeout |
| Privacy | Record app names · Record window titles (off by default) · Auto-delete after 7 / 30 / 90 days / Never |
| Heatmap | Intensity · Smoothing · Overlay opacity · Show clicks separately |
| Appearance | Dark · Light · Follow system |
| Data | Open data folder · Clear all data (with confirmation) |
### Tray
Open Trace · Pause Tracking · Resume Tracking · Show Today's Heatmap · Settings · Exit
Closing the window minimises to the tray.
---
## What Trace records
| Recorded | Never recorded |
|---|---|
| Cursor position (~every 100 ms) | Keystrokes |
| Mouse clicks and scroll | Passwords |
| Active application name (optional) | Screenshots or screen contents |
| Window titles (**off** by default) | Clipboard |
| Which monitor is in use | Anything sent to a server |
| Idle time and app switches | |
All data is stored in `%LocalAppData%\Trace\`:
```
trace.db        SQLite database
settings.json   your settings
trace.log       error log (only written if something goes wrong)
```
Delete the folder, or use **Settings → Clear data**, and it's gone.
---
## Requirements
- Windows 10 / 11
- [.NET 8 SDK](https://dotnet.microsoft.com/download/dotnet/8.0)
- No administrator rights needed
## Build & run
```bash
git clone <your-repo-url>
cd Trace
dotnet run -c Release
```
Or open `Trace.sln` in Visual Studio / Rider and press Run.
The built executable is at `bin\Release\net8.0-windows\Trace.exe`.
---
## Project layout
```
Trace/
├── Assets/        icon and logo
├── Converters/    XAML value converters
├── Database/      SQLite access (WAL, batched writes, UPSERTs)
├── Models/        plain data types and settings
├── Services/      tracking, statistics, heatmap rendering, overlay, theme, startup
├── Themes/        Palette.Dark / Palette.Light (swapped at runtime) + Controls
├── Utilities/     Win32 interop, monitor helpers, paths, logging
├── ViewModels/    one per page (CommunityToolkit.Mvvm)
├── Views/         XAML pages and overlay windows
├── App.xaml       application entry + resource dictionaries
└── MainWindow.xaml custom-chrome shell, sidebar, tray icon
```
## Tech
- C# / .NET 8 / WPF, MVVM via CommunityToolkit.Mvvm
- Microsoft.Data.Sqlite
- Hardcodet.NotifyIcon.Wpf for the tray icon
- Win32 low-level mouse hook, `GetLastInputInfo`, `EnumDisplayMonitors`, DWM attributes
- Per-Monitor V2 DPI aware
## Performance
- Cursor sampling on a background loop; UI and tracking are fully separated
- Writes are batched and flushed every 5 seconds
- Heatmaps render off the UI thread
- Typical footprint: low CPU, no noticeable input latency
---
## Licence
MIT – do what you like, just keep the notice.
