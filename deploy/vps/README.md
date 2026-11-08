# Umami on the Hostinger VPS

This fork of [umami-software/umami](https://github.com/umami-software/umami) runs as the shared analytics server for YottaBeacon projects.

- **VPS:** Hostinger KVM 2, `srv1282919.hstgr.cloud`, IP `72.62.133.124`
- **Docker project:** `umami` (Umami + Postgres 15 + Caddy for HTTPS)
- **Image:** `ghcr.io/yassine-bouassida/umami`, built by `.github/workflows/vps-image.yml`, which only runs when started by hand (tags `master` and `sha-<commit>`). Hostinger's Docker manager only pulls images, so it can't build from source itself.

## Configuration

`docker-compose.yml` holds no secrets. Copy `.env.example` to `.env` and fill it in; the values are passed to the Docker project as its environment. `.env` is git-ignored.

`UMAMI_HOST` lists every hostname Caddy serves. `analytics.yottabeacon.com` is the main one; `umami.72-62-133-124.sslip.io` resolves to the VPS without any DNS setup and works as a fallback. A hostname starts working once its DNS A record points at `72.62.133.124` (on Cloudflare, use "DNS only", not proxied, so Caddy can get the certificate); Caddy keeps retrying until then.

## MCP

`MCP_ENABLED=1` turns on Umami's read-only MCP endpoint at `https://analytics.yottabeacon.com/mcp`. It needs an API key from **Settings → API keys** in Umami, sent as `Authorization: Bearer umami_<key>`. For Claude Code:

```bash
claude mcp add --scope user --transport http umami https://analytics.yottabeacon.com/mcp --header "Authorization: Bearer umami_<key>"
```

## Deploying a change

1. Push the change to `master`.
2. Start the build and wait for it to finish: `gh workflow run vps-image.yml -R yassine-bouassida/umami --ref master`
3. Update the `umami` Docker project on the VPS so it pulls the new image (Hostinger hPanel → VPS → Docker Manager → Update, or the Hostinger API's update-project call). Volumes, and so the data, are kept.

## Syncing with upstream

```bash
git fetch upstream
git merge upstream/master
```

## Adding a site

In Umami, go to **Settings → Websites → Add website**, then put the tracking snippet it gives you in the site's `<head>`:

```html
<script defer src="https://<UMAMI_HOST>/script.js" data-website-id="<website-id>"></script>
```
