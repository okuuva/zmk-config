# Manual macOS and Windows modes

The keymap starts in macOS mode.
Hold `FUN` and press the leftmost thumb key to enable Windows mode and prefer USB output.
Repeat the chord to return to macOS mode and prefer Bluetooth output.
Neither direction changes the Bluetooth slot or remembers the previous connection type.
Any slot changes made in Windows mode remain in effect.

Two native ZMK macros in `config/corne.keymap` handle the switch.
`FUN` binds `&win_usb`; the same key on `WIN_FUN` binds `&mac_ble`.
Each toggles `WIN_MODE` and selects its output preference.
ZMK can fall back to the other output if the preferred connection is unavailable.

Windows mode resets to macOS on restart.
ZMK persists output preference normally, so restarting in Windows mode may retain the USB preference.

## Modifier placement

Windows mode keeps Ctrl on the existing outer home-row positions and adds Ctrl to the former GUI positions.
GUI moves to the two innermost home-row positions.

| Position | Tap on Base | Windows hold |
| --- | --- | --- |
| `LM4` | `C` | Left Ctrl |
| `LM1` | `A` | Left Ctrl |
| `LM0` | `,` | Left GUI |
| `RM0` | `K` | Right GUI |
| `RM1` | `H` | Right Ctrl |
| `RM4` | `S` | Right Ctrl |

The Symbols and Numbers layers preserve the same modifier positions while retaining their own tap keys.
The Mouse layer uses Left Ctrl on `LM1` and dedicated GUI keys on `LM0` and `RM0`.
Layer-specific actions still win where a position is used for navigation, media, or another function.

## Layers involved

The layer numbers in `config/constants.dtsi` match their definition order in `config/corne.keymap`:

| Layer | Number | Purpose |
| --- | ---: | --- |
| Base | 0 | macOS typing bindings |
| `WIN_MODE` | 1 | Windows Base bindings and Windows-mode state |
| `SYM` | 2 | Shared Symbols layer |
| `NUM` | 3 | Shared Numbers layer |
| `PAD` | 4 | Shared Numpad layer |
| `NAV` | 5 | macOS Navigation layer |
| `FUN` | 6 | macOS Function layer |
| `MOU` | 7 | Shared Mouse layer |
| `WIN_SYM` | 8 | Windows Symbols modifier overrides |
| `WIN_NUM` | 9 | Windows Numbers modifier overrides |
| `WIN_MOU` | 10 | Windows Mouse modifier overrides |
| `WIN_NAV` | 11 | Windows Navigation shortcuts and modifiers |
| `WIN_FUN` | 12 | Windows Function shortcuts |

`WIN_MODE` now has two jobs.
Its active bit records that Windows mode is enabled, and its sparse bindings provide the Windows Base modifier layout.
All unaffected positions remain transparent.

## Conditional overlays

The conditional layers are declared in `config/corne.keymap`:

```c
ZMK_CONDITIONAL_LAYER(win_sym, WIN_MODE SYM, WIN_SYM)
ZMK_CONDITIONAL_LAYER(win_num, WIN_MODE NUM, WIN_NUM)
ZMK_CONDITIONAL_LAYER(win_mou, WIN_MODE MOU, WIN_MOU)
ZMK_CONDITIONAL_LAYER(win_nav, WIN_MODE NAV, WIN_NAV)
ZMK_CONDITIONAL_LAYER(win_fun, WIN_MODE FUN, WIN_FUN)
```

Each declaration activates its Windows overlay only while Windows mode and the corresponding shared layer are both active.
For example:

```text
WIN_MODE off + NAV active → NAV
WIN_MODE on  + NAV active → WIN_NAV and NAV
```

ZMK implements this active-layer check in `zmk/app/src/conditional_layer.c`.
It checks layer state independently from key bindings.

## Key lookup

ZMK scans active layers from the highest-numbered layer downward.
It skips inactive layers and continues downward whenever a binding is transparent.
The arrows below show lookup priority, not inheritance between adjacent layers:

```text
macOS Base:      Base
Windows Base:    WIN_MODE → Base
macOS + NAV:     NAV → Base
Windows + NAV:   WIN_NAV → NAV → WIN_MODE → Base
Windows + SYM:   WIN_SYM → SYM → WIN_MODE → Base
Windows + NUM:   WIN_NUM → NUM → WIN_MODE → Base
Windows + MOU:   WIN_MOU → MOU → WIN_MODE → Base
Windows + FUN:   WIN_FUN → FUN → WIN_MODE → Base
```

A layer appears in these chains only when it is active.

### Windows shortcut example

At the Copy position, `WIN_NAV` contains:

```c
&kp WCOPY
```

`WCOPY` is defined as `LC(C)` in `config/constants.dtsi`.
That binding sends `Ctrl+C` and stops lookup, so the macOS `COPY` binding on NAV is never reached.

### Shared binding example

At the Home position, `WIN_NAV` is transparent:

```text
WIN_NAV: transparent, continue
NAV:     Home, stop
```

The shared NAV binding handles the key.

At a position where both `WIN_NAV` and NAV are transparent, lookup continues into `WIN_MODE` and then Base:

```text
WIN_NAV:  transparent, continue
NAV:      transparent, continue
WIN_MODE: Windows Base binding or transparent
Base:     fallback binding
```

The lookup loop is `zmk_keymap_position_state_changed()` in `zmk/app/src/keymap.c`.
The transparent behavior in `zmk/app/src/behaviors/behavior_transparent.c` tells that loop to continue.

## Why WIN_MODE follows Base

`WIN_MODE` used to be a fully transparent state marker, so its layer number did not affect key output.
It now contains real Base bindings for `LM0`, `LM1`, `RM0`, and `RM1`.

If `WIN_MODE` remained above the functional layers, those Base bindings would mask their keys.
For example, the `RM0` GUI/K binding could replace Fullscreen on NAV, `N6` on NUM, or `+` on PAD.

Placing `WIN_MODE` directly above Base gives functional layers priority:

```text
Conditional Windows overlay
Shared functional layer
WIN_MODE
Base
```

A functional binding wins when present.
A transparent functional position falls through to the Windows Base binding.
This ordering avoids adding protective copies of every functional layer.

## Leaving a layer

Releasing a shared layer deactivates its conditional Windows overlay.
For example, releasing NAV deactivates `WIN_NAV` while leaving `WIN_MODE` active.
The Windows overrides return the next time NAV is held.
