# OraLab — a workbench for tuning Oracle SQL (free)

![.NET 11](https://img.shields.io/badge/.NET-11.0-512BD4)
![C# 15](https://img.shields.io/badge/C%23-15-239120)
![Blazor Hybrid](https://img.shields.io/badge/Blazor-Hybrid-5C2D91)
![Oracle 12.2+](https://img.shields.io/badge/Oracle-12.2%2B-C74634)
![Windows x64](https://img.shields.io/badge/Windows-x64-0078D6)

**[Download the latest release](https://github.com/Doni-Kim/oralab-release/releases/latest)** ·
[한국어 설명](README.ko.md) · [User guide (English)](OraLab_manual_en.html) · [사용 설명서 (한국어)](OraLab_manual_kr.html)

OraLab is a desktop workbench for tuning one SQL statement at a time. Paste the statement, press F8, and get its session
statistics, wait events, V$SQL figures and the actual execution plan in one run. Change the SQL, run it again in the next
tab, and put the two runs side by side. One window, one executable, nothing to install on the server and no Oracle client
on your PC.

## Screenshots

| A tab with the SQL and its bind variables | Summary of a run |
|---|---|
| ![Main window](screenshots/main.png) | ![Summary](screenshots/summary.png) |

| Session statistics (V$MYSTAT delta) | The plan it actually ran |
|---|---|
| ![Statistics](screenshots/statistics.png) | ![Plan](screenshots/plan.png) |

| Bind Variables — every set of bind values of the SQL_ID | SQL Monitor report of a parallel run |
|---|---|
| ![Bind Variables](screenshots/bind-variables.png) | ![SQL Monitor](screenshots/sql-monitor.png) |

| Objects — tables, indexes, predicate columns | Plan Fix — SQL Patch, baseline and SQL Profile script |
|---|---|
| ![Objects](screenshots/objects.png) | ![Plan Fix](screenshots/plan-fix.png) |

| Tuning Advisor — editable script and report | Compare — metrics per execution |
|---|---|
| ![Tuning Advisor](screenshots/tuning-advisor.png) | ![Compare metrics](screenshots/compare-metrics.png) |

| Compare — two plans in one table | SQLID Compare — two SQL_IDs from the cursor cache or AWR |
|---|---|
| ![Compare plan](screenshots/compare-plan.png) | ![SQLID Compare](screenshots/sqlid-compare.png) |

| SQLID → Tab, with the captured binds | Tasks |
|---|---|
| ![SQL_ID to tab](screenshots/sqlid-tab.png) | ![Tasks](screenshots/tasks.png) |

| SQLID History — the plans of a SQL_ID in AWR | SQLID History — the trend, line colour = plan |
|---|---|
| ![SQLID History](screenshots/sqlid-history.png) | ![SQLID History trend](screenshots/sqlid-history-trend.png) |

The screenshots show test tables on a local test database.

## Install

- Unzip, keep the folder together, and run `OraLab.exe` (single-file publish).
- No .NET install and no Oracle client needed — both are inside the executable.
- There is no configuration file. Connections are entered in the start window and kept, with everything else, in
  `OraLab.db` next to the executable (passwords are stored encrypted).

## What it does

- **Runs the SQL to the end, and measures it on the server** — your statement is wrapped in a trace script that fetches
  every row and prints the session statistic deltas (V$MYSTAT, PX slaves included), the wait events, the V$SQL figures and
  `DBMS_XPLAN.DISPLAY_CURSOR` with actual row counts, times and buffers. OraLab shows each part as a sortable table.
  The run ends with a ROLLBACK.
- **Bind variables** — declare them in a `<Variables>` block at the top of the tab and the SQL uses them as real binds,
  so bind peeking and cursor sharing behave as in production.
- **Session commands** — a `<Commands>` block (`ALTER SESSION …`, PL/SQL blocks) before the SQL runs before the test SQL, one after
  the SQL runs after the measuring — in the same session, and they stay with the result.
- **Steps as tabs** — every change gets its own tab with a memo and a note; tabs are grouped into tasks. Scripts,
  notes and results are saved automatically.
- **Compare** — two tabs, or two SQL_IDs from the cursor cache or AWR: metrics per execution, both plans merged into one
  table with what went down and up, waits, and statistics. Save the comparison as Excel (with a chart), PDF or HTML.
- **SQLID → Tab** — brings a statement from the cursor cache (or AWR) into a tab together with bind values: those of its
  slowest SQL Monitor run, else the cursor cache capture, else AWR.
- **Bind Variables** — every set of bind values of a SQL_ID (SQL Monitor runs, cursor cache, AWR) in a list; open it in a new tab as
  declared variables or as literals.
- **SQLID History** — what AWR kept about one SQL_ID, in a separate window: its plans with per-execution figures (best and
  worst marked), bind values per snapshot, the trend with plan changes, ASH, past SQL Monitor reports, the optimizer
  environment and an AWR SQL report — and Excel, PDF or HTML of it all. Plan Fix loads a plan from AWR as a baseline (text only).
- **Objects** — the tables of the executed plan, all their indexes (the ones the plan used are marked) and the statistics
  of the predicate columns: NDV, histogram, low and high values, compression, In-Memory, partitions.
- **Plan Fix** — a ready-to-review script that pins the plan of a tab with a SQL Patch, a SQL Plan Baseline or a SQL Profile
  (the profile also covers statements that differ only in literals).
  OraLab shows it; it never runs it.
- **Tuning Advisor** — runs the SQL Tuning Advisor on the cursor of a tab and keeps its report with the tab (Tuning Pack).
- **Connection** — switch to another server without restarting; tasks and tabs stay. The dot before the address turns red
  when the server cannot be reached.
- **Also** — ASH by plan line, SQL Monitor report, Query Block Registry, optimizer environment, plan line colours,
  Beautify (spaces and line breaks only), whole SQL*Plus-style scripts, Excel, PDF and HTML of a single tab, 12 themes.
- On **Exadata** the statistics add Smart Scan, Storage Index, HCC and Flash Cache items with a short assessment of each.
- **Sequence cache refills** (1.0.8) — line 34 of the statistics shows the time the statement spent refilling sequence
  caches. It appears only when it adds up to 1 ms or more — a sign of a small cache or `NOCACHE`.

The bundled user guide has the details, with screenshots, in English and Korean.

## Diagnostics and Tuning Pack

Features that need a pack licence are **off by default**. Each connection has two boxes, and OraLab reads nothing from a
pack that is not ticked — querying those views alone leaves a trace in `DBA_FEATURE_USAGE_STATISTICS`.

- **Diagnostics Pack licensed** — ASH by plan line in a tab run; AWR and ASH waits in SQLID Compare; AWR bind values in
  SQLID → Tab; SQLID History.
- **Tuning Pack licensed** — the SQL Monitor report of a tab run; SQL Monitor bind values in SQLID → Tab; the SQL Profile
  section of Plan Fix; the Tuning Advisor sub-tab; the SQL Monitor tab of SQLID History.

Even when ticked, nothing is read if the server's `control_management_pack_access` excludes the pack. One exception:
a tab into which you paste the whole trace script is your own text and runs as written — switch the two parts off in
the script there (user guide, chapter 13).

## Requirements and limits

- **Windows only** (x64). The UI is web-based (Blazor Hybrid), so it does not run on Linux or macOS.
- **Oracle 12.2 or later.** Checked on 12.2, 19c and 26ai.
- A tab that holds a single statement accepts SELECT and WITH only; DML, DDL and PL/SQL run as a script.
- The code is obfuscated with ConfuserEx — a free tool, so do not expect strong protection.

## Privileges

The account you connect with needs to read the performance views the trace script uses.

```sql
CREATE USER oralab IDENTIFIED BY "...";
GRANT CREATE SESSION TO oralab;
GRANT SELECT_CATALOG_ROLE TO oralab;                 -- V$ and GV$ views
GRANT EXECUTE ON SYS.DBMS_SQLTUNE TO oralab;         -- optional, Tuning Pack only: SQL Monitor report, Tuning Advisor
GRANT ADVISOR TO oralab;                             -- optional, Tuning Pack only: Tuning Advisor
GRANT EXECUTE ON SYS.DBMS_WORKLOAD_REPOSITORY TO oralab; -- optional, Diagnostics Pack only: AWR Report in SQLID History
```

Plus whatever the SQL you are tuning needs to read. *Export Base Script* writes the trace script to disk; its header lists
the grants view by view if you prefer not to use the role. A part without the privilege prints Oracle's error in its
place and the rest still appears.

## WebView2 Runtime

OraLab draws its window with the Microsoft Edge WebView2 Runtime. When the runtime is missing, OraLab says so at startup and exits.
It is built into Windows 11 and shipped to most Windows 10 machines; Windows Server often needs it installed separately —
use Microsoft's "Evergreen Standalone Installer" (`MicrosoftEdgeWebView2RuntimeInstallerX64.exe`) from
https://developer.microsoft.com/microsoft-edge/webview2/

## If something breaks

Errors are written to `OraLab.log` next to the executable (the file only appears when something went wrong).

- **Bugs and questions** — open an [issue](https://github.com/Doni-Kim/oralab-release/issues).
  Please do not attach the log or `OraLab.db` there: the log holds no passwords, but both can contain server addresses and SQL text.
