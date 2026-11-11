---
section: Fixed
issues: [#5830]
---
- **A manual scan that stops before its scanners run fails the scans it returned instead of leaving them `running`** (#5830). `POST /api/v1/security/scan` commits one `running` row per scanner and returns their ids before the scan starts. When the scan then stopped early (the artifact's bytes could not be read, the artifact was deleted in between, a database error before or between scanners), those rows were never touched, so the UI showed a scan in progress until the stuck-scan janitor failed them after `STUCK_SCAN_THRESHOLD_SECS` (30 minutes by default) with "scan worker did not complete", and the real error was only in the server log. They now fail as soon as the scan returns, with `scan ended before this scanner ran: <error>`. Rows a scanner already finished are left as they are.
