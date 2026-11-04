# AGENTS.md

## What this is

- A **Ulauncher v3 extension**, not a browser extension. `main.py` is the only entrypoint and runs inside the Ulauncher process.
- The user triggers it with the keyword `mapsr` (preference id `kw` in `manifest.json`), types `<origin> to <destination>`, and the extension opens a Google Maps directions URL.
- Supporting files: `manifest.json` (Ulauncher metadata), `versions.json` (supported API versions), `images/icon.png`.

## Run and verify

- No package manager, build, lint, formatter, or test setup exists. Do not invent one.
- `python3 main.py` does **not** work standalone: imports come from `ulauncher.api.*`, which the Ulauncher runtime provides.
- Syntax-only check: `python3 -m py_compile main.py`, then remove the generated `__pycache__/`.
- Manual testing requires Ulauncher. Load the repo directory as an extension (Ulauncher normally symlinks extensions under `~/.local/share/ulauncher/extensions/`) and reload Ulauncher.

## Behavior and gotchas

- Query parsing is literal and case-sensitive: split on the first `" to "` only (`extract_params` in `main.py`). Incomplete input renders help items; no geocoding or validation is done.
- A successful query returns **two** items: the route and its reverse.
- URLs are built as `https://www.google.com/maps/dir/?api=1&origin=...&destination=...` with `urllib.parse.quote` (spaces become `%20`, not `+`).
- `on_event` catches all exceptions and returns an error result item; preserve this fallback behavior.
- Icon paths are relative to the extension directory (`images/icon.png`); keep them relative.
- `manifest.json` declares `required_api_version: "3"`; `versions.json` points that at `master`, so updates ship directly from the branch with no packaging/release step.
- The `tuple[str, str]` annotation requires Python 3.9+.

## Git

- `origin` is `roorjoan/google-maps-route-extension` (fork); `upstream` is `a3chron/google-maps-route-extension`.
