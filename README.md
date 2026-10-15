# Insight Board

Self-hosted deployment files and signed Android Player downloads for Insight Board.
Application source code is maintained privately.

## Install with Docker Compose

Requires Docker Engine with Docker Compose and a server with a domain pointing to it.
Allow inbound TCP ports 80 and 443 for the bundled HTTPS proxy.

```sh
git clone https://github.com/CuAuPro/insight-board-releases.git
cd insight-board-releases
cp .env.example .env
```

Edit `.env`: set your domain, matching HTTPS origins, email, database password,
and three different long random secrets. Generate each secret independently with
`openssl rand -hex 32`. Keep `SOURCE_ENCRYPTION_KEY` and `DEVICE_TOKEN_PEPPER`
stable across upgrades so saved connections and paired displays keep working.

```sh
docker compose pull
docker compose up -d
```

Open `https://YOUR_DOMAIN/setup` to create the first administrator and choose
single-workspace or multi-workspace mode. The browser player is at `/player`;
multi-workspace administration uses `/w/{workspaceSlug}`.
The API applies database migrations automatically at startup.

The self-hosted API and web images are pulled from GHCR. If pulling returns
`unauthorized`, the publisher must make those two container packages public.

## Android Player

Download the signed APK from [Releases](https://github.com/CuAuPro/insight-board-releases/releases/latest).
Install it on your Android TV / Google TV device and configure your installation's
player URL. Existing compatible installations can use **Player options > Update**.
Android controls installation confirmation; updates use the existing signing identity.
`SHA256SUMS` and `signing-certificate.txt` are provided with each release.

The default update feed is:

```text
https://github.com/CuAuPro/insight-board-releases/releases/latest/download/android-update.json
```

For an internal HTTPS mirror, copy the APK and manifest, update the manifest's
`apkUrl`, and set `ANDROID_UPDATE_MANIFEST_URL` in `.env`. Set it empty to disable
updates. Restart the API after changing the setting.

## Upgrade and backups

Before upgrading, back up PostgreSQL, the uploads volume, and `.env` including its
secrets. Keep the database, uploads, Redis and Caddy volumes across upgrades.

```sh
git pull --ff-only
docker compose pull
docker compose up -d
```

Review changes to `.env.example` and merge relevant settings into your own `.env`.
Do not run `docker compose down -v`: it deletes persistent volumes. Database
migrations may require restoring a backup to roll back an upgrade.

This repository distributes deployment configuration and binaries; it does not
grant an open-source license to the application.
