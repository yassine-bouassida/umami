# Adding a site to Umami

Use as much of Umami as possible on every site. A snippet alone is not the job. Work through the whole list, and say plainly which items don't apply or couldn't be done, and why.

Umami: <https://analytics.yottabeacon.com>. YottaBeacon is the worked example (website ID `f0d9e42c-02b9-4ea7-8829-d219a740af76`, board `YottaBeacon overview`).

## In Umami (API key, or Settings in the web app)

1. **Website:** `POST /api/websites` with `name` and `domain`.
2. **Replays and heatmaps:** `POST /api/websites/<id>` with `replayConfig`:
   ```json
   { "replayEnabled": true, "heatmapEnabled": true, "sampleRate": 1, "heatmapSampleRate": 1, "maskLevel": "strict", "blockSelector": "[data-umami-block]" }
   ```
   Lower the sample rates on a high-traffic site, because recordings use the VPS disk.
3. **Goals:** `POST /api/websites/<id>/goals`, one per lead or conversion event and one per key page. Parameters: `{ "type": "event" | "path", "value": "..." }`.
4. **Funnels:** `POST /api/websites/<id>/funnels`, one per path to a conversion. Parameters: `{ "window": 60, "steps": [{ "type": "path" | "event", "value": "..." }] }`.
5. **Board:** `POST /api/boards` with `type: "website"`. Include metrics bar, goals, visitors chart, funnels, top pages, sources, channels, events, world map, countries, weekly traffic, devices and UTM.
6. **Annotations:** `POST /api/websites/<id>/annotations` for launches and campaigns, starting with the day tracking began.
7. **Segments and cohorts:** add them in the web app once there's enough traffic to compare groups.
8. **Links and pixels:** use tracked links for campaigns and pixels for email, when the project has either.
9. **Team and sharing:** a public share link exposes the stats, so only create one if the user asks.

Event names can be defined before the site sends them, so goals and funnels can be created first.

## In the site's code

1. **Tracker on every page**, with Web Vitals on:
   ```html
   <script defer src="https://analytics.yottabeacon.com/script.js" data-website-id="<id>" data-domains="<domain>" data-performance="true"></script>
   ```
2. **Recorder on every page**, for replays and heatmaps:
   ```html
   <script defer src="https://analytics.yottabeacon.com/recorder.js" data-website-id="<id>"></script>
   ```
3. **Custom events** with short kebab-case names, sent only on real success (after the server confirms, not on click): `umami.track('book-call', { service: 'seo' })`, or `data-umami-event="..."` on buttons. Cover every lead, signup and purchase, plus main calls to action and outbound links. Never put names, emails or message text in event data.
4. **Revenue:** if the site takes payments, send `umami.track('purchase', { revenue: 49, currency: 'USD' })`.
5. **Private areas:** add `data-umami-block` to anything recordings must never capture, such as chat panels, and to anything showing personal data.
6. **Campaigns:** use UTM tags on links the project shares.
7. **Privacy page:** name Umami, the VPS host, and each feature in use, including session recordings and heatmaps. Have the owner decide whether recordings need a consent banner where the site's visitors are.
8. **Content-Security-Policy:** if the site has one, allow `https://analytics.yottabeacon.com` in `script-src` and `connect-src`.

## Checks

After deploying, confirm: a visit in Realtime, each event firing, a replay under Replays, heatmap data, Web Vitals under Performance, and the goals and funnels counting on the board.
