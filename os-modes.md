# Manual macOS and Windows modes

The keymap starts in macOS mode.
Press `FUN+C` to enable Windows mode, and press it again to return to macOS mode.
The selection lasts until it is toggled again or the keyboard restarts.

## Layers involved

The mode uses three layers defined in `config/constants.dtsi`:

| Layer      | Number | Purpose                                        |
|------------|-------:|------------------------------------------------|
| `WIN_MODE` | 7      | Records whether Windows mode is enabled        |
| `WIN_NAV`  | 8      | Replaces macOS shortcuts while `NAV` is active |
| `WIN_FUN`  | 9      | Replaces macOS shortcuts while `FUN` is active |

`WIN_MODE` is not an alternative base layer.
It is a state-only marker whose bindings are all transparent.

The conditional layers are declared in `config/base.keymap`:

```c
ZMK_CONDITIONAL_LAYER(win_nav, WIN_MODE NAV, WIN_NAV)
ZMK_CONDITIONAL_LAYER(win_fun, WIN_MODE FUN, WIN_FUN)
```

Conceptually, these declarations mean:

```c
if (WIN_MODE is active && NAV is active)
    activate WIN_NAV;
else
    deactivate WIN_NAV;

if (WIN_MODE is active && FUN is active)
    activate WIN_FUN;
else
    deactivate WIN_FUN;
```

ZMK implements this active-layer check in `zmk/app/src/conditional_layer.c`.
It checks layer state, not the bindings stored in those layers.
This is why a fully transparent layer can still be useful as a mode marker.

## Layer state and key lookup are separate

Each ZMK layer has an active or inactive state and a table of key bindings.
Conditional layers inspect the state.
Key lookup inspects the bindings.

When Windows mode is enabled, `&tog WIN_MODE` sets the active bit for layer 7.
The transparent bindings on that layer do not emit any keys.
They only allow key lookup to continue into lower active layers.

The lookup arrows below show priority order, not inheritance between adjacent layers:

```text
macOS + NAV:   NAV → Base
Windows base:  WIN_MODE → Base
Windows + NAV: WIN_NAV → WIN_MODE → NAV → Base
Windows + FUN: WIN_FUN → WIN_MODE → FUN → Base
```

ZMK scans active layers from the highest-numbered layer downward.
It skips inactive layers, so active layers do not need to be adjacent.

## Entering Windows mode

Initially, only Base is active:

```text
Active: Base
```

Holding `FUN` and pressing `C` invokes:

```c
&tog WIN_MODE
```

After `FUN` is released, the active layers are:

```text
Active: Base, WIN_MODE
```

Ordinary typing then resolves like this:

```text
WIN_MODE: transparent, continue
Base:     produce the key, stop
```

The marker changes no bindings by itself.
It records that Windows mode is enabled so conditional layers can react later.

## Using NAV in Windows mode

Holding the NAV layer-tap activates `NAV`:

```text
Active: Base, NAV, WIN_MODE
```

The conditional-layer listener sees that both `NAV` and `WIN_MODE` are active and activates `WIN_NAV`:

```text
Active: Base, NAV, WIN_MODE, WIN_NAV
```

The active layer table is now:

| Layer      | Number | Active |
|------------|-------:|--------|
| `WIN_FUN`  | 9      | No     |
| `WIN_NAV`  | 8      | Yes    |
| `WIN_MODE` | 7      | Yes    |
| `MOU`      | 6      | No     |
| `FUN`      | 5      | No     |
| `NAV`      | 4      | Yes    |
| Base       | 0      | Yes    |

ZMK skips the inactive layers and checks bindings in this order:

```text
WIN_NAV → WIN_MODE → NAV → Base
```

### Windows override example

At the Copy position, `WIN_NAV` contains:

```c
&kp WCOPY
```

`WCOPY` is defined as `LC(C)` in `config/constants.dtsi`.
That binding sends `Ctrl+C` and stops lookup:

```text
WIN_NAV: Ctrl+C, stop
```

The lower layers do not receive that key press.

### Shared NAV binding example

At the Home position, `WIN_NAV` is transparent:

```text
WIN_NAV:  transparent, continue
WIN_MODE: transparent, continue
NAV:      Home, stop
```

This keeps the existing NAV binding shared by macOS and Windows.

If NAV is also transparent at a position, lookup continues again:

```text
WIN_NAV:  transparent, continue
WIN_MODE: transparent, continue
NAV:      transparent, continue
Base:     produce the key, stop
```

A transparent binding passes through one layer at a time.
The lookup loop keeps running while each visited layer returns `ZMK_BEHAVIOR_TRANSPARENT`.
This behavior is implemented by `zmk_keymap_position_state_changed()` in `zmk/app/src/keymap.c` and the transparent behavior in `zmk/app/src/behaviors/behavior_transparent.c`.

## Releasing NAV

Releasing the NAV layer-tap deactivates `NAV`:

```text
Active: Base, WIN_MODE
```

The conditional-layer listener sees that NAV is no longer active and deactivates `WIN_NAV`.
`WIN_MODE` remains active, so the Windows overrides return the next time NAV is held.

`WIN_FUN` follows the same process while FUN is active.
It currently overrides the macOS lock-screen shortcut with `Win+L`.

## Why WIN_MODE is not directly above Base

`WIN_MODE` could be placed directly after Base, and the design would still work.
That ordering matters when a layer contains actual alternative base bindings that should have lower priority than NAV or FUN.

`WIN_MODE` contains no alternative bindings.
Its layer number therefore does not affect the result, as long as the conditional override layers have higher priority than the layers they override.
Keeping it at layer 7 avoids renumbering the existing layers.
