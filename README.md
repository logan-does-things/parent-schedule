# Family Schedule

A simple, single-file web app for tracking which days you have your kid, plus events, birthdays, and time off. No accounts, no servers, no app store. Just an HTML file you can open in any browser, host on GitHub Pages, and optionally sync to a private GitHub repo so your data follows you across devices.

Built mostly from a phone, mostly out of stubbornness, mostly by [Claude](https://claude.ai). I described what I wanted, drilled on the details, and pushed back when it suggested dumb things. The code is Claude's. The opinions in this README are mine.

If you can copy a file and follow a numbered list, you can use this.

## What it does

- **Mark days you have your kid** — tap a day to view it, long-press to toggle "my day"
- **Track events** — add appointments, swim lessons, anything with an optional time
- **Subscribe to iCal feeds** — pull in school calendars, holidays, sports schedules, whatever publishes an `.ics` URL
- **Recurring birthdays** — add once, shows every year
- **Time off (PTO)** — mark AM, PM, or full days off work, shown as a green bar in the calendar
- **Calendar begin date** — tell the app "ignore stats from before this date" so percentages aren't dragged down by old data
- **Four views** — Day, Week (timeline), Month (grid), Year (overview)
- **Stats** — see how much of the current month you have planned, plus year-to-date
- **Sync across devices** — optional, via your own private GitHub repo

All data stays yours. The app code is public; your data file is private.

## Setup

The app is one HTML file. Three ways to use it, in order of effort:

### Option 1 — Just open it locally

1. Download `index.html` from this repo
2. Double-click it. It opens in your browser.
3. Done.

Your data is saved only in that browser, on that device. Fine for trying it out or single-device use. Clearing browser data wipes it.

### Option 2 — Host it on GitHub Pages (still local data)

This gives you a real URL you can open on your phone and "Add to Home Screen" so it acts like an app.

1. **Fork this repo** (top-right "Fork" button on GitHub)
2. In your fork, go to **Settings → Pages**
3. Source: **Deploy from a branch**
4. Branch: **main**, folder: **/ (root)**
5. Click Save. Wait ~2 minutes.
6. Your URL is `https://YOUR-USERNAME.github.io/YOUR-REPO-NAME/`
7. Open it on your phone, tap Share → Add to Home Screen

Still no sync — data only lives in whichever browser opened the page.

### Option 3 — Host it on GitHub Pages + sync your data privately

This is the version that actually follows you across devices. It's a little more setup, but only once.

**Step A: Do Option 2 first** (you need the hosted app working).

**Step B: Create a private data repo.**

1. On GitHub, **New repository**
2. Name it something like `family-data` (the name doesn't matter)
3. **IMPORTANT:** Set it to **Private**
4. Check "Add a README" so the repo isn't empty
5. Click Create

**Step C: Add an empty data file.**

1. In your new private repo, click "Add file" → "Create new file"
2. Filename: `data.json`
3. File contents: just `{}` (two characters — open brace, close brace)
4. Scroll down, "Commit new file"

**Step D: Generate a personal access token.**

GitHub needs a way to let the app write to your private repo without you typing your password every time. Tokens are scoped passwords — this one will only have access to the one data repo.

1. GitHub → click your avatar (top-right) → **Settings**
2. Scroll down to **Developer settings** (left sidebar, very bottom)
3. **Personal access tokens** → **Fine-grained tokens**
4. **Generate new token**
5. Token name: anything (e.g. "family schedule app")
6. Expiration: pick whatever — I use 1 year and renew when it expires
7. Resource owner: your account
8. Repository access: **Only select repositories** → pick your data repo (the private one from Step B)
9. Permissions → **Repository permissions** → find **Contents** → set to **Read and write**
10. Scroll to bottom, **Generate token**
11. **Copy the token immediately** and stash it somewhere — GitHub only shows it once

**Step E: Connect the app.**

1. Open your hosted app
2. Tap the menu icon (top-right hamburger) → **Sync settings**
3. Fill in:
   - **GitHub username:** your username
   - **Private repo name:** the name from Step B (e.g. `family-data`)
   - **File path in repo:** `data.json`
   - **Personal access token:** paste the token from Step D
4. Tap **Save & Test**

If everything's right, you'll see "Connected — sync is on." From now on, changes you make auto-push to your private repo. To set up on another device, repeat Step E with the same credentials.

### Step F (optional but recommended): PWA install

On iOS Safari or Android Chrome, open your hosted app, then:

- **iOS:** Share button → "Add to Home Screen"
- **Android:** menu (⋮) → "Add to Home Screen" or "Install app"

This makes it look and feel like a standalone app — full screen, no browser chrome, app icon on your home screen.

## Features

### Calendar views

Four ways to look at the same data, picked from the buttons at the top:

- **Day** — single day, full event list, easy to scroll between days
- **Week** — 7-day timeline with timed events plotted on an hourly grid
- **Month** — traditional grid view, dots for events, triangles for kid-days, bars for PTO
- **Year** — heatmap of the year, one tile per day, color-coded by whether you have your kid

### Kid-present tracking

The whole point. In month view, **long-press** a day to toggle "my day." In day view, tap **My day** in the action bar. Days with the kid show a small triangle in the corner and tint the cell.

Stats at the top show: "Mon 2026: 12/31 - 39%" (you have your kid 12 of 31 days this month).

### Events

Tap **+ Add event** in the day view, or **+ Add event** in the action panel for any selected day. Events can be timed (8:30 AM) or all-day. Tap an event to edit or delete it.

Events are stored under `YYYY-MM-DD` keys, so they're tied to a specific date — they don't recur.

### Birthdays

Hamburger menu → **Birthdays**. These are stored as `MM-DD` (no year), so they appear every year automatically. They show up as purple dots in month view and as items in the day's event list.

### Subscribed calendars

Hamburger menu → **Subscribed calendars**. Add an iCal `.ics` URL — school district calendars, sports schedules, holidays. Events from subscriptions show as grey dots in month view and are read-only (you can't edit or delete them; remove the whole subscription if you don't want them).

Many calendar feeds block direct browser fetches due to CORS. There's a per-subscription toggle to route through `corsproxy.io` if a feed doesn't load directly.

### Time off (PTO)

Hamburger menu → **Time Off**. Three options per day:

- **AM** (8 AM – 12 PM) — green bar on the left half of the month cell
- **PM** (12 PM – 5 PM) — green bar on the right half
- **Full** (8 AM – 5 PM) — green bar full width

PTO also shows in the week timeline as a green block in the appropriate hours, and in the day/week event lists with a green tint. One PTO entry per day max — re-saving overwrites.

### Calendar begin date

Sync settings → **Calendar begin date**. This is for when you have data going back further than you want stats to reflect. Pick a date with the three dropdowns, save, and stats math (both the count and the denominator) ignores anything before it.

Example: you have data going back to 2023, but you only want percentages calculated from January 13, 2023 onward. Set the cutoff to 2023-01-13. The data is still there — just excluded from the math. Leave all three dropdowns blank to use everything.

The cutoff syncs via GitHub, so all your devices stay in agreement.

### Data tools (the cog button)

Bottom-right corner of the screen, there's a green cog icon. Tap it to open Data Tools:

- **Export local backup** — downloads your data as a JSON file. Good before risky operations.
- **Import local backup** — merges a previously-exported file into your current data. Doesn't replace; adds.
- **Clear all data** — wipes local data. Type-to-confirm (you have to type `CLEAR`). Defaults to also disconnecting GitHub sync, because if you wipe local and stay connected, the next push uploads empty data and overwrites your remote backup. Don't uncheck that box unless you know why.

## Gestures and shortcuts

Mostly tap and long-press. Some specifics:

- **Tap a day (month/year view)** — selects it, scrolls to event list
- **Long-press a day (month view)** — toggles "my day"
- **Tap an event** — opens edit modal
- **Swipe left/right (day view)** — go to prev/next day
- **Tap month name in header** — opens month/year picker
- **Tap the sync status bar** — manual sync (pull + push)
- **Tap outside any modal** — closes it

## Data model

If you ever need to edit `data.json` directly, or just want to know what's in there, here's the shape:

```json
{
  "days": {
    "2026-05-07": true,
    "2026-05-08": true
  },
  "events": {
    "2026-05-07": [
      {
        "id": "abc123",
        "title": "Swim lessons",
        "hour": "4",
        "minute": "0",
        "period": "PM"
      }
    ]
  },
  "birthdays": {
    "06-15": "John",
    "03-22": "Mom"
  },
  "pto": {
    "2026-05-10": { "name": "Time Off", "span": "FULL" },
    "2026-05-15": { "name": "", "span": "AM" }
  },
  "cutoff": "2023-01-13"
}
```

- **`days`** — `YYYY-MM-DD` keys. Value is always `true`. Absence = day not marked.
- **`events`** — `YYYY-MM-DD` keys mapping to arrays of event objects. Each event has an `id`, `title`, and optional `hour` / `minute` / `period`.
- **`birthdays`** — `MM-DD` keys (no year) mapping to a name string. Recurs annually.
- **`pto`** — `YYYY-MM-DD` keys mapping to `{ name, span }`. `span` is one of `"AM"`, `"PM"`, `"FULL"`. Empty name displays as "Time Off."
- **`cutoff`** — a single `YYYY-MM-DD` string or `null`. Stats math ignores keys before this.

Subscribed iCal events are NOT in this file — they're fetched live and cached in browser memory only. The subscription URLs themselves are stored locally per-device (not in the synced data) since each device might have different proxy preferences.

## Sync architecture

How the GitHub sync actually works, in case future-you is debugging:

**Storage layers (in order of precedence on load):**

1. localStorage `familyData` — instant, primary read
2. GitHub `data.json` — pulled on app boot if sync is configured

**Write flow:**

1. You make a change (tap, add, edit)
2. Local state updates immediately
3. localStorage gets saved synchronously
4. UI re-renders
5. `schedulePush()` sets a 1.5-second debounced timer
6. After 1.5 seconds of no further changes, the app PUTs the whole `familyData` JSON to GitHub via the Contents API
7. The new SHA is recorded so the next push knows the parent commit

**Read flow (boot):**

1. localStorage loads first (instant render)
2. If sync is configured, GET the latest `data.json` from GitHub
3. Merge remote into local using `mergeData()`
4. Save merged data locally, re-render, update stats

**Merge semantics:**

`mergeData(local, remote)` does a shallow merge per top-level key:

- `days`, `events`, `birthdays`, `pto` — object spread, **remote wins on same key**. New keys on either side are preserved.
- `cutoff` — scalar, **remote wins** if remote has a value (including null). If remote predates the cutoff feature and the key is `undefined`, local is preserved.

There's no timestamp-based conflict resolution. Last-write-wins on a per-key basis, with remote winning on a tie. For a one-person, mostly-one-device app, this is fine. The main failure mode is two devices editing the same date offline — the device that pulls last wins.

**Why GitHub:** because I already have GitHub, the API is free, and the data is stored under my account where I can audit, revert, or download it anytime. Git history is also a free undo system — wiped data locally? Check the commit log on the data repo and restore.

## Things to know (footguns and design choices)

**Clear all data + sync = remote wipe.** This is the biggest footgun. If you tap Clear all data with sync still connected, the next debounced push uploads an empty file and **overwrites your private repo backup**. The clear modal defaults to "also disconnect from GitHub sync" precisely to prevent this. If you really did clear by mistake and the remote is gone, check the data repo's commit history — Git keeps the previous version forever, you can restore it.

**Cutoff is remote-wins.** If two devices set different cutoffs offline, the device that pulls last loses its setting. Low risk for one-person use, but worth knowing.

**Subscription data isn't in sync.** Subscribed iCal URLs and their cached events live per-device. Adding a subscription on Device A doesn't propagate to Device B — you'd add it on both. This is intentional; subscription preferences (which proxy, which feeds) often differ per device.

**Local token storage.** Your GitHub token is stored in browser localStorage, in plaintext, on every device you connect. Treat that device like you'd treat a device with your password manager open. If a device is lost, **revoke the token on GitHub immediately** — your data is private, but the token is the key.

**Browser storage caps.** localStorage is ~5MB. You'd need decades of dense event data to hit it.

**iOS PWA isolation.** A "Add to Home Screen" app on iOS gets its own localStorage separate from Safari. If you use both, configure sync on both or they won't share data.

**iCal CORS.** Many calendar feeds don't include CORS headers. The app has a per-subscription proxy toggle (`corsproxy.io`) for these. The proxy sees the iCal URL but not your data — fine for public school calendars, but consider whether you're OK with that for any feed you add.

**No undo button in-app.** Deletions are immediate. If you delete a year's worth of data, the local copy is gone and the next push wipes the remote. Recovery means checking the data repo's commit history on GitHub.

**Birthdays don't have years.** They're `MM-DD` only. If you need to remember the year someone was born, put it in the name ("Mom (1958)") — the app won't help with age math.

**The cog button is for the rare stuff.** Export, import, clear. It sits in the bottom-right corner intentionally muted because you shouldn't be reaching for it often. Day-to-day adding/editing happens in the hamburger menu and the day's action panel.

## How it's built

- **One HTML file.** Everything — HTML, CSS, JS — in `index.html`. No build step, no `npm install`, no framework. View source to read it.
- **Vanilla JS.** No React, no jQuery. Lots of `document.getElementById`.
- **Modal-driven UI.** Bottom sheets for everything: menus, edits, settings.
- **localStorage primary, GitHub secondary.** Always works offline; sync is bonus.
- **Debounced writes.** Changes feel instant locally; the network sync waits 1.5 seconds after the last edit before pushing.
- **GitHub Contents API.** Standard REST. No serverless functions, no proxies (except for iCal CORS), no third-party services.

## Roadmap / things I'd like to do eventually

These aren't promises, just notes-to-self:

- [ ] **Search past chats / find an event by name.** Right now you have to navigate to the date.
- [ ] **Multi-day PTO entry.** Currently one day at a time; if you take a week off it's seven taps.
- [ ] **"Since calendar start" stat.** A third stat next to month and year showing the cumulative count since the cutoff date.
- [ ] **Better merge conflict handling.** Timestamps per top-level key, or per-record, so offline edits on two devices merge instead of one wiping the other.
- [ ] **Event recurrence.** Right now events are per-date. Recurring events (every Tuesday at 4) would be nice.
- [ ] **Color-coded event categories.** Customizable tags / colors for different types of events.
- [ ] **Subscription sync.** Move subscription URLs into the synced data so adding a feed on one device propagates.
- [ ] **A real undo.** Some kind of trash bin that holds deleted items for N days before purging.
- [ ] **Export to .ics.** So I can pull my own data into other calendar apps.

## Built with

This was vibe-coded over a long series of conversations with [Claude](https://claude.ai), mostly from an iPhone. I described what I wanted, Claude wrote it, I drilled on edge cases, and we iterated. I didn't write the code — Claude did. I picked the features, the layout, the constraints, and pushed back on a lot of "wait, that doesn't make sense" moments. The result is what shipped.

Source is intentionally one file because juggling multiple files from a phone is a special kind of suffering. If you want to fork it or improve it, you can do the whole thing in one tab.

## License

MIT. Do whatever you want with it.
