# getrostrum.dev

The website for [Rostrum](https://github.com/rostrum-audio/rostrum), a stream mix console for
Linux. It is plain hand-written HTML and one stylesheet: no framework, no build step, no
JavaScript, no analytics, and nothing loaded from other servers.

Bugs and feature requests for the app go to the
[app repository](https://github.com/rostrum-audio/rostrum/issues).

## Files

| File | Purpose |
| --- | --- |
| `index.html` | Landing page |
| `privacy.html` | Privacy summary, linking to the app's full `docs/privacy.md` |
| `404.html` | Not found page (Cloudflare Pages serves it automatically) |
| `style.css` | The only stylesheet; light and dark follow `prefers-color-scheme` |
| `images/` | Logo lockups, app screenshots and the social preview, copied from the app repository |
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

## The updater feed redirect

Rostrum's update checker fetches `https://getrostrum.dev/releases/latest.json` once a day. The
app's release workflow attaches `latest.json` to every GitHub release that is not a pre-release,
so the first line of `_redirects` sends that path to the newest one:

```
/releases/latest.json  https://github.com/rostrum-audio/rostrum/releases/latest/download/latest.json  301
```

Keep that line first and keep the path unchanged: released versions of the app have the URL built
in. Until the first release is published, GitHub answers the redirect target with 404.

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

Open `index.html` in a browser; there is nothing to build. Keep everything self-hosted: the
Content-Security-Policy in `_headers` only allows styles and images from this site, so inline
`style` attributes, scripts and external resources will not load. Feature and privacy wording
follows the app's `README.md` and `docs/privacy.md`; update them together.

## License

Apache-2.0. See [LICENSE](LICENSE).
