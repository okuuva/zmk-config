# FTL layout for reduced finger reach

Status: Q32 confirms left-hand-only play without delayed typing macros; implementation proceeds with the accepted timer limitations and final validation/reviews.
Local gated implementation is authorized; flashing and publication are not.

## Intent and one-change boundary

Reduce repeated bottom-row reaches without requiring simultaneous thumb presses.
Put the frequent combat actions, weapon depowering, and common system adjustments in `LT1`–`LT4`, `LM1`–`LM4`, and the left thumbs.
Keep Pause on `LH1`.
The user reports that the current shared power layer works; this is an ergonomic redesign, not a depowering bug fix.
Reduced fatigue is an unmeasured hypothesis, not a claimed result.

Owning jj change: `kvloqwntmwqmnyzvkvtwysxzxzrkkyup`, based on the verified shared-power change `pwmtwkkqlqsknwkxmusvznwlvpuptsny`.
Keep planning and the eventual approved implementation in this one change.
Do not squash into the shared-power or original FTL change.
If work is in an empty child, reconcile it with this owning change before `/implement`; do not silently create a second implementation change.

## Evidence and decisions

- [Current bindings](../../config/base.keymap), [constants](../../config/constants.dtsi), and [FTL guide](../../ftl.md) establish the existing game keycodes, explicit held-Shift depowering, entry, and double-tap exit.
- `modules/zmk/helpers/include/zmk-helpers/key-labels/42.h` establishes left-to-right positions `LT5 LT4 LT3 LT2 LT1 LT0`, likewise for home/bottom rows, and thumbs `LH2 LH1 LH0` at indices 36–38.
- [Answers Q1–Q9](ftl-comfort-layout.questions.md) establish that Hack, Cloak, Mind, doors, Crew home, Jump, momentary autofire, and system adjustment are frequent.
Battery activation is somewhat common; teleport controls, drones, and toggled autofire are rare; artillery adjustment is very rare.
- No two thumb keys may be required together, including a layer selector and Pause.
Adjacent home-row combos are comfortable, especially `LM1+LM2` and `LM2+LM3`.
Hack, Cloak, and Battery activation keys must not be combo constituents.
- The user selects a weapon before holding momentary autofire and targeting with the trackpad.
A finger-held autofire modifier therefore need not leave all four weapon keys simultaneously accessible.
- Q5 accepts trying timed multi-key modes with a 600 ms idle interval, retained Shift during depowering, and stopping if the helper fails verification.
Q11 clarifies that Pause should cancel power state and return to combat, provided a separate cancel gesture also exists.
Trackpad activity does not extend the keyboard timeout and remains affected by held Shift until cleanup.
The user later accepts the demonstrated expiry while another finger stays held; other timeout and cleanup requirements remain.
- Q15 now requests vanilla game defaults, superseding the old custom action and menu assignments.
Q17 accepts small custom exceptions for actions without defaults.
Q19 sets Windows as the target, with possible future Linux use; use the corroborated PC autofire modifier, Left Ctrl.
No game settings will be changed by this task.
- `zmk/app/src/combo.c`, `setup_candidates_for_first_keypress()`, checks the highest active layer and captures candidate presses.
Combat-only combos can be excluded from power layers even though the FTL combat layer remains active underneath.
- `config/combos.dtsi` is not included by the current keymap.
The active global Enter combo is in `config/constants.dtsi`; preserve it and do not enable the legacy combo collection.
- [Build tasks](../../mise-tasks.toml), [Justfile](../../Justfile), and [firmware instructions](../../AGENTS.md) establish the formatting, build, and freshness procedure.
The build wrapper counts combos from the inactive legacy file, so validate actual compiled combo capacity rather than assuming that scan covers new declarations.

## Layout revision after Q10

Q13 rejects LH1 hold because Pause must fire on the downstroke.
Q14 accepts the merged combat gestures but questions the 200 ms hold delay.
Q15 accepts double-tap Save crew and requests vanilla defaults for all FTL game outputs.
Q16 rejects finger-held layer switches; only thumb-held layers are acceptable.
Q17 approves small custom menu exceptions, Q18 confirms native event-text resizing, and Q19 selects Windows with possible future Linux.
Q20 accepts the thumb/Cancel arrangement and asks how cancellation works in each power mode.
Q21 rejects the non-adjacent Jump chord, Q22 accepts F9/F10, and Q23 requests a clearer Pause explanation without accepting a delay or another gesture flavor.
Q24 now requests an immediate toggle-plus-Pause when Autofire is undecided.
Q25 selects Open / Close / Jump on the three adjacent home-row pairs and asks to see every layer before placing Crew home.
Crew home is combat LM0 only; system/utility Cancel is LT5; Q27 accepts early-tap interruption for both merged gestures.
Round 11 answers settle Inventory at utility LM0, Shop at LB0, All crew at LB4, and removal of the dedicated Crew-menu key.
They also accept plain combat Settings, confirm the user's own native bank-shortcut check, and permit retaining shared Systems− W.
[Round 12](ftl-comfort-layout.questions.md#round-12-final-candidate) is approved by Q30, with its dedicated combat LB0 Cancel removed.

### Requested physical direction

- LH1 is dedicated Pause-and-cancel on press, with no alternate hold action.
Do not send Space on release as a substitute.
- Combat LT4 combines tap-toggle and held momentary autofire.
Cloak → Mind → Hack remains LT3/LT2/LT1; their power controls align with those positions.
Both system maps have Battery at LT4.
- Combat drones 1–3 and drone depowering are grouped at LB4/LB3/LB2 in their respective maps.
Weapon controls remain LM4/LM3/LM2/LM1.
- Merge Recall on tap and Send on hold at LB1 in combat.
Q30 approves that position.
- Add crew selection F1–F8 and text-size controls to Utilities.
Q18 accepts native event-text resizing, not whole-UI/browser zoom.
- Utilities uses LT5 Cancel, LT0 Ship menu, LM0 Inventory, LB0 Shop, and LB4 All crew.
Its LM5 and LB5 positions are empty after moving Inventory and removing the unused Crew-menu key.
Keep the eight individual crew selectors in the central top/home positions.
- Save crew on Utilities LB1 is protected by double-tap within 200 ms.
Single tap does nothing; double-tap saves once.
All-crew selection is Utilities LB4.
Crew home is now assigned to combat LM0 only, sending Return.

### Accepted utility access and cancellation

The finger-held LB0 proposal is rejected.
Q20 accepts LH0 tap Systems+ / hold Utilities, replacing its single/double-tap selector.
LH2 remains tap Systems− / hold Weapons−, and LH1 remains dedicated downstroke Pause.
Use separate ordinary 200 ms balanced thumb behaviors with no typing quick-tap/prior-idle shortcuts.
The post-play misclick fix replaces the original hold-preferred rule.
A finger may go down before the thumb releases: releasing the thumb first, before 200 ms, still selects Systems±.
Completing that finger tap while the thumb stays down, or reaching 200 ms, selects Utilities on LH0 or Weapons− on LH2.
The finger action waits until the decision rather than choosing hold on its downstroke.

The latest answers keep Settings as plain Escape on combat.
No conditional Settings/cancel macro is needed: active system modes and Utilities already bind LT5 to Cancel.
When those modes end, their owned state must be fully cleaned up before combat becomes usable.
That invariant belongs to the existing runtime gate, not to a second cleanup path hidden in Settings.

| Active view | Cancel route | LT5 output | LB0 role |
| --- | --- | --- | --- |
| Combat | LH1 Pause-and-cancel | Escape only | No action |
| Systems+ | LT5 | Cleanup only, no game key | Exit 2x only |
| Systems− | LT5 | Cleanup including owned Shift, no game key | Exit 2x only |
| Weapons− | Release LH2 | No action | Exit 2x only |
| Utilities | LT5 or release LH0 | Cleanup only, no game key | Shop F9 |

Exit on all power views keeps the 200 ms double-tap protection.
A single tap does nothing.
Cancel has no double-tap recognition wait; there is no combat Exit.
Systems± retain the 600 ms idle contract.
Weapons− ends on LH2 release; Utilities ends on LH0 release.
These are specified semantics, not a claim that the helpers have passed the runtime gate.

Entering Utilities replaces the previous extra mode and clears its owned Shift before a utility action.
Releasing LH0 returns to combat without restoring a previous mode.
Release a held layer thumb before Pause or another selector; no simultaneous thumb presses are required.
If LT5 or right-hand input cancels a held mode early, its later release must not affect a newer mode or resurrect the old one.

Cancellation does not release every physically held key.
Under the accepted Autofire rule, already-active Ctrl remains held until its finger releases.
Release it before Settings or other commands that must be unmodified.
An undecided Autofire/Recall gesture instead commits its accepted early tap before an interrupting Settings press.

```text
┌──────────────┬──────────────┬──────────────┬──────────────┬──────────────┬──────────────┐
│    Cancel    │ Crew 1 / F1  │ Crew 2 / F2  │ Crew 3 / F3  │ Crew 4 / F4  │  Ship menu   │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │ Crew 5 / F5  │ Crew 6 / F6  │ Crew 7 / F7  │ Crew 8 / F8  │  Inventory   │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │   All crew   │ Text smaller │ Text larger  │   Save 2x    │  Shop / F9   │
└──────────────┴──────────────┴──────────────┼──────────────┼──────────────┼──────────────┤
                                             │  Sys− / W−   │ Pause/Combat │ Sys+ / Utils │
                                             └──────────────┴──────────────┴──────────────┘
```

Inventory moves to LM0, and its old LM5 binding is cleared.
Shop is LB0; Ship menu stays LT0.
All crew Q is accepted at LB4.
Crew manifest has no dedicated key, so LB5 is empty and no F10 assignment is required.
Crew selection remains in the eight central positions; protected Save is LB1.
Text smaller at LB3 emits EQUAL; Text larger at LB2 emits MINUS.
Crew home remains combat LM0 only.
The approved five views and transition contract are in [Round 12](ftl-comfort-layout.questions.md#round-12-final-candidate).
Q30 removes the redundant combat LB0 Cancel; that position is now empty.

### Weapon and drone bank controls

The user reports personally checking the native W, Shift+W, and Shift+E shortcuts and explicitly asks to stop the extra lookup.
Accept that check as the evidence for the intended in-game bank actions.
The independent lookup was stopped without a completed result; do not present it as an independent verification pass.
This removes the research blocker but does not replace the later firmware output/modifier tests.

Systems+ LM0 sends W.
Weapons− LM0 sends W under the mode's held Shift, and LB1 similarly sends E.
Individual weapon/drone number controls remain.
Send the native bindings as requested; do not add an automatic repeated-key sequence.

Systems± keep a shared physical map, so Systems− LM0 also sends Shift+W.
Q29 explicitly permits retaining that extra binding when simpler than removing it.
Use that permission: retain the shared W binding rather than add a suppression behavior or duplicate system layer.

### Final transition contract

Q30 approves these transitions with combat LB0 cleared; the subsequent `/implement` request authorizes the gated work.

| Trigger | Result |
| --- | --- |
| LH2 short tap, released before 200 ms and before an overlapping finger releases | Replace the old extra mode with Systems−; hold owned Shift; request a 600 ms interval, subject to the accepted stale-deadline limit |
| LH2 held for 200 ms, or a complete finger tap while LH2 remains down | Replace the old extra mode with held Weapons−; hold owned Shift |
| Release LH2 after its Weapons− hold | Return to combat and release that mode's Shift; do not restore an older mode |
| LH0 short tap, released before 200 ms and before an overlapping finger releases | Replace the old extra mode with Systems+; remove old mode-owned Shift; request a 600 ms interval, subject to the accepted stale-deadline limit |
| LH0 held for 200 ms, or a complete finger tap while LH0 remains down | Replace the old extra mode with held Utilities; remove old mode-owned Shift |
| Release LH0 after its Utilities hold | Return to combat without restoring an older mode |
| Repeat a timed-system selector | Request a 600 ms interval; Q31 accepts that an older deadline can end it early |
| Any of the 21 left positions pressed/released in Systems±, including no-ops | Refresh the 600 ms interval; accepted exception: releasing one finger may allow expiry while another stays held |
| Systems± idle expiry | Return to combat and release mode-owned Shift |
| LH1 Pause press | Clear extra mode state and owned Shift, then send Space once; no LH1 hold action |
| LT5 in Systems± or Utilities | Cancel to combat without a game key or double-tap wait |
| LT5 in combat | Send Escape only; no conditional state test or extra cancel macro |
| Combat LB0 | No action; Q30 removes the redundant Cancel |
| Battery in Systems± | Clear the mode and owned Shift, then send B; stay in combat |
| Power-map LB0 single / double-tap | Single does nothing; second press within 200 ms clears extra state and leaves FTL |
| Right-half keyboard downstroke | End any extra FTL mode before its unchanged right-half binding |
| Trackpad activity | Neither cancel nor refresh a mode; held keyboard modifiers can still affect it |
| A held mode's key released after earlier cancellation | Do not restore it, repeat cleanup, or release a newer mode's modifier |

For the merged finger gestures, use the early-tap behavior specified below.
Initial timing remains 200 ms for thumb holds, 150 ms for the merged finger decisions, 600 ms for system idle, 200 ms for Save/Exit dances, and 30 ms for combat combos.
Q30 approves the full map and initial timing values.
The actual scheduler, queue, combo capture, and held-release paths still need the gate.

### Default game outputs

The new goal is vanilla game bindings, not the user's old custom assignments.
This changes only FTL outputs; preserve normal typing, OS/output modes, connection controls, and right-hand keymap bindings.
No game reset or settings write is authorized during planning.
The user must eventually make the game's settings match the approved default-based layout before in-game acceptance.

| Action | Vanilla-reference output | Change or qualification |
| --- | --- | --- |
| Settings/menu cancel | Escape | Plain combat LT5; active system/utility views use Cancel there instead; Open Options is separately bindable to O |
| Pause | Space | Unchanged |
| Cloak / Mind / Hack / Battery activation | C / M / N / B | Unchanged |
| Jump | J | Replaces V |
| Toggle autofire | V | Replaces Left Ctrl |
| Momentary autofire | Left Ctrl, `LCTRL` | Replaces Left Alt for the Windows target selected in Q19 |
| Crew home | Return, `RET` | Combat LM0 only; replaces Tab |
| Save crew | Slash, `SLASH` | Replaces historical custom Return |
| Select all crew | Q | Utilities LB4 |
| Crew 1–8 | F1–F8 | New bindings, corroborated by vanilla settings |
| Teleporter Send / Recall | T / R | Unchanged |
| Open / Close doors | Z / X | Unchanged |
| Ship menu / Inventory | U / I | Ship menu unchanged; Inventory newly exposed |
| Weapons 1–4 / Drones 1–3 | 1–4 / 5–7 | Unchanged |
| Weapon-system / Drone-system power | W / E | Native bank actions verified by the user; retained Shift supplies depowering |
| Shields / Engines / Med-Clone / O2 power | A / S / D / F | Unchanged |
| Teleporter / Cloak / Mind / Hack / Artillery power | G / H / K / L / Y | Unchanged |
| Depowering | Held Shift plus the relevant power/number key | Retain explicit Shift across taps |
| Shop | Unassigned by default | Custom F9 at Utilities LB0; matching game assignment required |
| Crew manifest | Unassigned by default | No dedicated firmware binding or required F10 assignment |
| Enlarge event text | Minus, `MINUS` | Native FTL action; no Ctrl or Shift |
| Shrink event text | Equals, `EQUAL` | Native FTL action; not Shift+Equal or Ctrl+Minus |

Do not retain old custom values merely because they appear in the original FTL change.
Do not turn unassigned defaults into invented keycodes.
The previous O/E proposals are superseded; they were not defaults and collided with Options and drone-system power.

### Crew and menu shortcut evidence

[2018 vanilla settings](https://steamcommunity.com/app/212680/discussions/0/1733212454824451218/#c2727382174624976849) and a [2022 vanilla 1.6.14 settings post](https://steamcommunity.com/app/212680/discussions/0/3276943773305294667/) expose distinct `ship_info`, `ship_inv`, `store`, and `ship_crew` bindings.
They corroborate Crew home Return, Save crew Slash, Select all crew Q, and Inventory I, with Store and Crew manifest unassigned.
A [Crew Manifest discussion](https://steamcommunity.com/app/212680/discussions/0/1500126447406165846/) describes assigning that action through Options.
These are player-posted vanilla settings and reports, not a fresh default reset of this installation or an official factory specification.
The complete reference table above covers every existing FTL constant and the requested additions.
An [independent shortcut reference](https://defkey.com/ftl-faster-than-light-shortcuts) corroborates ordinary combat/power bindings.
Published settings are not proof of a factory reset on the user's platform.

The user's original message `6bfe28b4` in session `01a0fdd3-900a-74e9-9d90-f242349d8d70`, plus capture `/tmp/zmk-ftl-complete.diff`, established custom Return-save/Tab-home.
That evidence remains historical, but Q15 now supersedes those assignments.
The capture is not an edit target.

Retained depower Shift would modify crew/menu outputs.
Neither SDL key enums nor uppercase labels prove that FTL ignores it.
Utilities must clear mode-owned Shift before emitting unshifted commands.

### Remaining default qualifications

The two settings records disagree on the configurable Autofire Modifier: one uses Left Ctrl, the other Left Alt alongside Left Ctrl for right-click emulation.
[PC targeting instructions](https://steamcommunity.com/app/212680/discussions/0/1696046342868290501/) describe Ctrl, and [rebinding instructions](https://steamcommunity.com/app/212680/discussions/0/3099012755253380959/) identify it on hotkeys tab 2.
Q19 resolves the target to Windows, so select Left Ctrl from the corroborated PC references.
No Mac-specific mapping or automatic platform switch is needed.
The modifier remains configurable; verify matching game settings before in-game acceptance and recheck desktop shortcuts if moving to Linux.

For the earlier two-key lookup, both complete settings records leave SDL values 290 and 291, F9 and F10, unused.
Q22 originally accepted Store F9 and Crew manifest F10.
The latest answer removes the dedicated Crew-menu binding, leaving only Store F9 as a required custom assignment.
Retain the historical F10 evidence, but do not add an unused firmware binding or require the user to configure it.
Planning approval does not establish that any game settings were changed.
These do not conflict with Steam's documented default [Shift+Tab overlay](https://partner.steamgames.com/doc/features/overlay) or [F12 screenshot](https://partner.steamgames.com/doc/features/screenshots) shortcuts.
[Windows documents F10 menu focus and Shift+F10 context menus](https://support.microsoft.com/en-us/windows/keyboard-shortcuts-in-windows-dcc61a57-8ff0-cffe-9796-cb9706c75eec).
That convention is not evidence of a conflict inside FTL, but test the actual game and any user-defined desktop shortcuts before calling these conflict-free.

The default text-size direction is counterintuitive: `increase_font=45` is Minus, while `decrease_font=61` is Equals.
An [explicit default-key report with confirmation](https://steamcommunity.com/app/212680/discussions/0/624076027809557257/#c624076027811123733) agrees with both settings records.
These resize event text, not the entire interface.
Q18 accepts this target; label the keys by effect: Text larger emits MINUS; Text smaller emits EQUAL.
Do not use PLUS, Shift+Equal, Ctrl chords, or OS zoom as substitutes.

Standalone named and ordinal depower settings are unassigned in the records, but native Shift+power/number depowering remains supported.
No additional per-system custom bindings are required for the existing explicit-Shift approach.

### Merged gestures: resolve the pending tap on interruption

Q24 requests that pressing Pause while Autofire is undecided commit the toggle and Pause without waiting out the hold threshold.
Q27 accepts the same early-tap resolution on any other keyboard downstroke for both Autofire and Recall/Send.
This changes the decision rule rather than asking a still-undecided gesture to pass Pause through.
The previous tap-preferred conflict remains true, but it does not rule out this new request.
Q30 approves the initial 150 ms finger threshold.

Read-only inspection of the installed source supports a configuration-only route:
- Hold-preferred resolves on another position's downstroke.
- Positional hold conditions can then force that decision to tap when the interrupting position is not allowed to trigger a hold.
- The chosen tap child runs before captured events replay, so the Pause press no longer waits for the rest of this hold-tap interval.
- Later hold timers return without changing an already-decided tap.

See `zmk/app/src/behaviors/behavior_hold_tap.c:338–354,495–574,727–785` and the installed `zmk,behavior-hold-tap.yaml` schema.
Leave `hold-trigger-on-release`, retro-tap, speculative hold output, and typing quick-tap/prior-idle shortcuts disabled.
Q27 accepts that all other keyboard downstrokes choose the tap for each merged finger gesture.
Do not substitute the alternative that makes non-Pause interruptions choose hold.
A nonempty positional list containing only that gesture's own physical position enables the positional filter while allowing no other position to force hold.
With no other key pressed, the first-other-position remains unset and the ordinary timeout can still choose hold.
An early keyboard interruption chooses tap; a trackpad click does not.
This policy is accepted for both gestures, including interruptions by layer thumbs and right-half keyboard keys.

Use one-shot macros for early-decided discrete actions.
A plain `&kp V` tap child stays pressed until the physical Autofire key is released, which could repeat while the finger remains down.
A macro that taps V once on invocation and has no release-phase action avoids that lifetime.
The same applies to Recall R; Send T should also remain one-shot.
See `behavior_hold_tap.c:418–487,637–676` and `behavior_macro.c:115–139,163–220`.
After a forced Autofire tap, continued holding must produce neither later Ctrl nor a second toggle on release.

Only the first new keyboard press gets to resolve the pending gesture.
If Ctrl has already activated, later Pause does not reclassify that hold or add a toggle; Ctrl stays held until the Autofire finger releases.
If Pause was already held before Autofire, there is no new Pause downstroke to trigger the exception.
Near the deadline, timer/event ordering determines whether the gesture is still undecided; verify resolved-state behavior instead of promising an exact wall-clock boundary.

The queue remains a separate constraint.
Zero macro delays do not bypass existing queued work or make two host actions atomic.
`zmk/app/src/behavior_queue.c:31–78` can defer queued bindings, and macro enqueue failures are not a safety proof.
Validate exactly one toggle and one Pause, intended order, no held/repeated V, and balanced releases using the actual behavior queue.
Other pre-existing undecided hold-taps, including preserved right-half typing behavior, can still affect global event delivery.
This design removes the intentional wait from these FTL finger gestures, not every possible event-path delay.

Q27 also accepts Recall on an early keyboard interruption and Send only on an uninterrupted hold.
No switch to Send-on-interruption, new module, or custom C is authorized.
The subagent follow-up could not start reliably; these findings come from direct primary-session source inspection.
These merged-gesture findings remain source inspection only; they were not exercised by the helper test below.

### Retained contracts and remaining decisions

- LH2 remains tap Systems− / hold Weapons− with a dedicated 200 ms balanced behavior after the post-play misclick fix.
Release it before using another thumb.
A thumb-first release roll selects Systems−; a completed finger tap while LH2 stays down chooses Weapons−.
- Q20 accepts LH0 tap Systems+ / hold Utilities.
The latest annotations move Cancel to LT5 on Systems± and Utilities while retaining power-map Exit at LB0.
This supersedes the earlier LH0 double-tap Cancel preference and its hold-Cancel fallback.
Do not revive either or silently combine all gestures into another nested selector.
- Cancel-only emits no Space.
LH1 clears FTL extra state and owned Shift before one Space on downstroke.
Every new mode replaces the old one; momentary-mode release returns to combat without restoring an earlier timed mode.
- System modes retain the accepted 600 ms idle target, refreshed by relevant left-half activity.
Accepted limitation: releasing one ignored finger may restart the countdown and allow expiry while another finger remains held.
Q20 accepts momentary Utilities, not another timed mode.
Trackpad activity does not extend keyboard timers and can be shifted while depowering.
- Depowering retains explicit held Shift and plain number/system keys underneath it.
Cleanup may release only owned modifiers, and pending work/later key releases may not resurrect modes.
Battery clears system state before its game action.
Right-hand keyboard input cancels extra FTL modes before its normal binding.
- Entry stays FUN plus the normal C position.
Exit remains protected 200 ms LB0 double-tap on power maps, not combat.
Settings stays plain Escape at LT5 in combat; Systems± and Utilities use LT5 for Cancel-only.
Utilities LM5 and LB5 are now empty.
- Q25 replaces the proposed chord assignments with these choices:

| Combat chord | Action |
| --- | --- |
| Weapon 1 + Weapon 2, LM4+LM3 | Open doors |
| Weapon 2 + Weapon 3, LM3+LM2 | Close doors |
| Weapon 3 + Weapon 4, LM2+LM1 | Jump |

Use Q30's approved combat-only 30 ms recognition, with no prior-idle requirement.
No combo includes Hack/Cloak/Battery activation.
The Round 10 annotation places Crew home on combat LM0 only.
Neither the diagonal nor a triple is approved or needed.
The user explicitly selected this outside-column position; do not move it back into a combo to enforce the earlier general preference.

The previously investigated triple containing Jump remains a rejected recommendation, not a fallback.
A late third key or an intervening release could commit the shorter Jump/Close action instead.
See `zmk/app/src/combo.c:145–194,210–241,403–410,428–485` and the installed `tests/combo/overlapping-combos-*` fixtures.
This was source/fixture inspection only, not a runtime pass.

### Selector feasibility and proposed alternative

Q20 uses independent thumb hold-taps and a plain power-map LB0 tap-dance, without nesting them.
The earlier LH0 double-tap proposal is now superseded by Q20.
Installed source supports second-press immediate final-entry selection and first-entry resolution on another key: `zmk/app/src/behaviors/behavior_tap_dance.c:139–180,215–239`.
Actual combo capture, macro queue order, and timed-mode cleanup remain runtime gates.

Earlier nested one-key selection was rejected for two source-derived hazards:
outer tap-dance could let an early held finger event reach the old layer before its child hold-tap existed; reversed nesting left an unresolved inner dance able to select Systems− after Weapons−.
See `zmk/app/src/behaviors/behavior_hold_tap.c:418–479,567–574,727–785` and `zmk/app/src/behaviors/behavior_tap_dance.c:102–136,215–239`.
The selector interactions have not been exercised at runtime.

## Numbered system hotkeys: defer, do not assume

The user reports separate, initially unbound Power System 1–8 and Depower System 1–8 actions in FTL.
Whether these exactly follow the displayed HUD, how they treat weapons/drones, and what changes after a system purchase have not been verified here.
The examined Hyperspace source and generated `SystemControl::OnKeyDown` bindings did not establish vanilla handling of those shortcuts.
Existing shortcut references establish named system controls, not that ordinal contract.

If numbered shortcuts follow HUD position, they would make an eight-key visual mapping attractive, but a key's system identity could then change with the ship or HUD composition.
Named bindings retain fixed physical meanings across ships, even when a system is absent.
Recommendation: keep named controls for this trial and do not require sixteen new in-game assignments.
Revisit ordinal controls only after a separately authorized game check establishes order, absent-system behavior, weapon/drone behavior, and the effect of buying a system, and the user chooses position-based memory over fixed system identities.
Do not add speculative ordinal constants or replace individual weapon depowering on that assumption.

## Timed-helper verification gate

The installed `zmk-tri-state` module exposes the needed general shape: start/continue/end bindings, ignored positions/layers, and an idle timeout.
It is already listed in `config/west.yml`; this is not stock one-next-key `&sl`.
However, read-only inspection of `modules/zmk/tri-state/behaviors/behavior_tri_state.c` found risks that must not be hidden:

- Inactive slots are reused without cancelling their pending timer; `reset_timer()` calls `k_work_schedule()`, which leaves an existing deadline unchanged according to `zephyr/include/zephyr/kernel.h`.
- The timeout guard counts the activator's pressed state, not all held ignored keys.
- Position-triggered cleanup is queued, while other start/end paths differ in ordering; repeated selector presses, switching, and cancellation must not leave stale state or modifiers.
- Layer deactivation alone does not end the installed tri-state behavior.
- Its current timeout fixture tests only the activator press/release, not this interaction contract.

The inspected [upstream version c089696](https://github.com/urob/zmk-tri-state/blob/c089696/behaviors/behavior_tri_state.c) still uses the same scheduling primitive and lacks an ignored-key hold count; an upgrade is not an assumed fix.
Planning used source inspection only.
The authorized runtime attempt below now confirms a held-key failure.

When `/implement` or a separate local experiment is authorized, first run a bounded proof using the actual module and scheduler, not merely a hand-written model of the desired behavior.
Use existing ZMK behavior-test tooling in a supported environment; inability to run it is an unmet gate, not a pass.
Once the revised interaction is approved, exercise its complete transition table, both thumb selectors, power-map Cancel/Exit taps, early finger rolls and combo capture during selection, several adjustment taps, overlapping held finger keys, cancellation/re-entry before the old deadline, both sides of expiry, Pause cleanup and cancel-only, Battery, exit, and right-hand interruption.
Include macro-queue ordering, rapid sign changes, held Weapons− release, and proof that no pending gesture reactivates an ended mode.
Verify the approved forced-tap/Pause path with actual queue ordering, exactly one toggle, no later hold or duplicate action, and no held V/R/T.
Also verify Pause cleanup, plain combat Settings versus active-view LT5 Cancel, Utilities lifetime, unshifted utility outputs, balanced autofire Ctrl, one-shot Send, protected Save crew, and Exit-only LB0 on all power maps.
Check native W/E outputs under the correct retained modifier, including shared Systems− LM0.
Require no delayed reactivation, balanced owned Shift, and the specified host actions.
Q31 accepts stale deadlines on repeated selection and cancellation/re-entry; keep those failures as TODO evidence instead of claiming they pass.
The user explicitly accepts expiry during overlapping held finger keys after one is released; document that limitation rather than claiming a held-key guard.

Only proceed to the layout implementation if the installed helpers can satisfy that contract through configuration.
Stop for failures beyond the timer limitations explicitly accepted by Q31, or if proceeding requires custom C, module changes, or an upstream upgrade.
Do not silently weaken timeout safety, substitute persistent toggles, or implement prerequisite fixes as another change.

### Runtime gate result

Q30 approved the layout with combat LB0 empty, and the user then invoked `/implement`.
At the first attempt, the held-key prerequisite failed and layout implementation stopped.
The user subsequently accepted that limitation; the resumed gate is recorded below.

The test ran the installed ZMK application, installed tri-state module, real Zephyr scheduler, and behavior queue on `native_posix_64`.
It used Linux/amd64 in `docker.io/zmkfirmware/zmk-dev-arm:3.5`, the development image declared by `zmk/.devcontainer/Dockerfile`.
The resolved image digest was `sha256:9dd48f05e5c9d3311057d114fd719d8a9dc43a5648b6fd572829b2ba05e0cd04`.
Repository sources were mounted read-only; only the ignored `.build/ftl-gate` output directory was writable.
No source patch, dependency upgrade, or game-setting change was made.

| Test | Result | Meaning |
| --- | --- | --- |
| Existing `modules/zmk/tri-state/tests/timeout` | PASS | The installed fixture builds and its ordinary activator timeout matches its snapshot |
| Local `.build/ftl-gate/cases/tests/held-left` | FAIL | The end action releases Shift before the last ignored finger is released |

The local fixture includes the module's existing `tests/behavior_keymap.dtsi`.
It overrides `swap` with a 600 ms timeout, ignored positions 1 and 2, and `<&kt LSHFT>, <&none>, <&kt LSHFT>` bindings.
Shift is an observable start/end marker, not a proposed toggle-based FTL implementation.
This isolates the helper's lifetime rule without pretending to validate final layer ownership or thumb gestures.

The mock sequence taps the activator, holds B at position 2, presses and releases A at position 1, then keeps B held for another 1000 ms.
The expected snapshot keeps Shift down until after B is released.
The actual HID event order was:

```text
Shift down
B down
A down
A up
Shift up    # timeout ended tri-state while B remained held
B up
```

The full log explicitly reports `Tri-state deactivated due to timer` between A release and B release.
The compiled devicetree confirms `timeout-ms = <0x258>` and ignored positions `<0x1 0x2>`.
In `behavior_tri_state.c`, the ignored-key release path schedules a timeout without counting other held ignored keys.
The timer handler guards only the activator's `is_pressed`.
This is an observed contract failure, not just the earlier source-based concern.

Local artifacts:
- Fixture, expected snapshot, and event filter: `.build/ftl-gate/cases/tests/held-left/`.
- Actual HID events and full trace: `.build/ftl-gate/tests/held-left/keycode_events.log` and `keycode_events_full.log`.
- Build/test logs: `/tmp/zmk-ftl-gate-timeout.log` and `/tmp/zmk-ftl-gate-held-left.log`.
- Tested tri-state source SHA-256: `0a02ee5a6ad80a3dd7b9f0a087db4562162f01c5a87e4fb6c0123d7728986f03`.

Reproduce locally from the repository root while those ignored artifacts remain:

```bash
docker run --rm --platform linux/amd64 \
  --mount type=bind,src="$PWD",dst=/workspace,readonly \
  --mount type=bind,src="$PWD/.build/ftl-gate",dst=/build \
  --workdir /workspace \
  -e Zephyr_DIR=/workspace/zephyr/share/zephyr-package/cmake \
  -e ZEPHYR_BASE=/workspace/zephyr \
  -e ZMK_SRC_DIR=/workspace/zmk/app \
  -e ZMK_BUILD_DIR=/build -e ZMK_TESTS_VERBOSE=1 \
  docker.io/zmkfirmware/zmk-dev-arm:3.5 \
  sh zmk/app/run-test.sh .build/ftl-gate/cases/tests/held-left
```

At that first stop, no later gate cases had run.
Fresh-deadline/reentry safety, final modifier ownership, thumb selectors, combos, merged gestures, and queue ordering were still unverified.
No Corne build, formatting pass, flash, or implementation review ran.
A passing baseline fixture is not approval of the FTL contract.
No fallback or prerequisite fix was implemented at that stop.
The following user acceptance allowed testing to resume.

### Resumed gate: stale deadlines

The user renewed implementation authorization with: "the plan. The drawback described is not ideal but acceptable."
This accepts the demonstrated overlapping-held-finger expiry only.
It does not waive fresh deadlines after selection/re-entry, stale-state cleanup, modifier ownership, or the other runtime checks.
The first held-key failure is now an accepted limitation, not the reason to stop.

The next native fixture, `.build/ftl-gate/cases/tests/fresh-deadlines`, exercises two remaining timer requirements in one run.
It uses the same installed application, module, kernel, read-only source mount, and Linux development image.
Unlike the first probe, it uses explicit `macro_press` and `macro_release` of Shift, with zero macro delays, rather than a toggle marker.
The timeout is 600 ms and B at position 2 is an ignored key.
No test step requires overlapping keys.

| Case | Probe | Observed result |
| --- | --- | --- |
| Repeat selector | Tap selector, wait 400 ms, tap it again, then press B after another 400 ms | FAIL: Shift is already released before B, although the second selection should provide 600 ms |
| Cancel and re-enter | Tap selector, cancel using non-ignored A, re-enter, then press B 450 ms after releasing the new selector | FAIL: the old timer ends the new activation before B |

In both phases, Shift is released before B is pressed, rather than remaining held through the probe.
The expected order keeps Shift held through the B tap and ends only after its fresh idle interval.
The full trace reports timer-driven deactivation before each probe.
Cancellation itself released Shift before A's output in this fixture, and all three Shift presses had matching releases.
Those observations do not validate the final multi-mode ownership or queue-backlog cases.

The source matches these results:
- `reset_timer()` updates `release_at`, but `k_work_schedule()` does not replace an already-pending deadline.
- The expiry handler does not compare the current time against the updated `release_at`.
- Position-triggered cancellation marks a slot inactive without cancelling its pending work.
`new_tri_state()` can reuse that slot and timer for a new activation.

This is different from the accepted held-finger limitation: a newly selected mode can end early with no finger held at all.
The direct helper route therefore still fails the fresh-deadline and cancellation/re-entry requirements.
No configuration-only correction has been established, and no helper patch or alternate architecture was authorized.

Local evidence:
- Fixture and expected snapshot: `.build/ftl-gate/cases/tests/fresh-deadlines/`.
- Actual HID events and full trace: `.build/ftl-gate/tests/fresh-deadlines/keycode_events.log` and `keycode_events_full.log`.
- Build/test log: `/tmp/zmk-ftl-gate-fresh-deadlines.log`.
- Reproduce using the Docker command above with the final test path changed to `.build/ftl-gate/cases/tests/fresh-deadlines`.

The original held-finger acceptance is recorded, but these two additional deviations remain unapproved.
[Q31](ftl-comfort-layout.questions.md#round-13-stale-timer-deadlines) asks whether to authorize a helper fix or accept these shortened selector/re-entry lifetimes as well.
The remaining gate cases, full layout implementation, firmware builds, and both implementation reviews remain pending.


### Q31 implementation

Q31 answers: "Leave a todo about making a proper fix and just go ahead with this limited implementation now."
This authorizes the shortened selector/re-entry lifetimes as well as the previously accepted overlapping-held-finger expiry.
These known timer failures are documented limitations, not passing tests or reasons to block this limited version.
The remaining cleanup, modifier, gesture, and layout requirements still apply.

The implementation uses `config/ftl.dtsi` for FTL-only behaviors and the existing installed tri-state module unchanged.
`config/base.keymap` contains the four physical FTL maps, with one shared Systems± map.
Layers 17–19 are transparent cancellation/release signals, not persistent user-facing views.
A global pulse ends the active tri-state before Pause, Battery, Exit, or another selector.
Each held mode has its own release pulse, ignored by the other modes, so a cancelled hold's later release cannot deliberately cancel a newer mode.
The module owns the matching Shift press/release; neither global release-all nor implicit per-key Shift replaces it.

TODO: repair tri-state timer rescheduling, pending-work reuse on cancellation/re-entry, and held ignored-key accounting, then restore the original timeout assertions.
The matching source TODO is in `config/ftl.dtsi`.
No custom C, module source patch, dependency upgrade, game-setting write, or flash was added.

A native `layout-probe` fixture exercised these behaviors and the exact four maps using the actual installed application and scheduler.
An independently specified expected sequence matched all 90 HID events.
It covers timed selection and expiry, Cancel/Pause/Battery cleanup, early finger selection, both held modes, cancellation followed by a newer mode and late old-thumb release, right-hand interruption, physical Shift ownership, V/R forced taps before Pause, held Ctrl, one-shot Send, all three combat chords, Save, and protected Exit.
The first capture intentionally had a placeholder snapshot; that comparison failure was not treated as a behavior failure or auto-accepted.
The explicit interaction-derived check then passed.

A second probe inserted a 300 ms behavior-queue backlog at an otherwise blank left key.
It caught Pause/Battery firing before Shift cleanup and a sign-change cancellation ordering error in the first implementation.
Selectors now pulse cancellation directly instead of nesting the cancellation macro.
Pause, Battery, and Exit defer their output through a child macro after the pulse's queued cleanup.
The corrected `queue-probe` matches 30 independently specified HID events, and the original 90-event `layout-probe` also passes the native snapshot runner.
This covers those bounded backlog cases, including sign switching, held-mode release, and forced V/R before Pause; it is not an arbitrary-backlog proof.

Local evidence is in `.build/ftl-gate/cases/tests/layout-probe/`, `.build/ftl-gate/tests/layout-probe/`, and `.build/ftl-gate/check-layout-probe.py`.
The production behavior file matches the tested prototype byte-for-byte.
The test checks bounded interaction sequences, not arbitrary host timing or game behavior.
Final build, compiled-map, formatting, and review results belong in the completion report.


### Review gate: busy-queue transitions

This records the earlier blocked review; Q32 resolves its applicability in the following section.
Both Corne halves compile, the formatter is idempotent, and compiled-map checks pass.
The additional `combo-probe` passes 30 expected HID events covering near misses, adjacent rolls, overlay exclusion, blank-key activity, and held Ctrl.
These passing checks do not override the failures below.

The named code-review workflow reviewed the complete `145ba52f78e6990d7cd9f6ccde40b4bf42bf0ce6` snapshot of `kvloqwntmwqmnyzvkvtwysxzxzrkkyup`.
Its parent remains `pwmtwkkqlqsknwkxmusvznwlvpuptsny` at `a70bb556b1114344fe3e408c0b491198ca2dc7f3`.
It found three busy-queue correctness defects.
A new actual-runtime `queue-interrupt-probe` reproduced all three, using the production behavior file and a blank left key instrumented to create a 300 ms backlog.

| Case | Required result | Actual result |
| --- | --- | --- |
| Tap-release LH0, then LM4 before queued activation | Shields A, possibly delayed | Combat Weapon1 instead |
| Systems− active, then a direct right-hand X during backlog | Owned Shift released before X | X pressed and released while owned Shift is still down |
| Held LH2 resolves during backlog, then right-hand input before activation | Pending mode cancelled | Weapons− and Shift activate after the right-hand interruption |

The regression snapshot expects 12 HID events; the actual trace has 14, including the unwanted late Shift press/release.
Evidence: `.build/ftl-gate/cases/tests/queue-interrupt-probe/`, `.build/ftl-gate/tests/queue-interrupt-probe/keycode_events.log`, and `/tmp/zmk-ftl-gate-interrupt.log`.
The log reports `FAILED: queue-interrupt-probe`.
These are wrong actions and cleanup failures, not the timer faults Q31 accepted or harmless delivery delays.
No configuration-only correction has been established.

The direct ponytail review of the same complete snapshot found one simplification:
`config/ftl.dtsi:131–142` duplicates `ftl_key` for V/R/T.
Reuse that existing parameterized one-shot in the gesture bindings when work resumes; net 12 source lines can be removed.
The code-review pass reported the same cleanup suggestion.
Neither review gate is clean.
No code changes followed the reviewed snapshot; this follow-up records the blocker in documentation only.

[Q32](ftl-comfort-layout.questions.md#round-14-busy-queue-transitions) asks for the required scope/contract decision.
The local prototype and compiled artifacts remain, but this is not completed or validated-for-flashing firmware.
Nothing was flashed or published.

### Q32 scope resolution

Q32 confirms no right-half use during FTL play and rejects the artificial queued-activation case as a practical gameplay blocker.
The supported path is the left-hand FTL controls plus trackpad, without unrelated delayed typing macros running.
Right-hand bindings remain unchanged; the earlier right-interruption contract is not a guarantee under an unrelated macro backlog.

The source audit checks every FTL macro and its child behaviors: all macro `wait-ms` and `tap-ms` values are zero, as are tri-state end taps.
`macro_pause_for_release` splits press/release actions without occupying the queue while the thumb is held.
Tap/hold, combo, double-tap, and idle timers are separate from macro-queue waits.
The installed queue drains zero-wait actions directly when no delayed work is pending.
The 300 ms backlog was deliberately injected by the fixture, not produced by an FTL control.
No FTL-only trigger has been demonstrated for the three reviewed backlog failures.

Keep those failures as out-of-scope stress evidence rather than claiming they were fixed or blocking ordinary play on them.
No helper, custom C, or dependency changes are authorized or needed for this narrowed scope.
The timer limitations accepted by Q31 and their proper-fix TODO remain.
The duplicate V/R/T macros are removed in favor of the existing parameterized `ftl_key`; the gesture parameters and previews are updated together.
After the simplification, the native runner again passes `layout-probe` with 90 events, `queue-probe` with 30, and `combo-probe` with 30.
`ftl-only-interrupt-probe` repeats the three reviewed input sequences without the injected delay and passes all 14 expected events.
Its expected trace includes normal activation before the finger action and Shift cleanup before right-key output.
All 18 FTL macros have explicit zero delays; all four tri-state end taps are also zero.
Both Corne builds, compiled-map checks, normal-map preservation, and formatter idempotence pass.
Evidence is in `.build/ftl-gate/cases/tests/ftl-only-interrupt-probe/`, `/tmp/zmk-ftl-q32-native.log`, and `/tmp/zmk-ftl-q32-build.log`.
Both full-diff review gates still apply; the completion report records their final results.

### Post-play thumb-roll fix

The user reports frequent wrong actions on the first system adjustment, but correct repeated adjustments after a successful first press.
They confirm that accidental held-layer selection during a thumb/finger roll matches the problem.
The original hold-preferred rule selected Weapons− or Utilities as soon as the finger went down, even only 5 ms before the thumb released.

The native `first-press-probe` checks 20 clean tap/sign-switch cases with 0–40 ms gaps and two overlapping rolls.
Before the fix, all clean cases pass, but the two rolls produce Shift+1 and F5 instead of the intended system A outputs.
The trace is reproducible on repeated runs without any injected macro delay.
This establishes the wrong-layer cause; it does not prove every reported opposite-direction game effect has the same cause.

Change only `ftl_minus` and `ftl_plus` to balanced recognition at the existing 200 ms threshold.
Thumb release before the overlapping finger release selects Systems; a completed finger tap while the thumb remains down selects the held view, as does the 200 ms timer.
This supersedes the original immediate hold-on-finger-down rule, leaving all maps, finger gestures, timer limitations, and helpers unchanged.
The user guide describes the first finger's possible wait for this decision.
After the fix, the unchanged `first-press-probe` expectation passes all 22 cases and 160 HID events; `layout-probe` also passes its existing 90-event snapshot.
The latter still covers intentional held modes, cleanup, late thumb releases, finger gestures, protected actions, and combat combos.
Evidence is in `.build/ftl-gate/cases/tests/first-press-probe/`, `/tmp/zmk-ftl-first-press.log` before the fix, and `/tmp/zmk-ftl-balanced-native.log` after it.
The original failing fixture is retained; final builds and both review results are recorded in the completion report.

## Implementation scope

- Change the FTL behaviors, maps, combat-only combos, and embedded preview overrides in `config/base.keymap`.
- Update only required FTL constants and layer indices in `config/constants.dtsi`; use verified defaults and document only explicitly approved exceptions.
- Update `ftl.md` with all approved maps, thumb gestures, cancel-only versus Pause-and-cancel behavior, exit, and timer/modifier warnings.
- Keep ordinary typing, OS modes, connections, and right-hand bindings unchanged.
Retarget all FTL outputs to verified vanilla defaults, except any explicitly approved unbound-action exceptions; retain trackpad access.
- Do not activate the legacy `combos.dtsi`, add a runtime dependency, edit game settings, automate mouse clicks, or expand to unrelated keymap cleanup.
- Keep the plan's decision and verification record current in the same owning change.

## Verify

Q30 approves the complete layout, timing values, and transition contract with combat LB0 empty.
Q31 accepts the observed timer failures and requests a TODO for the proper fix.
Other required validation remains in force.
Leave no action, modifier lifetime, timeout policy, or fallback for `/implement` to invent.
Do not revive the incompatible tap-preferred-plus-immediate-Pause contract; test the accepted early-tap semantics for both gestures.
The original timer assertions remain expected failures under the accepted limitation; unrelated correctness failures still block completion.

1. Complete all source edits, run `nvim --headless config/base.keymap +QMKFormat +wq`, review the previews, and check formatter idempotence before the final build.
2. Check compiled DTS for 42 positions per layer, layer indices, plain number outputs, named power outputs, LH1 Pause everywhere, transparent right half, explicit left no-ops, and the protected exit.
3. Check effective actions against every row of the approved transition table, including selector recognition, hold release, timeout, and cancellation cleanup.
4. Check every approved combo, near misses, single constituents, adjacent weapon rolls, the held autofire modifier, and timeout boundaries.
Assert no combo includes Hack/Cloak/Battery activation and no combat combo runs in any non-combat FTL layer.
Preserve the global Enter combo.
5. Run `nix develop --command mise run build` without a target argument, or `mise run build` inside the declared development environment.
Investigate any build-induced source edits and rebuild after all final saves.
6. After final version-control operations, require `nix develop --command mise run --dry-run --verbose flash` to report `[build] sources up-to-date, skipping`.
Do not flash automatically.

After separate user-authorized flashing, repeat the approved power-transfer examples ten times in a safe game state and exercise every combat combo, Pause, and cancel in each mode.
Success means the agreed frequent actions use only comfortable positions, no simultaneous thumb presses are needed, no unintended game action occurs, and no modifier or layer remains stuck.
Ask the user whether discomfort improved, stayed the same, or worsened; this is an ergonomic smoke check, not a measured long-term fatigue claim.
Failure returns to planning or rollback, not an automatic helper rewrite.

## Dependencies, rollout, and rollback

Product gate: passed by Q30 and the subsequent `/implement` request.
Technical gate: Q31 accepts the timer limitations; Q32 excludes unrelated delayed macros and right-half use from play.
The artificial-backlog failures remain known outside that scope, with no demonstrated FTL-only trigger.
Base prerequisite: verified shared-power change already present locally.
Status: implementation present; final checks and both review results are reported in the completion report.

No merge, publication, or backfill is requested.
Flashing requires separate authorization; successful builds do not establish in-game correctness.
Actual menu bindings may require user-configured game assignments; do not change those settings or claim the shortcuts work without verification.
Mode timing is a compatibility risk: after idle expiry, the same physical key can perform a combat action rather than a power adjustment.

Rollback restores only this change's FTL implementation while preserving unrelated subsequent work and the earlier working FTL baseline.
Rebuild and repeat the freshness check before any separately authorized rollback flash.
These assignments are reversible local configuration choices; no ADR or new domain glossary is needed.

## Review record

All earlier answers and annotated previews remain in the questions file.
Q13 requires downstroke Pause, rejects LH1-hold Utilities, corrects menus to L5 positions, and adds crew F1–F8 and text-size controls.
Q14 accepts merged tap/hold semantics with concern about 200 ms; Q15 approves double-tap Save and switches FTL outputs to defaults.
Q16 rejects non-thumb layer holds; Q17 accepts custom exceptions; Q18 accepts native event-text resizing; Q19 selects Windows with possible future Linux.
Q20 accepts LH0-held Utilities and relocated Cancel, with a follow-up explanation requested for each power mode.
Q21 rejects the non-adjacent Jump chord; Q22 accepts F9/F10 menu exceptions.
Q23 requests clarification of press versus release and does not approve delayed Pause.
The default audit is complete for this Windows target.
Q24 requests that Pause force a still-pending Autofire tap; Q27 accepts early-tap interruption for both merged gestures.
Q25 fixes adjacent pairs to Open / Close / Jump; the following annotations place Crew home on combat LM0 only.
The latest annotations keep plain combat Settings, confirm native bank shortcuts, move Inventory to utility LM0, accept All crew at LB4, and remove the Crew-menu key.
Q29 permits keeping the extra Systems− W binding; the shared-map version is retained.
The extra bank lookup is stopped at the user's request, without claiming an independent research pass.
Q30 approves Round 12 with the dedicated combat LB0 Cancel removed, followed by an explicit `/implement` request.
The user accepts the initial held-key expiry limitation and renews implementation authorization.
The next native test fails fresh-deadline checks for selector repetition and cancellation/re-entry.
Q31 explicitly accepts this limited version and requests a TODO for a proper fix.
The four FTL maps, gestures, native outputs, and cleanup behaviors are implemented; the bounded 90-event native probe passes.
The subsequent reviews and native regression test identify wrong-map, Shift-cleanup, and pending-activation failures during queue backlogs.
Q32 and the source audit resolve those injected-backlog cases as outside the intended left-hand play scope, without undoing the timer acceptance or claiming the stress failures were fixed.
No game settings have changed and nothing was flashed.
