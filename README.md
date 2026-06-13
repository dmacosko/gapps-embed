[Gapps Embed](https://github.com/benchkit-labs/gapps-embed) · **README** · [Quick start](#quick-start) · [Configuration](#configuration-via-query-string)

---

# Gapps Embed

**Embed Google apps anywhere. One iframe. No build step. No tracking.**

A lightweight toolkit for dropping a Google apps grid onto any site — your intranet, a portal, a sidebar, a launcher. Three visual presentations share one data core, one sprite, and zero build dependencies. Theme it via query string or `postMessage`. Rewrite URLs for any Workspace domain.

Static HTML + JavaScript. No accounts. No analytics. No server-side anything.

[Quick start](#quick-start) · [Configuration](#configuration-via-query-string) · [Theming](#theming-via-postmessage)

</div>

## Presentations

| | | |
|---|---|---|
| **Launcher** | Plain icon grid, identical to Google's own app launcher | ✅ `v1.0.0` |
| **Spotlight** | Card grid with name, description, and "Learn more" link | 🛠 `v1.1` |
| **Menu** | Waffle button that opens a Google-style popover panel | 🛠 `v1.2` |

## Quick start

```html
<iframe src="https://gapps-embed.benchkit-labs.dev/gapps-launcher.html"
        style="border: none; width: 100%; height: 600px;"></iframe>
```

That's it. You now have a Google apps grid on your page, served as a static site.

This URL always tracks the latest release. For production, pin to a tagged version instead so the embed never changes underneath you — see [Pinning to a version](#pinning-to-a-version).

**Live demo, themed examples, and an interactive URL builder:** [gapps-embed.benchkit-labs.dev](https://gapps-embed.benchkit-labs.dev/)

## Configuration via query string

All presentations honor the same parameters.

### Filter — pick which apps to show

| Param | Example | Effect |
|---|---|---|
| `apps` | `?apps=mail,drive,docs` | Show specific apps in that order |
| `category` | `?category=communication` | Show apps in one or more categories |
| `filter` | `?filter=workspace` | Show only `workspace`-tagged apps |

### Auth — point at your Workspace

| Param | Example | Effect |
|---|---|---|
| `domain` | `?domain=example.com` | Rewrite URLs through `accounts.google.com/AccountChooser` for your Workspace domain |
| `custom` | `?custom=mail,calendar` | Use `https://app.domain/` for these apps instead of the default Google URL |

### Theme — match your site

| Param | Example | Effect |
|---|---|---|
| `tile` | `?tile=%23ffffff` | Tile background color |
| `text` | `?text=%23202124` | Label text color |
| `texthover` | `?texthover=%230E1B2A` | Label text color *on hover* — set when your hover background contrasts with the rest state (e.g., a color-flip theme where the tile lights up on hover) |
| `hover` | `?hover=%23f1f3f4` | Hover state background |

Colors are URL-encoded hex (e.g., `#1FA9A5` → `%231FA9A5`). `hover` is auto-derived from `tile` if you don't set it. `texthover` defaults to `text` if omitted (the label stays the same color on hover).

## Theming via postMessage

Update the theme dynamically from the parent page:

```js
iframe.contentWindow.postMessage({
  type: 'gapps-embed-theme',
  tileBg:         '#ffffff',
  hoverBg:        '#f1f3f4',
  textColor:      '#202124',
  textColorHover: '#202124'  // optional — defaults to textColor if omitted
}, '*');
```

Use `textColorHover` when the hover background contrasts strongly with the rest tile and the label needs to flip color to stay readable (e.g., dark label at rest on a muted tile, light label on hover when the tile lights up — or the reverse pattern).

The iframe reports its rendered height back so you can size it correctly:

```js
window.addEventListener('message', e => {
  if (e.data.type === 'gapps-embed-height') {
    iframe.style.height = e.data.height + 'px';
  }
});
```

## Pinning to a version

The live site URL (`gapps-embed.benchkit-labs.dev`) always serves the latest `main`, so an embed pointed at it can change when a new version ships. For production, pin to a tagged release through a versioned CDN instead:

**Option 1: jsDelivr (fastest, recommended)**
```html
<iframe src="https://cdn.jsdelivr.net/gh/benchkit-labs/gapps-embed@v1.0.0/gapps-launcher.html">
```

**Option 2: raw.githack.com**
```html
<iframe src="https://rawcdn.githack.com/benchkit-labs/gapps-embed/v1.0.0/gapps-launcher.html">
```

**Option 3: unpkg**
```html
<iframe src="https://unpkg.com/gapps-embed@1.0.0/gapps-launcher.html">
```

All three CDNs cache a tagged release indefinitely, so a pinned embed is frozen at that version.

**Rule of thumb:** use the Pages URL for demos and internal pages where always-latest is fine; pin to `@v1.0.0` via a CDN for anything you can't easily update later.

## Self-hosting

You don't need a CDN. Clone the repo, edit `src/apps.js` to add/remove/relabel apps, and serve the files from any static host:

```bash
git clone https://github.com/benchkit-labs/gapps-embed.git
cd gapps-embed
# edit src/apps.js if you want a custom app list
python -m http.server 8080
```

That's the entire setup. Open `http://localhost:8080/gapps-launcher.html`.

## What's in the box

```
gapps-embed/
├── index.html                # Landing page
├── gapps-launcher.html       # Presentation 1: plain icon grid
├── gapps-builder.html        # Interactive embed-URL builder
├── src/
│   ├── apps.js               # Shared app inventory (the data core)
│   ├── filter.js             # Shared filter pipeline (querystring → apps)
│   ├── theme.js              # Shared theme + postMessage handling
│   └── sprite.css            # Icon sprite coordinates
└── assets/
    └── sprite.png            # All app icons in one sprite
```

## Why this exists

Google's app launcher is a closed UI element you can't embed outside `google.com`. Workspace admins, intranet builders, and anyone running a portal end up either rebuilding it from scratch or accepting that their users have to leave the site to find Gmail.

Gapps Embed is the small, free, open-source version of that launcher. It runs entirely in the visitor's browser, makes no network calls beyond loading the static files, and has no tracking. Drop it in and forget about it.

## Stack

- Vanilla JavaScript (ES2022+, no transpilation)
- Zero runtime dependencies
- Static HTML/CSS, served as-is by GitHub Pages
- One PNG sprite for all 50 app icons (refreshed via `REFRESHING_ICONS.md`)

## Contributing

Pull requests welcome. Project-specific notes:

- **No build step.** Files must be drop-in usable.
- **Test all three breakpoints:** ≥1024px / 600–1023px / <600px.
- **Sprite refreshes** must update both `assets/sprite.png` and the `.icon-*` CSS coords in `src/sprite.css` in lockstep.
- **No external dependencies.** Keep the codebase vanilla JavaScript with zero runtime deps.

## License

MIT — see [LICENSE](LICENSE). Free to use, modify, and distribute.
