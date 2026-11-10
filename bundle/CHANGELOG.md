# Changelog

Each entry records a bundle release. The EZY DNS web UI tracks the current live
build (`ghcr.io/jakeharvey05/ezy-dns:works`); the daily update workflow bumps
this add-on whenever that image changes so Home Assistant offers an update. The
Technitium DNS Server version is pinned in the Dockerfile.

## 0.2.1

- EZY DNS can now bind any port, including low ports such as 80: the Python
  interpreter is granted `cap_net_bind_service`, so the non-root `ezy` user can
  bind privileged ports without running as root. This fixes the crash loop when
  `ezy_dns_port` was set to 80.
- Removed the ports mapping and the Open Web UI button. Under host networking the
  EZY DNS UI is reached directly on the host; `ezy_dns_port` (default 5555) and
  `ezy_dns_bind_address` are the only controls, on any port and bind IP per site.

## 0.2.0

- Removed the `technitium_public_url` option; EZY DNS 0.48 serves the Technitium
  logo locally, so the browser-facing URL is no longer needed.

## 0.1.0

**Initial release.**

- Technitium DNS Server (pinned 15.6.2) and the EZY DNS web UI in one add-on,
  one container, supervised by s6-overlay.
- EZY DNS reaches Technitium over loopback and reads the query-logs SQLite DB
  off the shared filesystem, read-only.
- Configurable exposure: Technitium console on its chosen bind address/port,
  EZY DNS UI on its chosen bind address (default port 5555).
