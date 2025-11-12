# Repository Guidelines

## Project Structure & Module Organization
Top-level directories mirror a standard ZMK config: `boards/shields/corne/` holds the lone keymap, `config/west.yml` pins upstream ZMK/Zephyr modules, `zephyr/module.yml` exposes this repo as an overlay, and `build.yaml` drives the GitHub Actions matrix for the left, right, and settings-reset builds. Keep new shields under `boards/shields/<shield>/`, share reusable behavior snippets via DTS files nearby, and leave generated `build/` content untracked.

## Build, Test, and Development Commands
Initialize and refresh dependencies with `west init -l config && west update`. Build each half explicitly, e.g. `west build -s zmk/app -d build/corne_left -b nice_nano_v2 -- -DSHIELD=corne_left`, then re-run with `corne_right`. Produce the reset firmware via `west build -d build/reset -b nice_nano_v2 -- -DSHIELD=settings_reset`. From any build dir, `west build -t flash` copies the UF2 to a connected nice!nano. Update `build.yaml` whenever targets change so CI matches local expectations.

## Coding Style & Naming Conventions
Device-tree sources (see `boards/shields/corne/corne.keymap`) use four spaces for indentation, uppercase macro names (`DEFAULT`, `PUNC`), and descriptive labels such as `td_ctrl_punc`. Keep ASCII diagrams or block comments above each layer to clarify intent. Prefer `//` for inline notes, reserve `/* ... */` for multi-line context, and give new behaviors `lower_snake_case` labels plus a short comment describing tap/hold semantics.

## Testing Guidelines
There is no automated unit test suite; compile-time success is the main safety net. Always run both halves locally and glance at `build/<target>/zephyr/zephyr.uf2` sizes for sanity. When altering behaviors or layers, add a `west build -t pristine` cycle to ensure DTS changes work from a clean slate. Mirror new board/shield combos in `build.yaml` so CI covers the same surface.

## Commit & Pull Request Guidelines
History shows short, lowercase summaries (`fix`, `qwerty test`), so keep subjects under 50 characters, imperative, and expand in the body only when needed. Each PR should state the motivation, list affected shields, call out new behaviors/macros, and mention how you verified the firmware (command + target). Link issues and drop screenshots or diagrams when they help reviewers visualize the layout shift.

## Security & Configuration Tips
Treat `config/west.yml` as the source of truth for upstream revisions; bump modules intentionally and call out Zephyr/ZMK hash changes in the PR. Never commit BLE keys or personal profiles. Prototype major layout experiments in a temporary shield or overlay, then merge only the finalized variant.
