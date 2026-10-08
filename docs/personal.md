# Personal Corne configuration

The personal 42-key layout starts in `config/corne.keymap`.
Upstream's `config/base.keymap`, board adapters, mouse layer, drawing files, and README remain upstream examples, not descriptions of this Corne.
Keep personal changes in the Corne files so upstream updates do not overlap the layout.

## Ownership

- `config/corne.keymap`: shared layers, layer ordering, and embedded qmk.nvim previews.
- `config/windows.dtsi`: Windows shortcuts, OS-mode switching, conditional overlays, and layer bindings with embedded qmk.nvim previews.
- `config/constants.dtsi`, `config/corne-mouse.dtsi`: personal bindings and behaviors, included only by the Corne keymap.
- `config/ftl-constants.dtsi`, `config/ftl.dtsi`, `config/ftl-layers.dtsi`: Corne-only FTL constants, behaviors, and layers with embedded qmk.nvim previews.
- `config/corne.conf`: Corne firmware settings.
- `mise.toml`, `mise-tasks.toml`, `mise-tasks/`: personal tooling around the upstream Just recipes.
- `draw/corne.yaml`: personal keymap-drawer parsing configuration.

Keep shared integration edits limited to the Corne entries in `build.yaml`, Studio dependencies in `config/west.yml` and `flake.nix`, and the full Zephyr toolchain in `.github/workflows/build-nix.yml`.
Retain upstream's other matrix entries and files.
CI builds every matrix entry; the default local mise build selects `corne_` so it does not also build Corneish Zen.
The upstream environment smoke test still builds Planck and draws the upstream layout.

[OS modes](../os-modes.md) and [FTL controls](../ftl.md) document the personal bindings.
The [FTL plan](plans/ftl-comfort-layout.md) and [questionnaire](plans/ftl-comfort-layout.questions.md) retain the design decisions and accepted limitations.
Their historical change IDs are not instructions to rewrite those changes.

## Firmware builds

Use the Nix/direnv development environment and initialize a fresh checkout with `mise run init`.
Outside that environment, prefix mise commands with `nix develop --command`.

Finish all source edits, including comments, before the final build.
For Corne keymap changes, refresh qmk.nvim formatting and previews, then review and include the generated changes:

```bash
nvim --headless config/corne.keymap +QMKFormat +wq
nvim --headless config/windows.dtsi +QMKFormat +wq
nvim --headless config/ftl-layers.dtsi +QMKFormat +wq
```

Run formatter idempotence checks before compiling too.
Even a save with identical contents changes metadata and can invalidate mise's freshness check.

Compile through `mise run build`, without a target argument, matching the dependency used by `mise run flash`:

```bash
nix develop --command mise run build
```

Mise checks source freshness and the exported `firmware/corne_*.uf2` artifacts, not just files under `.build/`.
Its freshness rules target the default Corne build; use `mise run --force build <target>` for other targets or after editing nested ZMK/module sources.

After the final build and version-control operations, check freshness without flashing:

```bash
nix develop --command mise run --dry-run --verbose flash
```

Require `[build] sources up-to-date, skipping` before reporting firmware ready to flash.
Any later source edit, formatting pass, or save requires repeating the build and freshness check, even if it only changes comments or leaves the diff empty.
If a build is still scheduled, investigate or report the blocker and keep the freshness checks enabled.

`mise run flash` is a separate, explicit hardware action on macOS.
It accepts only the expected left/right Corne UF2 filenames, and requires both before prompting for either half.
The dry run does not test device detection or copying.
The existing copy command requires GNU `cp`, and bootloader detection does not verify which half is connected; check the prompt and connect only the requested half.

## Diagrams

`just draw` belongs to the upstream layout and writes its SVGs.
The Corne's embedded qmk.nvim previews are refreshed during formatting above.
For a separate Corne SVG, run keymap-drawer inside the development environment without overwriting upstream drawings:

```bash
keymap -c draw/corne.yaml parse -z config/corne.keymap > /tmp/corne.yaml
keymap -c draw/corne.yaml draw /tmp/corne.yaml > /tmp/corne.svg
```
