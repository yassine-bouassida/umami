# Umami on the Hostinger VPS

This fork of [umami-software/umami](https://github.com/umami-software/umami) runs as the shared analytics server for YottaBeacon projects.

- **VPS:** Hostinger KVM 2, `srv1282919.hstgr.cloud`, IP `72.62.133.124`
- **Docker project:** `umami` (Umami + Postgres 15 + Caddy for HTTPS)
- **Source:** the VPS builds the image from this fork's `UMAMI_GIT_REF` branch (default `master`)

## Configuration

`docker-compose.yml` holds no secrets. Copy `.env.example` to `.env` and fill it in; the values are passed to the Docker project as its environment. `.env` is git-ignored.

To move to a new hostname, point its DNS A record at `72.62.133.124` (on Cloudflare, use "DNS only", not proxied, so Caddy can get the certificate), then redeploy with the new `UMAMI_HOST`.

## Deploying a change

1. Commit and push the change to the branch named in `UMAMI_GIT_REF`.
2. Redeploy the `umami` Docker project on the VPS with this compose file and `.env` (Hostinger hPanel → VPS → Docker Manager, or the Hostinger API's create-project call, which replaces the existing project and keeps its volumes).

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
