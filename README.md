# BLACKWATCH Community Suite 2.0

A self-hosted suite for a Raspberry Pi, Linux mini PC, and independent tablet/browser sessions. **This version includes the Global Watch globe.** It replaces the limited 1.0 feed-only distribution.

##Screenshot
<img width="2304" height="1440" alt="1000040240" src="https://github.com/user-attachments/assets/dcc1fca2-6f44-4c65-aecb-4ca59a1f74f4" />
<img width="1408" height="970" alt="6d685a55-1af4-4aab-ac39-feb7381f7928-1_all_8208" src="https://github.com/user-attachments/assets/4e5928c1-fbd1-45f5-b91e-669328c8caed" />
<img width="1586" height="992" alt="1000040264" src="https://github.com/user-attachments/assets/f5b9eed8-f603-4634-a06b-4f128471b6f3" />
<img width="2304" height="1440" alt="1000040254" src="https://github.com/user-attachments/assets/c290defc-6812-4973-b898-513239fa3515" />
<img width="2304" height="1440" alt="1000040252" src="https://github.com/user-attachments/assets/99575401-f12a-4181-8a25-2f92e4d4cbe5" />
<img width="1586" height="992" alt="1000040262" src="https://github.com/user-attachments/assets/93cda2c5-4f79-40bd-8253-9e35453fe4e9" />
<img width="2304" height="1440" alt="1000040260" src="https://github.com/user-attachments/assets/9ad25b70-d446-423d-bcfd-023984b952ca" />

## Included applications

| App | Included capabilities |
| --- | --- |
| Global Watch | Interactive Cesium globe, earthquakes, disaster feeds, approximate conflict-news locations, local weather, related YouTube videos and public camera discovery |
| Air Intel | Your readsb/dump1090 aircraft.json endpoint; green military-flagged aircraft; authorized JSON/GeoJSON dispatch reports with police/fire/EMS/road icons |
| Iran Watch | Public Sentinel-2 imagery, dated before/after views, Tehran and locations across Iran |
| Headlines | US politics, US unrest, Iran, European unrest and a Fox News headline ticker |

All four application servers are installed together. They share one browser address and setup screen. No existing BLACKWATCH installation is required. This distribution does not include the wall's LiDAR calibration, Wall 13 scenes, copyrighted music, or an Android APK. Tablets use their browser; sessions do not control another display.

## Requirements

- 64-bit Raspberry Pi OS or a supported 64-bit Debian/Ubuntu Linux system; Python 3.10+ recommended.
- Internet for installation, public feeds, Cesium, basemaps and satellite imagery.
- A modern browser with WebGL for the globe. Raspberry Pi 5 with 4 GB+ RAM recommended for imagery.
- Your own reachable ADS-B/dispatch endpoints if you want those local layers. No receiver required for the other three apps.
- `python3-venv` and pip. On Debian/Ubuntu/Raspberry Pi OS:

```sh
sudo apt update
sudo apt install -y python3 python3-venv python3-pip
```

## Install

Download `Install_BLACKWATCH_Suite.py` from this repository. Run as your normal user, **without sudo**:

```sh
python3 "$HOME/Downloads/Install_BLACKWATCH_Suite.py"
```

The installer creates `~/blackwatch-suite`, a private virtual environment, and `blackwatch-suite.service`. It installs Rasterio and Pillow for satellite imagery. It prints a randomly generated **setup password**; keep it.

Open `http://SERVER-LAN-IP:8810/` on a computer or tablet on the same trusted LAN. Select **Setup** and enter the printed password.

If port 8810 is already occupied by the old community version, use:

```sh
python3 "$HOME/Downloads/Install_BLACKWATCH_Suite.py" --port 8811
```

Then open port 8811. The old app is not removed or overwritten. Existing wall services are not modified.

For startup before login:

```sh
sudo loginctl enable-linger "$USER"
```

The service otherwise starts when your user session starts. Browser autostart/kiosk configuration is separate.

### Source installation

Extract `BLACKWATCH_Suite_2.0.zip`, enter its directory, and run `python3 install.py`. Other options: `--local-only` binds only to this computer; `--no-service` installs files/dependencies and prints a manual start command. Rerunning preserves saved settings and setup password.

## Setup: your infrastructure

1. Set your station name, home latitude/longitude, and local-feed poll interval.
2. **ADS-B:** enter the complete URL, including port and path. Example: `http://192.168.1.50:8080/data/aircraft.json`. Test connection before saving.
3. **Dispatch / 911:** enter an authorized JSON or GeoJSON URL. Select format, array path and field mappings. Dot paths support nested fields. A website address or a CAD vendor's IP alone is not a compatible feed; a provider API or adapter is required. Reports without coordinates stay in the list.
4. Enter optional authentication header/credential fields for either feed. Secrets stay on the server. Blank keeps the saved credential; use the clear checkbox to remove it.
5. **Global Watch media:** optionally enter your own YouTube Data API v3 key and Windy Webcams API key. Neither is required for the globe or public event feeds. Provider quotas/restrictions apply. The globe uses an imagery provider that needs no Cesium ion token.
6. Save. The APPS button returns to all four installed apps. Additional links are optional.

Saved settings and password hash: `~/.config/blackwatch-suite/`. They are not part of this repository. The dashboard is viewable without login on the trusted LAN; only configuration requires login. Do not expose it directly to the internet. For remote use, use a private VPN or an authenticated HTTPS gateway.

## Updates and data limitations

- Local ADS-B/dispatch polling: configurable 3–300 seconds; display checks every 3 seconds.
- Headlines: check the displayed source timestamps and health status.
- Satellite catalog: checked on demand and periodically while the imagery page is open, with a 30-minute cache. **This is not new imagery every 30 minutes.** Sentinel-2's 10 m imagery is dated, cloud-dependent, and not street-level or a live tactical feed.
- Conflict markers are approximate news place-name matches, not verified strike coordinates. Related videos may not show the selected incident. Dispatch reports are preliminary.
- Aircraft identification uses supplied receiver metadata. A military flag does not establish mission or passengers.
- Internet outages, provider changes and quotas can interrupt data. Missing/stale sources should not be interpreted as no activity.

## Service and troubleshooting

```sh
systemctl --user status blackwatch-suite --no-pager
journalctl --user -u blackwatch-suite -n 60 --no-pager
systemctl --user restart blackwatch-suite
```

Reset setup password:

```sh
~/blackwatch-suite/.venv/bin/python ~/blackwatch-suite/server.py --reset-password
systemctl --user restart blackwatch-suite
```

If installation reports a missing venv module, install the packages above. If no Rasterio wheel is available for your architecture, use a supported 64-bit OS. If the globe is blank, enable browser hardware acceleration/WebGL and check access to the Cesium CDN. If satellite tiles fail, check Earth Search/Sentinel COG connectivity and source capture dates/cloud cover. If a tablet cannot connect, check LAN connectivity, chosen port and host firewall.

## Verification and source

`python3 Install_BLACKWATCH_Suite.py --verify` verifies the bundled payload. `SHA256SUMS` covers the installer and source ZIP. The `tests` directory contains backend tests and an optional Playwright browser smoke test. Browser tests need Node/Playwright and a Chromium binary; they are not runtime dependencies.

Validation performed during packaging: backend feed/auth tests, mocked installer/rerun test, and browser navigation/setup tests for all four apps. External data availability is not guaranteed by these tests; no physical Pi boot test has been performed for this package.

Map, satellite, news and video sources retain their own rights and attribution. Leaflet's license is included in `NOTICE`. This release does not bundle personal API keys, receiver addresses, observation history, music, APK signing material, or credentials.
