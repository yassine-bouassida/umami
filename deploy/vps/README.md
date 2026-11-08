# Umami on the Hostinger VPS

This fork of [umami-software/umami](https://github.com/umami-software/umami) runs as the shared analytics server for YottaBeacon projects.

- **VPS:** Hostinger KVM 2, `srv1282919.hstgr.cloud`, IP `72.62.133.124`
- **Docker project:** `umami` (Umami + Postgres 15 + Caddy for HTTPS)
- **Image:** `ghcr.io/yassine-bouassida/umami`, built by `.github/workflows/vps-image.yml` on every push to `master` (tags `master` and `sha-<commit>`). Hostinger's Docker manager only pulls images, so it can't build from source itself.

## Configuration

`docker-compose.yml` holds no secrets. Copy `.env.example` to `.env` and fill it in; the values are passed to the Docker project as its environment. `.env` is git-ignored.

To move to a new hostname, point its DNS A record at `72.62.133.124` (on Cloudflare, use "DNS only", not proxied, so Caddy can get the certificate), then redeploy with the new `UMAMI_HOST`.

## Deploying a change

1. Push the change to `master` and wait for the **Build VPS image** workflow to finish.
2. Update the `umami` Docker project on the VPS so it pulls the new image (Hostinger hPanel → VPS → Docker Manager → Update, or the Hostinger API's update-project call). Volumes, and so the data, are kept.

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
