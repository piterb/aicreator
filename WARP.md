# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Project overview

This repository contains a single-page "AI Video Creator – Foundation Kit" meant as a cozy, beginner-friendly guide for creating AI-generated films. The app is a static HTML page with inline CSS and JavaScript that:

- Presents a hero section with quick links to external tools (e.g., Runway ML and Runway Academy).
- Shows a grid of "Prompt System" tiles that represent creative control dimensions (Camera & Motion, Lighting, Mood / Emotion, Style / Aesthetic, Scene Dynamics, Cinematic Quality).
- When a tile is clicked, reveals a panel with ready-to-copy prompt snippets tailored to that dimension.

There is no build system, bundler, or test setup. Everything runs directly in the browser from `index.html`.

## Tech stack and structure

- Pure static front-end:
  - `index.html` is the only code file and contains HTML, CSS, and JavaScript.
  - Styling is defined inline via a `<style>` block in the `<head>`; there are no external CSS files.
  - Interactivity is handled with vanilla JavaScript in a `<script>` block at the bottom of the page.
- Assets:
  - `aiqr.png` is used in the hero "card" as a QR-based visual entry point.
  - `postcard.png` is an additional visual asset (not currently referenced in the HTML source as of the initial scan).

### Prompt system architecture (JavaScript)

All prompt content is defined in a single constant, `PROMPTS`, near the bottom of `index.html`:

- `PROMPTS` is an object whose keys are category identifiers (e.g., `camera`, `lighting`, `mood`, `style`, `dynamics`, `cinematic`).
- Each category entry has:
  - `title`: Display name for the panel heading.
  - `subtitle`: Explanatory text shown under the heading.
  - `items`: An array of prompt objects, each with:
    - `label`: Short descriptor (e.g., "Template", "Film Finish").
    - `text`: The actual prompt text copied to the clipboard.

The interaction flow:

1. The page renders a grid of `.tile-btn` buttons, each with a `data-key` attribute that matches a key in `PROMPTS`.
2. On load, the script:
   - Selects the prompt panel elements (`promptPanel`, `panelTitle`, `panelSubtitle`, `panelBody`, `panelClose`).
   - Attaches a `click` handler to every `.tile-btn` that calls `render(btn.dataset.key)`.
3. The `render(key)` function:
   - Looks up `data = PROMPTS[key]`; if missing, it aborts.
   - Updates the panel title and subtitle from `data`.
   - Clears `panelBody` and repopulates it with a series of `.prompt` blocks, one per `items` entry.
   - For each item, creates a "Copy" button that writes `it.text` to the clipboard using `navigator.clipboard.writeText` and provides basic success/error feedback by changing the button label.
   - Shows the panel (adds the `show` CSS class) and scrolls it into view.
4. The "Close" button simply removes the `show` class from the panel to hide it.

This design centralizes all prompt copy in one place (`PROMPTS`) and keeps the HTML structure generic.

## Running and developing locally

There is no dedicated build, lint, or test tooling configured in this repository. Development is done directly against `index.html`.

Common ways to work with the page:

- **Open directly in a browser (macOS):**
  - From the repo root: `open index.html`
- **Serve via a simple HTTP server (helps mimic real hosting and avoids clipboard/security quirks in some browsers):**
  - Using Python 3: `python3 -m http.server 8000`
  - Then open `http://localhost:8000/index.html` in your browser.

There are currently no automated tests, so manual verification consists of:

- Loading the page and confirming layout and styling look correct on desktop and mobile viewport sizes.
- Clicking each prompt tile and verifying the panel shows the correct title, subtitle, and prompt list.
- Using the "Copy" buttons and confirming the clipboard contents match the displayed prompt text.

## Extending or modifying the prompt system

When editing or extending this app, prefer to keep the prompts and UI wiring consistent with the existing structure:

- To add a new creative control category:
  - Add a new key to `PROMPTS` with `title`, `subtitle`, and `items`.
  - Add a matching `.tile-btn` in the HTML grid with `data-key` equal to the new key.
- To add or tweak prompts within an existing category:
  - Edit the relevant `items` array in `PROMPTS`.
- To change how prompts render (layout, styling, copy behavior):
  - Adjust the DOM creation logic in the `render` function and the associated CSS classes (e.g., `.prompt`, `.copy-row`, `.btn`).

Because everything is inline in `index.html`, be careful to keep sections (HTML structure, CSS, and JS) in sync when making structural UI changes.