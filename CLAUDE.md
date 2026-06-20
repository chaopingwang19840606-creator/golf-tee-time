# CLAUDE.md

Guidance for AI assistants (and humans) working in this repository.

## What this is

A single-page **golf tee-time planner** (高尔夫开球出发安排). Given a tee-off
time, it works backward to tell the user when to **leave home** (出发时间) and
when they will **arrive at the course** (到达球场时间), accounting for travel
time and how early they want to arrive. The UI is in Simplified Chinese.

The entire app is one static HTML file with inline CSS and vanilla JavaScript.
There is **no build step, no framework, no package manager, and no tests.**

## Repository layout

```
index.html   The entire application: markup + inline <style> + inline <script>
golf.jpg     Logo/avatar shown in each day card header (referenced as "golf.jpg")
CNAME        GitHub Pages custom domain — contains "chaopingwang.asia"
```

That's the whole repo. There are no other source files, configs, or directories.

## How it's deployed

- Hosted on **GitHub Pages**, served from the default branch (`main`).
- The `CNAME` file binds the site to the custom domain **chaopingwang.asia**.
  Do not remove or change `CNAME` unless intentionally changing the domain.
- Deployment is automatic: merging/pushing to `main` publishes the site. There
  is no CI pipeline or build artifact — what's in the repo is what ships.

## How the app works (logic in index.html)

All code lives in the IIFE inside the `<script>` tag at the bottom of
`index.html`. Key pieces:

- **Settings** (collapsible panel): `travelMin` (去球场车程, default 50 min) and
  `earlyMin` (提前到达, default 25 min). Changing either recalculates every card.
- **Day cards**: each has a date picker, an hour `select` (5–12) and a minute
  `select` (00–59) for the tee-off time, and two result boxes.
- **Core calculation** (`calc`): converts tee-off time to minutes-of-day, then
  `arrive = round5(teeoff − earlyMin)` and `depart = round5(arrive − travelMin)`.
- **Helpers**: `pad` (zero-pad), `r5` (round to nearest 5 min), `hm`
  (minutes → "H:MM", wrapping past midnight by adding 1440), `fmtDate`,
  `getWd` (weekday name), `nextSat` (next Saturday — the default date).
- **Dynamic cards**: `addDay()` builds card HTML and wires `change`/`input`
  listeners; the global `window.removeDay(id)` deletes a card (the first card
  has no delete button). "新增日期" adds a card defaulting to the day after the
  last card's date. A `counter` variable assigns each card a unique `id` used
  to build element IDs like `date{id}`, `h{id}`, `m{id}`, `a{id}`, `d{id}`.

## Conventions to follow

- **Keep it a single self-contained file.** Do not introduce a build tool,
  bundler, npm dependencies, or external CDN scripts unless explicitly asked.
  Styles go in the existing `<style>` block; logic goes in the existing IIFE.
- **Vanilla JS only**, ES5-compatible style as currently written (`var`,
  `function` declarations, string concatenation for HTML). Match the existing
  terse, minified-ish formatting rather than reformatting the file.
- **UI text is Simplified Chinese.** Keep new user-facing strings consistent
  with the existing tone and terminology.
- **Mobile-first.** The layout targets phones (`max-width:480px`, viewport is
  non-scalable). Test changes at a narrow viewport.
- Element IDs are generated from the per-card `counter`. When adding fields to
  a card, follow the `prefix{id}` naming pattern and register listeners the
  same way existing fields do.
- Times are handled as **minutes since midnight** internally; reuse `r5`/`hm`
  rather than reinventing time math.

## Working in this repo

- **Run/preview locally**: just open `index.html` in a browser, or serve the
  folder (e.g. `python3 -m http.server`) and visit it. No install needed.
- **There is no test suite or linter.** Verify changes manually in the browser:
  add/remove day cards, change settings, and confirm depart/arrive times update
  and round to 5-minute increments.
- **Git workflow**: develop on the assigned feature branch, commit with clear
  messages, and push. Do not push directly to `main` or open a PR unless asked.
