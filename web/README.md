# web/

Static artifacts for the public marketing site (https://zfolio.co.in), published
here so the site's browsers fetch them from jsdelivr's GitHub CDN instead of
from the zenserve VM.

zenserve is a single free-tier GCE instance. Every image on the marketing page
used to be served by that VM off `static/site/img/` via the `/img` mount, so a
single page view cost it one request per screenshot, logo and broker mark — by
far the noisiest thing in its access log, and all of it for bytes that change
maybe twice a year. Nothing here is dynamic, so none of it belongs on the API.

Served as:

    https://cdn.jsdelivr.net/gh/qxakshat/zenpub@main/web/img/<file>

The `@main` ref is mutable, and jsdelivr caches a mutable ref at its edge for
up to 7 days. Replacing an image in place therefore does NOT show up on the
site promptly. To ship a changed image, add it under a new filename (e.g.
`og-card-2.png`) and point the HTML at that — same reason the rest of this repo
appends rather than rewrites.

The originals stay in zenserve's `static/site/img/` and stay mounted at `/img`:
og:image URLs on that domain are already indexed by crawlers and embedded in
previously-shared links, so that path has to keep answering. It just isn't what
the pages themselves reference any more.
