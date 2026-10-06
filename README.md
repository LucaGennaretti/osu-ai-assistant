# osu-ai-kb — Windows portable

A personal collector for osu!lazer: captures local scores, analyzes osu!standard replays, and synchronizes data with a Google Sheets knowledge base when configured.

This repository distributes the portable version for **Windows x64**. The application accesses the game's storage in read-only mode and keeps its own data in a separate SQLite database.

**Status: experimental.** Local collection, replay analysis, osu! API enrichment, Google synchronization, and daily rollover have been verified with real data. Some failure and compatibility scenarios still need testing; details are in the acceptance report included in the package.

## Download

Open this repository's **Releases** section and download the **`osu-ai-kb-win-x64.zip`** asset for the version you want.

Extract the entire ZIP to a writable folder. Do not run the program directly from the archive.

The package includes the executable, startup and configuration scripts, a configuration template, Apps Script code, and documentation. You do not need to install .NET, Node.js, Python, or Git to use it.

## Requirements

- Windows x64 with PowerShell.
- An osu!lazer installation with accessible local storage.
- Your osu! account's numeric ID.
- For Google synchronization: a Google account, a personal spreadsheet, and a bound Apps Script project.
- For online enrichment: credentials for your own osu! API v2 application.

Google Sheets and the osu! API are optional. Without Google configured, events remain in the local SQLite queue.

## First-time setup

1. In the extracted folder, copy `config/appsettings.example.json` to `config/appsettings.json`.
2. Open the new file and set `UserId` to your osu! account's numeric ID. You must replace the initial value of `0`.
3. Leave `LazerPath` set to `null` for automatic detection through `%APPDATA%\osu\storage.ini`. If necessary, set it to the directory containing `client.realm`.
4. Leave `GoogleWebAppUrl` set to `null` to start with local collection only.
5. Open PowerShell in the folder containing `osu-ai-kb.exe` and run:

   ```powershell
   .\osu-ai-kb.exe test-lazer --config .\config\appsettings.json
   .\osu-ai-kb.exe run --dry-run --config .\config\appsettings.json
   ```

   These commands check access to the game's data. The dry run does not capture data into the collector's database or send events to Google.

6. Double-click **`start-osu-ai-kb.cmd`** to start collection. The window stays open; press **Ctrl+C** to stop the program.

Do not run multiple collector instances against the same installation.

## Configuration

| Field | Purpose |
| --- | --- |
| `UserId` | Numeric ID of the account whose local scores you want to collect. |
| `LazerPath` | osu!lazer storage directory; `null` enables automatic detection. |
| `DatabasePath` | The collector's private database; default: `data/osu-ai-kb.db`. Relative paths depend on the startup directory. |
| `TimeZoneId` | Time zone used for the daily boundary and rollover; default: `Europe/Rome`. |
| `ReconciliationSeconds` | Reconciliation interval in seconds; default: `5`. |
| `GoogleWebAppUrl` | URL of your Apps Script Web App; `null` disables sending to Google. |

The included startup scripts set the extracted folder as the working directory.

## Google Sheets

Follow `docs/google-setup.md` in the extracted folder. It explains how to create the spreadsheet, copy `apps-script/Code.gs`, initialize the sheets, and deploy the Web App.

Use **`configure-google-secret.cmd`** to generate or reuse the Google ingest secret. The script saves it in your Windows user environment variables and copies it to the clipboard. Paste it into the **`OSU_KB_INGEST_SECRET`** Apps Script property, then clear the clipboard by copying something else.

Set the Web App URL in the `GoogleWebAppUrl` field of your local configuration. To check the connection, run this command from the extracted folder:

```powershell
powershell.exe -NoProfile -ExecutionPolicy RemoteSigned -File .\run-osu-ai-kb.ps1 test-google
```

The test sends an authenticated heartbeat to the spreadsheet. Collector events retain a stable request ID across delivery attempts.

Daily rollover aggregates previous days' data and removes the corresponding rows from the current-day sheets. Use a spreadsheet dedicated to the collector; local scores remain in the SQLite database.

## osu! API

Follow `docs/osu-api-setup.md` in the extracted folder to register your API application. Run **`configure-osu-api.cmd`** and enter the Client ID and Client Secret from the same application.

The configuration helpers save credentials in your Windows user environment variables. To check the configuration:

```powershell
powershell.exe -NoProfile -ExecutionPolicy RemoteSigned -File .\run-osu-ai-kb.ps1 test-osu-api
```

The collector uses the API to enrich local data. Scores are detected in osu!lazer's local storage.

## Status and backups

Run these commands from the extracted folder:

```powershell
powershell.exe -NoProfile -ExecutionPolicy RemoteSigned -File .\run-osu-ai-kb.ps1 status
.\osu-ai-kb.exe backup --config .\config\appsettings.json
```

With the default configuration, the database is in `data/`, backups are in `data/backups/`, and logs are in `logs/`. For custom database paths, backups are created in a `backups` subfolder next to the database.

Before updating, create a backup and stop the collector with Ctrl+C. Keep `config/appsettings.json`, `data/`, and any custom data locations; replace the program files with those from the new release. If the release updates Apps Script, also follow the update procedure in `docs/google-setup.md`.

## Credentials and personal data

| Variable | Purpose |
| --- | --- |
| `OSU_KB_INGEST_SECRET` | Authenticates submissions to your Google Web App. |
| `OSU_CLIENT_ID` | Identifies your osu! API application. |
| `OSU_CLIENT_SECRET` | The secret for your osu! API application. |

The startup scripts load these variables from your Windows user environment even if the current window has not inherited them yet. Do not put secrets in the JSON configuration, Apps Script code, repository, or distributed ZIP.

Your personal configuration contains your UserId, local paths, and any Google endpoint you configure. Databases, backups, and logs may contain personal data: keep these files in your own installation and check their contents before sharing them.

## Limitations and documentation

- Replay analysis is currently limited to osu!standard and the legacy input format. Metrics describe input and cursor movement; the collector does not invent hit judgments or hit error values.
- osu!lazer updates may require another storage compatibility check.
- Some failure scenarios, failed scores, custom or modified beatmaps, and installation on a clean Windows machine still need full verification.

The complete guides are in the package's `docs/` folder: `windows-install.md`, `google-setup.md`, `osu-api-setup.md`, `troubleshooting.md`, `osu-lazer-compatibility.md`, and `acceptance-report.md`.

Licenses, attributions, and notices for distributed dependencies are included in `licenses/`, with the inventory in `THIRD_PARTY_NOTICES.md` and `THIRD_PARTY_INVENTORY.json`.
