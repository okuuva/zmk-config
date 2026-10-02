# Customization guide

This fork uses a 42-key Corne layout.
The [README](README.md) describes the upstream configuration and build environment; its 34-key layout is not this fork's active keymap.

## Ground rules

- GitHub Actions builds the targets in `build.yaml` on pushes and PRs touching `config/**` or `build.yaml`.
  Enable Actions in a fresh fork before relying on those builds.
- Local builds require the Nix/direnv development environment and a one-time `mise run init` to fetch ZMK, Zephyr and the modules.
  If `just` or `west` is missing, activate the environment with `direnv allow` or run commands through `nix develop --command <cmd>`.
- `config/west.yml` is maintained by [pin-west](https://github.com/urob/pin-west).
  Never hand-edit pinned revisions; run `pin-west bump` instead.
  To add or remove a module, edit its entry and run `pin-west pin`.
- `dts-format` formats devicetree files but not C-preprocessor macros.
  Pass files explicitly; without arguments it recurses over the working directory.
- `just test` is a snapshot-test harness for modules under `modules/zmk/`, not this keymap.
  Validate keymap changes by building firmware.

## Firmware builds

Finish all source edits, including comments, before the final build.
For keymap changes, run `nvim --headless config/base.keymap +QMKFormat +wq` to refresh qmk.nvim formatting and previews, then review and include the generated changes.
Do this before compiling so the user does not need to save the keymap to trigger qmk.nvim afterward.
Run any formatter idempotence checks before compiling too: even a save with identical contents changes metadata and can invalidate mise's freshness check.
The upstream `just draw` recipe and `draw/config.yaml` assume a 34-key layout; they need adapting before they can render this fork's 42-key layout.

Compile firmware through `mise run build`, rather than invoking `just` or `west` directly.
For the default Corne build, omit the target argument to match the `build` dependency used by `mise run flash`.
Use the Nix/direnv development environment; outside it, run `nix develop --command mise run build`.
Mise checks source freshness and the artifacts in `firmware/*.uf2`, not just compiled files under `.build/`.
After the final build and version-control operations, run `mise run --dry-run --verbose flash` and confirm that `build` reports `sources up-to-date, skipping` before reporting firmware ready to flash.
Any later source edit, formatting pass, or save requires repeating the build and freshness check, even if it only changes comments or leaves the version-control diff empty.
If the check still schedules a build, investigate or report the blocker instead of claiming the firmware is ready; keep the freshness checks enabled.

## Layout and new boards

`config/corne.keymap` enables wireless bindings, includes the `zmk-helpers/key-labels/42.h` position header, then includes `base.keymap` and selects the six-column physical layout.
`config/base.keymap` defines full 42-key layers, not upstream's `ZMK_BASE_LAYER` blocks.
Key labels run from `LT0` to `LT5`, `LM0` to `LM5`, `LB0` to `LB5`, and `LH0` to `LH2`, mirrored with `R` on the right; `0` is innermost.
The retained Planck and Glove80 adapters target upstream's layout and are not active build targets.

When adding a board:

1. Identify its ZMK board/shield names from the ZMK docs or vendor configuration.
2. Find its position header under `modules/zmk/helpers/include/zmk-helpers/key-labels/` after initialization.
   If no header matches, follow the [key-label conventions](https://github.com/urob/zmk-helpers/blob/main/docs/key_labels.md#standardization).
3. Create `config/<board>.keymap`, using `corne.keymap` as the entry-point example.
   Adapt the full layer bindings and hold-tap/combo positions if the physical layout differs; the current layers do not adapt automatically.
4. Create `config/<board>.conf` with the board's Bluetooth, display and sleep settings as needed.
   The mouse layer requires `CONFIG_ZMK_POINTING=y`.
5. Register the target in `build.yaml`, using the board/revision syntax supported by the pinned ZMK version.
6. Run `mise run build <name>` and confirm the artifact appears in `firmware/`.
   `just list` lists the available targets.

## Where to change what

| Change | File |
| --- | --- |
| Layers and thumb keys | `config/base.keymap` |
| Layer numbers, shortcuts, HRMs and timing | `config/constants.dtsi` |
| Global Enter combo | `config/constants.dtsi` |
| FTL controls | `config/base.keymap`, `ftl.md` |
| Mouse behavior | `config/mouse.dtsi` |
| Board entry point and physical layout | `config/corne.keymap` |
| Board settings | `config/corne.conf` |
| Build targets | `build.yaml` |
| Task wrappers | `mise-tasks.toml`, `mise-tasks/`, `Justfile` |
| ZMK/module versions | `config/west.yml`, via pin-west |
| Dev environment | `flake.nix`, `nix/`; check with `nix develop` |

`config/combos.dtsi` and `config/leader.dtsi` are retained upstream files, not included by the active keymap.
To remove a feature, remove its bindings, declarations and includes.
If no remaining feature uses its module, remove the module entry from `config/west.yml` and re-pin.
