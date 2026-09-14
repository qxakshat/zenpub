# web/

Static artifacts for the public marketing site (https://zfolio.co.in), published
here so the site's browsers fetch them from jsdelivr's GitHub CDN instead of
from the zenserve VM.

zenserve is a single free-tier GCE instance. Every image the marketing pages
reference used to be served by that VM off `static/site/img/` via the `/img`
mount, so a page view cost it one request for the logo plus one per broker mark
— the noisiest thing in its access log, for bytes that change about twice a
year. Nothing here is dynamic, so none of it belongs on the API.

The six `*.webp` app screenshots are mirrored here too, but note that no page
currently references them — they are carried along so this directory is a
complete copy of the site's image set, not because they were costing requests.

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

## img/app/

The image set the mobile app (zenfolio) uses at runtime: the three broker marks
on the connect/login screens plus the Google sign-in mark. These used to be
bundled into the APK/IPA under `assets/imgs/`; the app now fetches them from
here and keeps a local on-device copy (see the app's
`src/utils/remoteAssetService.js`).

It is a separate namespace from `img/brokers/` on purpose, even though
`groww.png` and `paytm-money.png` are currently byte-identical to their
copies there. The site and the app already disagree — the app's `zerodha.png`
is a different crop of the mark from the site's — and giving each surface its
own set means restyling one cannot silently change the other. The cost is that
a mark you want changed everywhere has to be updated in both places.

Note: `app/google.png` is 840x859 and ~270KB, and the app renders it at 20x20.
It is published here as-is (same bytes the app shipped before), but re-encoding
it at ~64px would cut it to a couple of KB and is worth doing.

The app's launcher icon and splash (`assets/favicon.png`, `assets/splash-icon.png`
in zenfolio) are deliberately NOT here. Those are read by the native build at
compile time to produce the launcher icon, adaptive icon and splash screen —
they have to be files in the app bundle and cannot be fetched at runtime.
