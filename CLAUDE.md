# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A "Raffle Checker" — a single-page web app for tracking raffle tickets you hold and checking called numbers against them on raffle night. The entire app lives in **`index.html`**. There is no build step, no package manager, no dependencies installed locally.

## Architecture

Everything is in one file (`index.html`):

- **React 18 + ReactDOM + Babel Standalone** are loaded from CDN (`unpkg.com`) via `<script>` tags. JSX is transpiled **in the browser at runtime** by `@babel/standalone` (the app script is `<script type="text/babel">`). This is deliberate so the file can be served statically with zero tooling.
- **Styling** is a hand-rolled mini utility-class system (Tailwind-like names such as `.flex`, `.rounded-2xl`, `.px-4`) defined in the inline `<style>` block, combined with inline `style={{...}}` objects. The color palette is the `C` object; fonts are Bungee (display) and Archivo (body) from Google Fonts.
- **Persistence** is `localStorage` under the key `raffle-checker-data`. State (`tickets`, `winners`, `history`) is saved on every change via a `useEffect`, and rehydrated through `loadSaved()`. The migration logic lives in `normalizeState()` (old string-array ticket format → `{n, c}` objects; missing `color` field → `null`), shared by both load and backup-import so any saved/pasted blob is coerced into the current shape.
- **Backup/restore** (no account, no server): `encodeBackup()`/`decodeBackup()` serialize the state to a base64 "backup code" the user can copy and paste back later or onto another device. Data is all ASCII (see `normalize`), so plain `btoa`/`atob` is sufficient. Restore **replaces** current state.

### Data model

- A ticket is `{ n: string, c: colorId | null }` where `n` is the normalized number/code and `c` is a color id from `COLORS` (or `null` = "No color").
- `keyOf(t)` produces the dedup/identity key `"<color>|<number>"`.
- `"No color"` is a wildcard: in `matchTicket`, a `null` color on **either** the ticket or the called color matches any color.

### Key functions (all top-level in the script)

- `normalize(raw)` — trims/uppercases input, accepts pure digits (stripped of leading zeros) or alphanumeric codes with dashes; returns `null` if unparseable.
- `parseTicketInput(raw)` — splits on whitespace/commas/semicolons, expands ranges like `4501-4550` (capped at 5000 per range), collects `errors` for unreadable tokens.
- `groupTickets(tickets)` — collapses consecutive numbers per color into range/single groups for compact display.

### Components

`RaffleChecker` (root, holds all state) → `ColorPicker`, `ResultStub` (the animated win/miss ticket stub), `TicketChip`. Two tabs: **Check Calls** and **My Tickets**.

## Running & deploying

- **Run locally:** open `index.html` directly in a browser, or serve the folder (`python3 -m http.server`). No install/build.
- **Deploy:** GitHub Pages, Settings → Pages → Deploy from a branch → `main` / root. The file is designed to work as-is when served statically.
- There are **no tests, linters, or build scripts** in this repo.

## Working in this codebase

- Keep it a single self-contained `index.html` unless explicitly asked otherwise — that's the whole point of the project (zero-tooling static deploy). Don't introduce a bundler, npm, or external files without confirming.
- Because Babel transpiles in-browser, avoid syntax that `@babel/standalone`'s default React preset won't handle; test by loading the page, not by running a compiler.
