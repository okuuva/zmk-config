# Left-hand FTL mode

Use the left Corne half for shortcuts and the trackpad for aiming and movement.
The right half keeps its existing bindings.
Outputs target Windows FTL defaults, except Store, which you must assign to F9 in the game.
Firmware does not change game settings.

## Enter and leave

Hold FUN and tap the normal C position, second from the left on the home row.
Release the entry keys before playing.
This does not change the OS or output selection.

Double-tap LB0, **Exit 2x**, in either system mode or while holding Weapons− to leave keyboard FTL mode.
The second press must start within 200 ms of the first, without another key press between them.
A single tap does nothing.
There is no combat Exit key.
Release held keys after exiting, including Autofire if Ctrl is down.
Nothing flashes or exits the game automatically.

## Thumbs

Every FTL view has the same three thumbs, left to right:

| Thumb | Tap | Hold |
| --- | --- | --- |
| LH2 | Systems− | Weapons− |
| LH1 | Cancel extra mode, then Pause | No alternate action |
| LH0 | Systems+ | Utilities |

The selector threshold is 200 ms.
A short thumb tap selects Systems±, including a roll where the finger goes down before the thumb comes up.
Release the thumb before that finger releases and before 200 ms to keep the system tap.
Holding the thumb for 200 ms, or completing a finger tap while the thumb stays down, selects Weapons− or Utilities.
Until that decision, the first finger action waits rather than selecting the held view immediately.
Release a held layer thumb before Pause or the other selector; no simultaneous thumb presses are required.

Pause sends Space once on its downstroke after cancelling extra FTL state.
LT5 cancels Systems± or Utilities without sending a game key.
Weapons− also cancels when LH2 is released; Utilities cancels when LH0 is released.
Keyboard input on the right requests cancellation before continuing through the existing right-hand bindings.
With an unrelated delayed typing macro running, cleanup can arrive too late; that use is outside the play scope below.
The native helper also treats right-key releases as interruptions.
Trackpad activity does not refresh or cancel a mode.

## Combat

Rows run LT5 to LT0, LM5 to LM0, and LB5 to LB0.
A dash is a no-op, not a transparent typing key.

```text
Settings Autofire Cloak    Mind    Hack        —
—        Weapon1  Weapon2  Weapon3 Weapon4     Crew home
—        Drone1   Drone2   Drone3  Recall/Send —

                  Sys−/W−  Pause   Sys+/Utils
```

Settings sends Escape; Crew home sends Return.
Weapons send 1–4 and drones send 5–7.
Cloak, Mind, and Hack send C, M, and N.

The three adjacent weapon pairs use a 30 ms combat-only combo window:
- Weapon1 + Weapon2: Open doors, Z.
- Weapon2 + Weapon3: Close doors, X.
- Weapon3 + Weapon4: Jump, J.

A near miss can select a weapon instead; rolling between adjacent weapons can trigger a combo.
Cloak, Hack, and Battery are not combo constituents.

### Autofire and teleport gestures

Autofire toggles with one V on a short tap.
Hold it uninterrupted for 150 ms to hold Left Ctrl for momentary autofire reversal.
Release the finger to release Ctrl.
Select a weapon first, then hold Autofire and use the trackpad.

Recall/Send emits one R on a short tap, or one T after an uninterrupted 150 ms hold.
If another keyboard key goes down before either gesture resolves, it commits the tap once and lets that key continue.
Thus early Pause produces V then Space, or R then Space, without waiting out the remaining gesture threshold.
Keeping the finger down after that decision does not produce a later hold or a second tap on release.
Trackpad clicks do not resolve these keyboard gestures.

Already-active Ctrl stays held until Autofire is released, even across Pause or mode cancellation.
Release it before commands that must be unmodified.
Other pending typing behaviors or queued macros can still delay event delivery; these gestures do not make the entire keyboard event path atomic.

## Systems±

Tap-release LH0 for Systems+ or LH2 for Systems−.
Both use the same map; Systems− holds explicit Left Shift between adjustments.

```text
Cancel Battery Cloak     Mind      Hack     —
—      Shields Engines   Med/Clone O2       Weapons
—      —       —         Artillery Teleport Exit 2x

                Sys−/W−   Pause     Sys+/Utils
```

System outputs are Shields A, Engines S, Med/Clone D, O2 F, Cloak H, Mind K, Hack L, Artillery Y, Teleporter G, and Weapons W.
Systems− therefore sends Shift with those keys, including Shift+W at LM0.
Battery cancels the current mode and releases its owned Shift before sending B, then selects Systems+.
You can distribute the extra power without tapping Sys+ again; the usual Systems+ timeout still applies.
New modes replace old modes; cancelling or releasing a held mode does not restore an older one.

### Accepted timer limitations

The idle timeout is configured to 600 ms, but the installed tri-state helper does not reliably restart it in every case.
Re-selecting a mode or cancelling and re-entering can retain an earlier deadline and shorten the new session.
Releasing one finger can also start expiry while another finger remains held.
After expiry, the same position can perform a combat action rather than a power adjustment.
Do not depend on an exact 600 ms grace period or on holding a finger to prevent expiry.
Use Pause or LT5 Cancel to return to combat explicitly.

These limitations were accepted in Q31 of the plan's questionnaire.
A TODO in `config/ftl.dtsi` records the proper timer/held-key fix; no helper source or dependency was changed.

## Weapons−

Hold LH2 for 200 ms, or keep it held through a complete adjustment-finger tap.
Release it to return to combat.
Shift stays down between adjustments until the mode ends.

```text
— —        —        —        —        —
— Weapon1− Weapon2− Weapon3− Weapon4− Weapons−
— Drone1−  Drone2−  Drone3−  Drones−  Exit 2x

                    Sys−/W−  Pause    Sys+/Utils
```

Individual controls use Shift+1–7.
LM0 sends Shift+W and LB1 sends Shift+E using the native bank shortcuts you verified.
No repeated-key bank macro is added.
Shift also affects trackpad clicks while a depower mode is active.
Release adjustment fingers before leaving the mode.

## Utilities

Hold LH0; release it to return to combat.
Entering Utilities clears previous mode-owned Shift.

```text
Cancel Crew5/F5 Crew6/F6     Crew7/F7    Crew8/F8 Ship/U
—      Crew1/F1 Crew2/F2     Crew3/F3    Crew4/F4 Inventory/I
—      All/Q    Text smaller Text larger Save2x   Shop/F9

                             Sys−/W−    Pause    Sys+/Utils
```

Save crew requires two taps within 200 ms and sends Slash once.
A single tap does nothing.
Text smaller sends Equals; Text larger sends Minus, using FTL's native event-text sizing.
There is no dedicated Crew manifest key or required F10 assignment.

## Play scope

Q32 confirms left-hand controls and the trackpad, without right-half typing during play.
All FTL macros use zero programmed delays, including their mode-start and cleanup actions.
The tap/hold and double-tap timers do not occupy the macro queue; neither does holding a layer thumb.

A stress test injected a separate 300 ms delayed macro and found wrong-map actions, late Shift cleanup, and activation after right-hand interruption.
No FTL-only action has been shown to create that backlog.
Those tests remain evidence about mixing delayed typing macros with FTL, not a reproduced failure of the intended play pattern.
Do not run unrelated delayed typing macros during play; revisit this restriction if such macros are added to the FTL controls.

## Implementation and verification

`config/ftl.dtsi` contains the FTL-only behaviors.
Three transparent signal layers end the active tri-state or a particular held mode; they are not extra persistent user modes.
Mode-specific release signals prevent a cancelled thumb's later release from cancelling a newer mode.
Cleanup releases only the Shift press owned by that mode.

Native ZMK checks exercise mode transitions, modifier balance, late thumb releases, early finger rolls, merged gestures, combos, and protected Save/Exit.
The plan records local test evidence and the accepted timer failures.
Build validation does not establish ergonomic comfort or game-setting correctness.
Flashing and in-game testing remain separate user actions.
