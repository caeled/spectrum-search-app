# Spectrum Search — Desktop App

A small desktop shell for [Spectrum Search](http://caeled.geekgalaxy.com/web/steve/phoenix/SpectrumSearch.html),
Steven Powell's consent-first, plain-language search page. The shell is built with
[Tauri v2](https://v2.tauri.app/) and hosts the single Spectrum Search HTML file in the
operating system's own webview — WebView2 on Windows, WKWebView on macOS — so there is no
bundled browser and the download stays small.

## Install

Step-by-step instructions for every platform are in [INSTALL.md](INSTALL.md).
The short version:

- **Web, no install:** https://caeled.github.io/mis-classmate-portal/SpectrumSearch.html
  (also on the author's server: http://caeled.geekgalaxy.com/web/steve/phoenix/SpectrumSearch.html)
- **Windows and Mac:** download the installer for your system from the
  [v0.8.0 release](https://github.com/caeled/spectrum-search-app/releases/tag/v0.8.0).
  Both builds are unsigned, so expect a one-time warning — INSTALL.md shows the way through it.
- **Chromebook:** open the GitHub web address in Chrome and choose **Install**
  (address-bar icon or ⋮ menu). It lands in the launcher like a native app.
- **iPad and iPhone:** open the GitHub web address in **Safari**, then
  Share → **Add to Home Screen**.
- **Android:** open the GitHub web address in Chrome, then ⋮ menu → **Install app**.

## The one rule

**Searching stays in the app. Opening a page does not.**

- The Spectrum Search page itself loads inside the window, and searching works there
  exactly as it does on the web.
- Any attempt to open a page — a result link (the page's results use `target="_blank"`),
  a `window.open`, or a plain navigation to an external http/https address — is
  intercepted by the shell, cancelled inside the app, and handed to the operating
  system's **default browser** instead.

The interception lives in `src-tauri/src/main.rs`: `on_new_window` denies every new
window and forwards http/https URLs to the default browser via the opener plugin, and
`on_navigation` allows only the app's own URLs (`tauri://`, `http://tauri.localhost`,
`about:`, `data:`) and routes any other http/https navigation out the same way.

## Storage note

The app webview keeps its **own** local storage, separate from your web browser. The
Spectrum Search settings, consent choices, and saved topics you set inside the app live
in the app; the ones in your browser live in your browser. Set things once in each place
you use.

## Updating the embedded page

The app embeds one file: `dist/index.html`.

1. Save the newest `SpectrumSearch.html` over `dist/index.html`.
2. Rebuild (locally with `npm run tauri build`, or push a `v*` tag / run the GitHub
   Actions workflow manually).

Nothing else changes; the shell does not care what the page contains.

## Building locally

Prerequisites: Node 20+, a stable Rust toolchain, and the platform webview pieces
(WebView2 is built into current Windows; macOS needs nothing extra). Linux
prerequisites are listed in the Tauri docs if you want to try it there.

```sh
npm install
npm run tauri build
```

Bundles land in `src-tauri/target/release/bundle/` (Windows: NSIS `.exe` installer and
`.msi`; macOS: `.dmg` and `.app`).

## Releases from GitHub Actions

`.github/workflows/build.yml` builds both platforms:

- Trigger it manually from the Actions tab ("Build desktop bundles" → Run workflow), or
- push a tag like `v0.8.0`.

Each run uploads the produced bundles as artifacts named
`spectrum-search-windows-latest` and `spectrum-search-macos-latest` (the macOS build is
a universal binary for Apple silicon and Intel).

### The macOS build is unsigned

There is no Apple code-signing certificate in this project, so macOS will warn on first
launch. To open it the first time: **right-click the app → Open → Open**. After that it
launches normally. This is expected for an unsigned build, not a defect.

## License

MIT — see [LICENSE](LICENSE).
