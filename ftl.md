# Left-hand FTL mode

Use the left Corne half for shortcuts and the trackpad for aiming, crew selection and movement.
The right half keeps its existing bindings.

## Enter and leave

Hold `FUN`, the top-left key, and tap the normal `C` position, second key from the left on the home row.
Release both keys to play.
This works in either OS mode and does not change the OS or output selection.

To leave, hold **mo Power+** or **mo Power-**, then double-tap **LB0**, the innermost bottom-row key, labeled **Exit 2x**.
Start the second press within 200 ms of the first, keeping the power thumb held.
A single tap does nothing.
Release the keys afterward to return to normal typing.
This leaves keyboard FTL mode, not the game.

## Combat

```text
Settings      Send      Recall    Cloak     Mind      Hack
mo Autofire   Weapon 1  Weapon 2  Weapon 3  Weapon 4  Crew home
tog Autofire  Battery   Open      Close     Jump      Ship menu

                             Pause     mo Power- mo Power+
```

Settings sends `Escape`, and Pause sends `Space`.
`mo Autofire` sends `Left Alt` for momentary autofire reversal, and `tog Autofire` sends `Left Ctrl` to toggle it.
Crew home sends `Tab`, and Jump sends `V`.
Ship menu sends the default `U` for ship upgrades and equipment.
Save crew positions using the on-screen icon; the left-hand FTL layer deliberately has no Save crew key.
Cloak, Hack, Mind and Battery send `C`, `N`, `M` and `B`.
Open and Close send `Z` and `X` for all doors; Send and Recall send `T` and `R` for the teleporter.
Weapons send `1` through `4`.
All combat keys are immediate, with no tap/hold behavior.

## Power management

Hold the inner thumb for **mo Power+**, or the middle thumb for **mo Power-**.
Release it to return to combat.
The outer thumb pauses in both combat and power management.
Both power thumbs activate one shared layer, matching the combat keys for weapons, cloaking, mind control and hacking.
Both Send and Recall positions control teleporter power.

```text
Drone 1   Teleport  Teleport  Cloak     Mind      Hack
Drone 2   Weapon 1  Weapon 2  Weapon 3  Weapon 4  Artillery
Drone 3   Shields   Engines   Med/Clone O2        Exit 2x

                          Pause     mo Power- mo Power+
```

The double-tap exit is available while holding either power thumb.

Release the current power thumb before pressing the other.
If the holds overlap, releasing either thumb deactivates the power layer even while the other is held, so subsequent keys can trigger combat actions instead.
Release both thumbs and press the desired one again to recover.

mo Power+ sends the default bindings: drones `5` through `7`, shields `A`, engines `S`, oxygen `F`, medbay or clone bay `D`, artillery `Y`, teleporter `G`, hacking `L`, mind control `K`, and cloaking `H`.
mo Power- holds Left Shift from thumb press to thumb release, including between repeated system taps.
This sends Shift before the system key instead of relying on FTL to recognize both arriving together.
There is no home-row-mod delay and no separate Shift hold is needed.
Release the system keys before releasing mo Power-.
Shift also applies to trackpad clicks and right-hand keys while mo Power- is held.
Store access and individual crew selection remain on the trackpad.
