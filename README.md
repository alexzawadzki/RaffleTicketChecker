# 🎟️ Raffle Checker

A dead-simple web app for raffle night: load the ticket numbers you hold, then check called numbers against them as they're drawn. The whole app is a single `index.html` — no build step, no dependencies, no account.

## Features

- **Add tickets** by single number, comma/space-separated lists, or ranges (e.g. `4501-4550, 88, 1203`).
- **Roll colors** — tag tickets by color; "No color" acts as a wildcard that matches any called color.
- **Check calls** — pick the color being called, type the number, and get an instant WINNER / NOT YOURS ticket stub.
- **50/50** — a separate tab for the 50/50 draw, tracked as its own roll of plain numbers with its own check, winners, and history. It never mixes with your main raffle tickets.
- **Winners & history** — winning tickets and every number called so far are tracked automatically.
- **Saves on your device** — tickets persist in the browser's `localStorage`, no sign-in needed.
- **Backup & restore** — copy a backup code to keep your tickets (main raffle and 50/50) safe, or move them to another device, and paste it back to restore.

## Run it

Open `index.html` in any browser, or serve the folder:

```sh
python3 -m http.server
```

There's nothing to install or build.

## Deploy

Hosted as a static site on **GitHub Pages**: Settings → Pages → Deploy from a branch → `main` / root. The file works as-is when served statically.

## How it works

React 18 + Babel are loaded from a CDN and the JSX is transpiled in the browser, so the app stays a single self-contained file with zero tooling. Data lives in `localStorage`; the backup code is just that data encoded as base64 so it can be copied and pasted anywhere.
