# Changelog

Each entry records a bundle release. The EZY DNS web UI tracks the current live
build (`ghcr.io/jakeharvey05/ezy-dns:works`); the daily update workflow bumps
this add-on whenever that image changes so Home Assistant offers an update. The
Technitium DNS Server version is pinned in the Dockerfile.

## 0.1.0

**Initial release.**

- Technitium DNS Server (pinned 15.6.2) and the EZY DNS web UI in one add-on,
  one container, supervised by s6-overlay.
- EZY DNS reaches Technitium over loopback and reads the query-logs SQLite DB
  off the shared filesystem, read-only.
- Configurable exposure: Technitium console on its chosen bind address/port,
  EZY DNS UI on its chosen bind address (default port 5555).
