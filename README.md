# EZY DNS Technitium DNS Server Bundle (install repository)

Add this repository as a custom add-on repository in Home Assistant to install
the **EZY DNS Technitium DNS Server Bundle**: Technitium DNS Server and the EZY
DNS web UI in one container.

This repository holds only the add-on definition that Home Assistant reads to
install the add-on. The image is **pre-built and private**, so the Home Assistant
box needs a read-only GHCR credential to pull it. See
[`bundle/DOCS.md`](bundle/DOCS.md) for the full setup and install steps.

The image is built and published from a separate private build repository; none
of the build recipe lives here.

## Install

In Home Assistant: **Settings -> Add-ons -> Add-on Store -> ⋮ -> Repositories**,
add this repository's URL, then follow [`bundle/DOCS.md`](bundle/DOCS.md)
(including the one-time GHCR credential under **⋮ -> Registries**).
