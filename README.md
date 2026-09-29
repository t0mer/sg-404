# sg-404

A Stargate SG-1-themed "404 Not Found" page. It is a single, self-contained HTML file. When the
page loads, a stylized Stargate dials an address, locks its chevrons one by one, opens the event
horizon, and then reports that the offworld address is invalid.

The page is pure HTML, CSS, and vanilla JavaScript. It loads no external fonts, images, scripts,
or stylesheets, so you can drop it into any web server as a custom error page. It also works
offline.

<!-- TODO: screenshot -->

## Contents

- [Features](#features)
- [How it works](#how-it-works)
- [Deployment](#deployment)
- [Using it as your own 404 page](#using-it-as-your-own-404-page)
- [Customization](#customization)
- [Limitations](#limitations)
- [Contributing](#contributing)
- [Trademarks and disclaimer](#trademarks-and-disclaimer)
- [License](#license)

## Features

- **Animated Stargate.** The gate is built from CSS gradients and positioned `<div>`s. It has
  9 chevrons, a slowly rotating ring of glyphs, and an event horizon that ripples while it is open.
- **Dialing sequence.** When the page loads, a chevron locks about every 0.5 seconds. After all 9
  have locked, the wormhole opens, then the status changes to `NO ROUTE` / `OFFWORLD ADDRESS INVALID • 404`.
  The page stays on this failure screen, with the event horizon open, until you re-dial or abort.
- **SGC status readout.** Shows a random IDC code and a random 7-glyph "remote address" on every
  dial, plus MALP telemetry, gate status, and `ANOMALY: RESOURCE NOT FOUND`. The readout is an
  `aria-live="polite"` region.
- **Controls.**
  - **Re-dial** button or the `R` key starts a new dialing sequence.
  - **Abort Dialing** button or the `Esc` key resets the chevrons and shows `DIALING ABORTED`.
- **Ambient effects.** A drifting CSS starfield, a scan-line sweep over the readout, and a brief
  "glitch" effect on the headline.
- **Responsive layout.** Two columns on wide screens; at 900 px and narrower the layout stacks into one
  column and the page scrolls.
- **No dependencies.** It uses system font stacks and Unicode glyphs (runic letters, math
  symbols and arrows) instead of web fonts or images. There is no sound, no tracking, and no network request.

The page does **not** redirect, count down, show the requested URL, or link back to a home page.

## How it works

Everything is in [`index.html`](index.html):

| Part | What it does |
|------|--------------|
| `<style>` | Theme colors as CSS custom properties, the starfield, the gate rings, chevron and event-horizon styles, and the keyframe animations (`drift`, `scan`, `ripple`, glitch). |
| Markup | A left panel (headline, subtitle, status readout, buttons) and a right panel (the gate). |
| `<script>` | An IIFE that creates the 38 glyphs and 9 chevrons around the ring, runs the dialing sequence with `setInterval`/`setTimeout`, and handles the buttons and the `R`/`Esc` shortcuts. |

## Deployment

The page is published with **GitHub Pages** by the workflow
[`.github/workflows/static.yml`](.github/workflows/static.yml) ("Deploy static content to Pages"):

- **Triggers:** every push to `main`, or a manual run from the Actions tab (`workflow_dispatch`).
- **What it publishes:** the entire repository (`path: '.'`), not just `index.html`, so
  `README.md` and `CNAME` are served as well.
- **Steps:** `actions/checkout@v4` → `actions/configure-pages@v5` →
  `actions/upload-pages-artifact@v3` → `actions/deploy-pages@v4`, into the `github-pages`
  environment. An in-progress deployment is never cancelled; only the latest pending run waits
  for it (`cancel-in-progress: false`).

For the workflow to deploy, the repository's Pages source must be set to **GitHub Actions**
(Settings → Pages).

### Custom domain

The [`CNAME`](CNAME) file sets the custom domain to `404.cloudguard.co.il`. GitHub Pages serves
the site on that domain only if DNS for it points to GitHub Pages.
<!-- TODO: verify: 404.cloudguard.co.il did not resolve in DNS when this README was written (2026-09-29) -->

An alternative Cloudflare Workers setup is proposed in an open pull request. It is not part of
`main`.

## Using it as your own 404 page

The file has no external assets and uses no relative paths, so it renders the same at any URL.
Copy `index.html` and rename it as your server expects.

### GitHub Pages

Save the file as `404.html` in the root of the published site. GitHub Pages then serves it, with
a 404 status, for any path that does not exist.

### Nginx

```nginx
server {
    # ...
    error_page 404 /404.html;

    location = /404.html {
        root /var/www/errors;   # directory that contains 404.html
        internal;
    }
}
```

### Apache

In the virtual host or in `.htaccess` (requires `AllowOverride FileInfo`):

```apache
ErrorDocument 404 /404.html
```

### Cloudflare

Cloudflare Pages serves a `404.html` from the project's output directory for unknown paths, so
save this file there as `404.html`. Cloudflare's classic custom error pages only replace errors
that Cloudflare itself generates, not a 404 returned by your origin server. Setup details depend
on the product and plan; see the Cloudflare documentation.

## Customization

- **Text:** edit the headline, subtitle, readout labels, and the footer line
  (`SGC • GATE ROOM 404 • “Indeed.”`) directly in the markup.
- **Colors:** change the CSS custom properties in `:root` (`--blue`, `--cyan`, `--teal`,
  `--amber`, `--red`, and so on).
- **Dialing speed:** the chevron interval is `520` ms in `startDialing()`; the failure message
  appears `1600` ms after the wormhole opens.
- **Glyphs:** edit the `glyphs` array in the script.

## Limitations

- The chevrons and the glyph ring are placed with fixed pixel radii (220 px and 192 px). On
  screens narrower than about 510 px the gate shrinks (`min(440px, 86vw)`) but these radii do
  not, so the chevrons sit outside the ring and the glyphs can be clipped.
- The animations do not respect `prefers-reduced-motion`.
- The gate never returns to standby after a dial. A timer sets `dialing = false` 2800 ms after
  the wormhole opens, just before the standby callback runs (also at 2800 ms), and that callback
  exits when `dialing` is false. The page stays on `NO ROUTE` with the event horizon open until
  you re-dial or abort.
- The glyphs are Unicode runic letters, math symbols and arrows, not the gate glyphs from the show.

## Contributing

Issues and pull requests are welcome. To try a change, open `index.html` in a browser; there is
no build step.

## Trademarks and disclaimer

Stargate and Stargate SG-1 are trademarks of their respective owners (MGM). This is a
non-commercial fan page and is not affiliated with, endorsed by, or sponsored by MGM.

The page contains no images, logos, video, or audio from the show. The gate, starfield, and
glyphs are drawn with CSS and Unicode characters in `index.html`. The text uses terms from the
series (SGC, MALP, IDC, chevron, event horizon) and the catchphrase "Indeed."

## License

No license file.
