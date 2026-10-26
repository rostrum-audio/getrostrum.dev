# getrostrum.dev

The website for [Rostrum](https://github.com/rostrum-audio/rostrum), a stream mix console for
Linux. It is plain hand-written HTML and one stylesheet: no framework, no build step, no
authored JavaScript or tracking code, with self-hosted styles and images. Cloudflare
processes serving/security requests and operational statistics; deployed responses
also advertise Network Error Logging. The custom domains currently receive an injected
Web Analytics script tag, which the strict CSP blocks. The Pages origin had no script
tag during browser inspection. See [privacy.html](privacy.html) for these distinctions.

Bugs and feature requests for the app go to the
[app repository](https://github.com/rostrum-audio/rostrum/issues).

## Files

| File | Purpose |
| --- | --- |
| `index.html` | Landing page |
| `privacy.html` | Privacy summary, linking to the app's full `docs/privacy.md` |
| `404.html` | Not found page (Cloudflare Pages serves it automatically) |
| `style.css` | The only stylesheet; light and dark follow `prefers-color-scheme` |
| `images/` | Logo lockups, release screenshots and the social preview |
| `favicon.svg`, `apple-touch-icon.png` | Icons |
| `_redirects` | Cloudflare Pages redirects, including the updater feed (below) |
| `_headers` | Security headers: a strict Content-Security-Policy and friends |
| `robots.txt` | Allows all crawlers |

## Hosting: Cloudflare Pages

Connect this repository in Cloudflare under **Workers & Pages → Create → Pages → Connect to Git**
with these build settings:

- Framework preset: **None**
- Build command: *(empty)*
- Build output directory: **`/`**

Every push to `main` deploys. Other branches get preview deployments.

Custom domains: `getrostrum.dev` and `www.getrostrum.dev`.

Web Analytics injection is a hosting setting, not authored site code. Do not weaken
CSP to let the beacon run. Changing that setting requires separate authorization;
disabling it would not itself disable Network Error Logging or request processing.
Verify delivered HTML and browser requests on both custom domains after hosting
changes: source inspection or a generic curl response alone can miss injection.

## The updater feed redirect

Rostrum's update checker fetches `https://getrostrum.dev/releases/latest.json` once a day. The
app's release workflow attaches `latest.json` to every GitHub release that is not a pre-release,
so the first line of `_redirects` sends that path to the newest one:

```
/releases/latest.json  https://github.com/rostrum-audio/rostrum/releases/latest/download/latest.json  301
```

Keep that line first and keep the path unchanged: released versions of the app have the URL built
in. Rostrum 0.2.0 is published; the redirect reaches a feed whose AppImage hash
and size match the public download. Do not change
this redirect to point at a local fixture or manually edit the public feed.

`/github` and `/download` are short links to the repository and the latest release.

Test after deploying:

```sh
curl -sI https://getrostrum.dev/releases/latest.json
```

Expect `HTTP/2 301` with
`location: https://github.com/rostrum-audio/rostrum/releases/latest/download/latest.json`. To
follow it all the way to the feed:

```sh
curl -sL https://getrostrum.dev/releases/latest.json
```

## Editing

Open `index.html` in a browser or serve it locally; there is nothing to build.
For release-content changes, inspect narrow and desktop layouts in light/dark mode.
Match download links and compatibility claims to the published release, and keep
source-build minimums separate from the tested AppImage environment. OBS captures
Stream Mix, VOD Mix and Rostrum Mic separately. Recording tracks 3–6 are optional;
readiness and matching assignments do not verify recorded sound.

The six app screenshots use the published 0.2.0 AppImage in a disposable container
with private PipeWire, WirePlumber, PulseAudio compatibility, D-Bus and Xvfb. Devices
and app names are demo fixtures. The OBS page shows an actual isolated OBS instance;
no recording or stream was started. Never capture the user’s live session. Replace
the image URL’s `?v=` value with the new image’s SHA-256 prefix when updating a
screenshot, and update its dimensions, alt text and caption. Preserve CSP and static assets.
Keep everything self-hosted: the
Content-Security-Policy in `_headers` only allows styles and images from this site, so inline
`style` attributes, scripts and external resources will not load. Feature and privacy wording
follows the app's `README.md` and `docs/privacy.md`; update them together.

## License

Apache-2.0. See [LICENSE](LICENSE).
