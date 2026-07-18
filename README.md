# uptime-monitor

External uptime monitor for `GET https://glissandolabs.com/ready`, run on a GitHub
Actions schedule (~every 10 minutes; GitHub cron timing is approximate).

On any non-200, unexpected body, or timeout, the workflow POSTs an alert to a private
ntfy topic (the `NTFY_URL` Actions secret) and fails the run (which also triggers
GitHub's own failed-workflow email).

`keepalive.yml` commits a monthly heartbeat so GitHub does not auto-disable the
schedule after 60 days of repo inactivity.

Why this exists — and why it is not a Cloudflare Worker: the app's in-app alerting
cannot cover the app being down entirely, and a Cloudflare Worker cron cannot deliver
to ntfy.sh (persistent HTTP 522 — ntfy.sh refuses connections from Workers egress,
even with the `global_fetch_strictly_public` compatibility flag; verified 2026-07-17).
