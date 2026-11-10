# EZY DNS Technitium DNS Server Bundle

Technitium DNS Server and the EZY DNS web UI running together in one Home
Assistant add-on, one container, the way they coexist on a Raspberry Pi.

- Technitium DNS Server is the primary service (DNS on port 53, web console on
  port 5380 by default).
- EZY DNS is the companion web UI (default port 5555). It talks to Technitium
  over loopback inside the container and reads Technitium's query-logs database
  off the shared filesystem, read-only.

Both are supervised by s6-overlay. This is a private, pre-built add-on: Home
Assistant pulls a finished image from GHCR rather than building it on the box.

## One-time setup: GHCR credential on the Home Assistant box

The bundle image is private, so Supervisor needs a read-only credential to pull
it. Do this once before installing.

1. On GitHub, create a personal access token with read-only package scope:
   - Classic token: scope `read:packages`, or
   - Fine-grained token: read-only access to the bundle package.
2. In Home Assistant, go to **Settings -> Add-ons -> Add-on Store**, open the
   three-dots menu, choose **Registries**, and add:
   - Registry: `ghcr.io`
   - Username: the GitHub account the token belongs to
   - Password: the token from step 1
3. Add this repository as an add-on repository (three-dots menu -> Repositories):
   `https://github.com/jakeharvey05/ezy-dns-technitium-dns-server-bundle-working`

The add-on then appears in the store and can be installed.

## Install and first run

1. Install the add-on and start it.
2. Open the **Technitium web console** (`http://<ha-ip>:5380/`) and finish
   Technitium's own first-time setup (set the admin password).
3. In the Technitium console, create an **API token**
   (Administration -> Sessions, or the API token section), copy it.
4. In the add-on **Configuration** tab, paste the token into
   `technitium_api_token` and save, then restart the add-on. Until a token is
   set, EZY DNS runs with limited data (no API-backed views).
5. Open the **EZY DNS UI** by browsing to `http://<host-ip>:<ezy_dns_port>`
   (default `http://<host-ip>:5555/`). There is no "Open Web UI" button; under
   host networking you reach the UI directly on the host. On first run it shows
   a **create-admin page**: set and confirm a password to create the first EZY
   DNS admin account. After that it behaves normally with its own login.

### Query logs page

EZY DNS's query-logs view reads Technitium's **Query Logs (Sqlite)** database.
For data to appear, install the **Query Logs (Sqlite)** app in the Technitium
console (Apps -> Store). The database is created at
`/config/apps/Query Logs (Sqlite)/querylogs.db` and EZY DNS reads it read-only.
If the app is not installed, the query-logs page simply shows nothing; the rest
of EZY DNS works.

## Options

| Option | Default | Meaning |
|---|---|---|
| `web_service_local_addresses` | `0.0.0.0,::` | Addresses the Technitium console listens on. Set a specific LAN IP to publish it only there. |
| `web_service_http_port` | `5380` | Technitium HTTP console port. |
| `web_service_https_port` | `53443` | Technitium HTTPS console port. |
| `web_service_enable_https` | `false` | Serve the console over HTTPS too. |
| `ezy_dns_bind_address` | `0.0.0.0` | Address the EZY DNS UI listens on. Set a LAN IP to publish it only there. |
| `ezy_dns_port` | `5555` | Port for the EZY DNS UI. May be any port, including low ports such as 80, on any bind address, per site. There is no Open Web UI button; browse to the host IP and this port directly. |
| `technitium_api_token` | *(empty)* | API token EZY DNS uses to call Technitium. |

Host networking is required (a DNS server must bind port 53), so Home Assistant
cannot remap ports: whatever each service binds is exposed on the host
directly. Set the bind addresses above to control which interface each web UI is
reachable on. DNS listen endpoints, forwarders, blocking, zones, DoH/DoT/DoQ and
DHCP are all configured in the Technitium console and persist in `/config`.

## Data and persistence

- Technitium config, zones and installed apps (including the query-logs DB) live
  in `/config` (the add-on config dir), browsable and persistent.
- EZY DNS state (`app.db`, `avatars/`, `secret_key`) lives under `/data/ezy-dns`
  on the add-on's persistent volume.

Both survive restarts and add-on updates.

## Updates

The EZY DNS UI tracks the current live build. When a new EZY DNS image is
published, a daily workflow bumps this add-on and rebuilds its image, and Home
Assistant shows a normal "Update". The Technitium DNS Server version is pinned
and updated separately.
