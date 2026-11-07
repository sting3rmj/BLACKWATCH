# BLACKWATCH Community Feed Edition

A self-hosted, tablet-friendly dashboard for your own ADS-B receiver and local dispatch feeds.

© 2026 BLACKWATCH · Michael Jones

## Features

- Configurable ADS-B and dispatch feed URLs
- Connection testing and record previews
- JSON and GeoJSON dispatch field mapping
- Green military aircraft when identified by receiver flags
- Distinct Police, Fire, EMS, Roads, and Other symbols
- Password-protected settings
- Optional menu links to your other applications

## Requirements

- Raspberry Pi OS or Ubuntu/Debian Linux
- Python 3.9 or newer
- A reachable ADS-B and/or dispatch JSON feed
- A browser on the server or another device on the same LAN

## Install

Download `Install_BLACKWATCH_Community.py` from this repository.

Run as your normal user, without sudo:

```bash
python3 "$HOME/Downloads/Install_BLACKWATCH_Community.py"
```

Save the setup password printed by the installer.

## First-time setup

Find your server's LAN IP:

```bash
hostname -I
```

Open this address in your browser, replacing SERVER-IP:

```text
http://SERVER-IP:8810/setup
```

Sign in with the setup password, then:

1. Enter your display name and home coordinates.
2. Enter the full ADS-B feed URL.
3. Enter your dispatch feed URL and field mappings.
4. Add authentication headers if required.
5. Test each connection.
6. Save settings.

Example ADS-B URL:

```text
http://192.168.1.50:8080/data/aircraft.json
```

Your receiver may use a different port or path.

## Dispatch / 911 feeds

Use an authorized JSON or GeoJSON endpoint, not an HTML webpage.

For JSON, specify the path to the incident array:
- `incidents`
- `data.calls`
- Leave blank for a top-level array

Map the incident ID, title, category, coordinates, location, status, and timestamp.

For GeoJSON, select GeoJSON and use `features` as the array path. Point coordinates are read as longitude, latitude.

Reports without coordinates remain in the list. Provider subscriptions and access credentials are not included.

## Tablet access

On the same local network, open:

```text
http://SERVER-IP:8810/
```

Use the server's LAN IP—not `127.0.0.1` on the tablet. No APK is required.

## Start automatically

The installer creates a user service that starts at login.

To run after boot without logging in:

```bash
sudo loginctl enable-linger "$USER"
```

This starts the server; it does not automatically open a browser or configure a projector.

## Maintenance

Check service status:

```bash
systemctl --user status blackwatch-community
```

Restart:

```bash
systemctl --user restart blackwatch-community
```

View logs:

```bash
journalctl --user -u blackwatch-community -n 40
```

Reset the setup password:

```bash
python3 ~/blackwatch-community/server.py --reset-password
systemctl --user restart blackwatch-community
```

## Troubleshooting

- **Cannot connect:** check the server IP, service status, network and firewall.
- **Port already in use:** run the installer with `--port 8811`.
- **Feed test fails:** check the full URL, credentials and connectivity from the server.
- **No aircraft markers:** the receiver must provide valid coordinates and fresh `seen_pos` data.
- **Military aircraft not green:** the receiver must provide `military: true` or the military bit in `dbFlags`.
- **Dispatch records but no markers:** check coordinate field mappings.
- **Redirect error:** enter the feed's final endpoint URL.
- **No user systemd session:** install with `--no-service` and use the startup command it prints.

## Scope and privacy

This edition installs the ADS-B/dispatch dashboard and setup wizard. Global Watch, Iran Watch, Headlines, LiDAR and WALL 13 are separate applications and can be linked from the menu.

Configuration stays on the host in:

```text
~/.config/blackwatch-community/
```

Do not upload that directory or your API keys to GitHub.

Use this HTTP service on a trusted LAN. Do not expose port 8810 directly to the internet.

Map tiles require internet access. Dispatch reports may change, and military classifications reflect receiver flags rather than independent verification.
