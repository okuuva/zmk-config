# Firmware builds

Finish all source edits, including comments, before the final build.
For keymap changes, run `nvim --headless config/base.keymap +QMKFormat +wq` to refresh qmk.nvim formatting and previews, then review and include the generated changes.
Do this before compiling so the user does not need to save the keymap to trigger qmk.nvim afterward.
Run any formatter idempotence checks before compiling too: even a save with identical contents changes metadata and can invalidate mise's freshness check.

Compile firmware through `mise run build`, rather than invoking `just` or `west` directly.
For the default Corne build, omit the target argument to match the `build` dependency used by `mise run flash`.
Use the Nix/direnv development environment; outside it, run `nix develop --command mise run build`.
Mise checks source freshness and the artifacts in `firmware/*.uf2`, not just compiled files under `.build/`.
After the final build and version-control operations, run `mise run --dry-run --verbose flash` and confirm that `build` reports `sources up-to-date, skipping` before reporting firmware ready to flash.
Any later source edit, formatting pass, or save requires repeating the build and freshness check, even if it only changes comments or leaves the version-control diff empty.
If the check still schedules a build, investigate or report the blocker instead of claiming the firmware is ready; keep the freshness checks enabled.
