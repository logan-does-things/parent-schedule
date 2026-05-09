# Family Time

A simple, single-file web app for tracking which days you have your kid, plus events and birthdays. No accounts, no servers, no app store. Just an HTML file you host on GitHub Pages, with optional sync to a private GitHub repo so your data lives wherever you do.

Built mostly from a phone, mostly out of stubbornness.

## What it does

- **Mark days** — tap a day to view its events, long-press to toggle "my day"
- **Track events** — add notes/appointments to any day with optional times (swim lessons 4pm, dentist 2:30pm, etc.)
- **Subscribe to iCal feeds** — pull in school district calendars, holidays, sports schedules, anything that publishes an `.ics` URL
- **Recurring birthdays** — add once, shows every year
- **Multiple calendar views** — Day, Week (timeline), Month (grid), Year (overview)
- **Stats** — see how much of the current month you have planned, plus actual days year-to-date
- **Sync across devices** — optional, via your own private GitHub repo

All data stays yours. The app code is public, but your data file is private.

## Setup

The app itself is one HTML file. You can use it three ways:

### Option 1 — Just open it locally

Download `index.html`, open it in a browser. Data is saved in that browser only. Fine for trying it out.

### Option 2 — Host on GitHub Pages (no sync)

1. Fork this repo
2. Go to **Settings → Pages**
3. Source: **Deploy from a branch**, branch: **main**, folder: **/ (root)**
4. Wait a couple minutes — your URL will be `https://YOUR-USERNAME.github.io/REPO-NAME/`
5. Open it on your phone, "Add to Home Screen" for an app-like experience

Data still only lives in whatever browser you opened it in.

### Option 3 — Host on GitHub Pages + sync your data privately

This is what makes it actually useful across devices.

1. Do everything in Option 2
2. Create a **second** repo on GitHub, this time **private**. Name it whatever (e.g. `family-time-data`)
3. Add a single file to that private repo called `data.json` containing just `{}`
4. Generate a **fine-grained personal access token**:
   - GitHub → Settings (your account) → Developer settings → Personal access tokens → Fine-grained tokens
   - Generate new token
   - Resource owner: your account
   - Repository access: Only select repositories → pick your data repo
   - Permissions: Repository permissions → **Contents: Read and write**
   - Generate, copy the token (you'll only see it once)
5. Open your hosted app, tap the menu (⋯) → **Sync settings**
6. Enter your username, the data repo name, the file path (`data.json`), and paste your token
7. Tap **Save & Test** — should turn green

From now on, every change auto-pushes to the private repo. Open the app on another device, set up the same sync, and your data follows you.

## How it works

- **Single file** — everything lives in `index.html`. HTML, CSS, JavaScript all in one. No build step, no dependencies, no frameworks
- **localStorage** — your data is cached in your browser for instant load
- **GitHub Contents API** — when sync is enabled, the app reads/writes a JSON file in your private repo via authenticated HTTPS calls
- **Debounced writes** — taps are quick locally; the sync waits ~1.5 seconds after your last change before pushing
- **iCal subscriptions** — fetches `.ics` URLs and parses out events. Many calendar feeds block direct browser fetch due to CORS, so there's a toggle to route through `corsproxy.io` when needed

## Data shape

If you want to edit your `data.json` directly:

```json
{
  "days": {
    "2026-05-07": true,
    "2026-05-08": true
  },
  "events": {
    "2026-05-07": [
      { "id": "abc123", "title": "Swim lessons", "hour": "4", "minute": "0", "period": "PM" }
    ]
  },
  "birthdays": {
    "06-15": "John",
    "03-22": "Mom"
  }
}
```

- `days` — `YYYY-MM-DD` keys for days you have your kid
- `events` — `YYYY-MM-DD` keys mapping to arrays of events. Time fields are optional
- `birthdays` — `MM-DD` keys (no year) so they recur every year

## Things to know

**Privacy.** The app code is public, but your data file is private (assuming you set up sync correctly). The only thing stored on your device is the data plus your access token in localStorage. Treat the token like a password — anyone with access to your unlocked phone could pull it.

**No accounts, no analytics.** Nothing is sent anywhere except your own GitHub repo when you sync, and any iCal URLs you subscribe to.

**Browser storage limits.** localStorage caps around 5MB per site. You'd need decades of data to hit that.

**iCal CORS.** Most calendar feeds (school districts, etc.) don't include CORS headers, so direct browser fetches fail. The app falls back to `corsproxy.io` if you enable the toggle in settings. The proxy sees the URL but not your data — fine for public calendar feeds. Some corporate firewalls block proxies entirely.

**iOS PWA quirks.** When added to the home screen on iOS, the "app" gets its own isolated localStorage separate from Safari. If you also use Safari to access it, you'll have two separate caches unless sync is configured.

## Built with

This was largely vibe-coded over a series of conversations with Claude (Anthropic) — mostly from an iPhone, which means many of the design decisions are shaped by "what's tolerable to test on mobile." Source is intentionally one file because juggling multiple files from a phone is a special kind of suffering.

## License

MIT. Do whatever you want with it.
