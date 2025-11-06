## Quick context

This repository is a very small static site: a single-page HTML file at `index.html` (no build system, no package.json). The app is a self-contained itinerary page with inline CSS and vanilla JS. Treat edits as direct HTML/CSS/JS changes — there is no bundler step.

## What matters to an AI coding agent

-   Primary source of truth: `index.html` (styles, DOM structure, and client JS all live inline).
-   Accessibility-first patterns: tabs use `role="tab"` + `aria-controls` and panels use `role="region"` and `hidden` attributes. Preserve these when refactoring or moving HTML.
-   Small JS modules are implemented as IIFEs at the bottom of `index.html`. They rely on DOM elements being present (scripts run after markup). If you split code, maintain equivalent load order or use DOMContentLoaded.

## Common structural patterns (examples)

-   Tabs: the `.tabs` container contains `.tab` buttons. Each `.tab` has `aria-controls` pointing to the panel id (example: `aria-controls="panel-itinerary"`). When adding a new tab, create the button and a corresponding panel with the same id and `role="region"`.

-   Schedule rows: the game/practice table uses `tr[data-type="practice|game|meeting"]` and each `td` uses `data-label` to show labels on small screens. Maintain `data-type` and `data-label` when adding rows so responsive CSS and the `hide-practices` toggle keep working.

    Example row to follow when inserting into the schedule tbody:

    <tr data-type="game">
      <td data-label="Date">25 Nov 2025</td>
      <td data-label="Time">12:00</td>
      <td data-label="Type"><span class="badge-type badge-game">Game</span></td>
      <td data-label="Match">USA vs GBR</td>
      <td data-label="Venue">J-Village</td>
    </tr>

-   Session state keys: the page uses `sessionStorage` keys `notesExpanded` and `showPractices`. If you rename or change behavior, update both the HTML control (checkbox/button) and the JS that reads/writes those keys.

## Styling conventions

-   CSS variables are defined in `:root` (e.g., `--accent`, `--card-border`). Reuse these variables for colors to preserve theme consistency.
-   Responsive breakpoints are used at 960px and 640px; tests/visual checks should include narrow/mobile widths to confirm the mobile stacking behavior (table → stacked cards).

## Editing guidance and safety rules

-   Avoid removing inline scripts unless you replicate their behavior. Those IIFE scripts implement the tabs, notes collapse, schedule toggle, and small formatting helpers (date/time formatting).
-   When adding features, prefer adding new HTML/CSS/JS near related code inside `index.html` rather than scattering files — this keeps the single-file, no-build characteristic intact.
-   Keep ARIA roles/attributes and keyboard handlers intact (tabs support ArrowLeft/ArrowRight/Enter/Space). If you refactor, add equivalent keyboard affordances.

## Debugging and testing

-   There is no test harness. Verify changes by opening `index.html` in a browser or serving the folder with a static server (examples):

    -   Python 3: `python -m http.server 8000`
    -   Node (if available): `npx http-server -c-1`

-   Use DevTools to inspect `sessionStorage` keys and to confirm `data-type` filtering (toggle the "Practices" checkbox to confirm rows hide via `.hide-practices`).

## When to split into multiple files

-   If you add complex JS/CSS (reusable components or tests), prefer creating a small `assets/` or `src/` folder and update `index.html` to reference the files. Note: doing so introduces a manual deployment step (no bundler) — document it in README.md if you go that route.

## Files to look at

-   `index.html` — everything (styles, markup, behavior) lives here. Use it as the canonical example for patterns described above.

---

If anything here is unclear or you'd like more/less detail (for example, a suggested test checklist, automated linting, or a migration plan to multiple files), tell me which parts to expand and I'll iterate.
