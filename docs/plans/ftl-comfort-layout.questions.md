# FTL comfort layout questions

Plan: [FTL layout for reduced finger reach](ftl-comfort-layout.md).
Status: Q32 scopes left-hand play; the post-play follow-up below replaces hold-preferred thumbs with balanced recognition to tolerate accidental rolls. Earlier answers remain historical.
Edit the `Answer:` fields and report when your answers are ready.
Recommendations are proposals, not defaults accepted by silence.

## Settled inputs

- Pause is `LH1` in every FTL layer.
- Frequent finger actions belong in `LT1`–`LT4` and `LM1`–`LM4`.
- Weapon/drone number keys already provide powering; separate Power+ copies are unnecessary.
- Rare actions may use the bottom row.
- Use one new change rather than modifying the original FTL revision.

Physical left-hand order, viewed as the keymap is drawn:

```text
LT5  LT4  LT3  LT2  LT1  LT0
LM5  LM4  LM3  LM2  LM1  LM0
LB5  LB4  LB3  LB2  LB1  LB0
               LH2  LH1  LH0
```

The four central columns are the requested area.
`LH2` is currently Pause, `LH1` currently Power−, and `LH0` currently Power+.
After moving Pause to `LH1`, only `LH2` and `LH0` remain for other thumb roles.

## Round 1

### Q1. Which actions need the easy positions, and which need no layer hold?

Please group the actions below as **direct**, **frequent but a hold/chord is fine**, or **rare**.
Pick at most eight direct finger actions; Pause already has its own thumb.
Also name the two or three power transfers you repeat most, such as O2 → Engines or Medbay → Weapons.
This is about your play, not all theoretically useful ship systems.

- Weapons 1, 2, 3, 4; drones 1, 2, 3.
- Activate Hack, Cloak, Mind, Battery.
- Teleporter Send and Recall; Crew home.
- Shields, Engines, O2, Med/Clone, Teleporter, Cloak, Mind, Hack, Artillery power adjustment.
- Weapon/drone depowering.
- Open doors, Close doors, Jump.
- Toggle autofire and momentary autofire reversal.

Recommendation: keep weapons 1–4 directly on `LM4 LM3 LM2 LM1`, then choose four top-row actions from your actual priorities.
Put frequent power adjustments behind an easy thumb hold rather than on the bottom row.
Do not assume drones, doors, or teleporting are rare just because they currently sit outside the central area.

Answer:
  - Activate Hack, Cloak, Mind are frequent
  - Door actions, crew home, and jumps too
  - Power adjustments for pretty much everything are in constant use when there are multiple systems like hack, cloak, mind, battery
  - I rarely use drones
  - tog Autofire is used quite rarely. mo Autofire is much more common

### Q2. Which holds and simultaneous presses are comfortable?

With Pause moving to `LH1`, is `LH2` comfortable for a frequently held layer key, and is `LH0` acceptable for an occasional hold?
Can you comfortably press `LH1` to pause while either other thumb key is held?
Would holding `LH2` and `LH0` together be acceptable, or should the design never require two thumb keys at once?
Are adjacent two-finger presses on the central top/home rows comfortable, if combos prove useful?
No timed experiment is required; describe what already feels comfortable and what you want to avoid.

Recommendation: use `LH2` for the most-used extra bank, minimize `LH0` use, and avoid requiring two held thumb keys until you explicitly confirm that works for your hand.
Keep Pause usable without first dismantling a power-management chord.

Answer:
In order per line:
  - Yes
  - No
  - No, never
  - Yes


### Q3. Which interaction trade-offs are acceptable?

Earlier you wanted immediate combat keys, no combat tap/hold decisions, and no toggled power layers.
Do those constraints still hold?
Would you accept a few simultaneous two-finger combos even though their individual keys may wait for combo recognition, or should we first design with momentary thumb holds only?
A thumb hold plus a finger key does not require the simultaneous timing window of a finger combo.

Recommendation: retain immediate Pause, avoid combat tap/hold and persistent layer toggles, and try momentary access first.
Only use finger combos where the resulting placement benefit is worth their recognition delay and accidental-roll risk.
We will choose exact pairs and timing in a later round if you want combos.

Answer:
No persistent layer toggles, but I'd be willing to try some timed sticky layers if those are feasible.
I.e a button or chord activates a sticky layer, which stays active for consecutive key presses until there's a short pause or explicit deactivation.


### Q4. Are the menu examples required new shortcuts or just bottom-row placement examples?

Do you want new dedicated Store, inventory, and crew-view keys, or is keeping Ship menu on a rare-use key and using the trackpad for its tabs and Store sufficient?
The current config has `U` for Ship menu and no dedicated Store/inventory-tab/crew-tab binding.
The shortcut reference checked so far does not establish those dedicated shortcuts; we will not invent keycodes for them.
If dedicated menu keys are mandatory, their supported access method becomes a verification gate before implementation.

Recommendation: keep the existing Ship menu and trackpad workflow for this change; treat those examples as permission to place rare actions low, not a requirement to automate menus.
Preserve the existing game bindings, safe exit, and trackpad-based crew selection and saving.

Answer:
Dedicated menu keys are not mandatory in the layers but do define constants for those as well

## Round 1 conclusions

Your answers rule out any design requiring two thumb keys at once, including a layer hold plus Pause.
Hack, Cloak, Mind, doors, Crew home, Jump, momentary autofire, and power adjustment need the comfortable area.
Drones and toggled autofire can use rare-action positions.
Timed multi-key layers are worth investigating, but persistent toggles are not acceptable.
Menu keys are optional; menu constants are requested.

The installed urob tri-state module has idle-timeout support, unlike stock one-shot `&sl`.
Its older implementation also has timer-reuse and overlapping-held-key risks, so timed-layer correctness needs the bounded verification described in the plan.
These questions settle the desired interaction before implementation or any firmware experiment.

## Round 2

### Q5. Is this the timed-layer contract you want to try?

Proposed behavior:

- Tap a selector, release it, and make several power adjustments without holding a thumb.
- Start with a **600 ms idle timeout**, refreshed by the relevant key activity; no expiry while an action key remains held.
- Idle expiry returns to combat, so the next finger key uses its combat binding.
- `LH1` always pauses immediately and also cancels the timed mode, clearing its held Shift before sending Space.
- Selecting another mode ends the old mode cleanly; no simultaneous thumb presses are required.
- A timed depower mode keeps Shift down between taps, as the working depower behavior does now.
Until cancellation or timeout, Shift can also affect trackpad clicks.
Trackpad activity will not extend the keyboard's idle timer.

Recommendation: try this contract with 600 ms as an adjustable starting point and Pause as a clear return-to-combat action.
If the installed helper cannot pass cleanup and timeout checks, stop and report the blocker rather than silently upgrading ZMK or writing a new behavior.
Would you change the timeout, Pause cancellation, or retained-Shift policy?

Answer: sgtm

### Q6. Can frequent combat actions use these two-key combos?

A candidate keeps weapons 1–4 on `LM4 LM3 LM2 LM1`, with Cloak, Mind, Hack, and momentary autofire on `LT4 LT3 LT2 LT1`.
To avoid moving other frequent actions to the bottom row, propose:

| Combo | Action |
| --- | --- |
| `LT4 + LT3` | Open doors |
| `LT3 + LT2` | Close doors |
| `LM4 + LM3` | Crew home |
| `LM2 + LM1` | Jump |

All are adjacent same-row pairs; no vertical same-finger chord is required.
Start with **30 ms** combo recognition and no prior-idle requirement, scoped to combat.
Individual Cloak/Mind/Hack and weapon presses participating in those combos can wait for recognition; a recognized pair suppresses the two individual actions.
Pause and the proposed momentary-autofire key do not participate.
A near-miss Open doors combo could activate Cloak or Mind instead; pressing weapon 3 and 4 together would request Jump rather than select those weapons.
If you commonly roll those weapon keys together, we should not use that Jump pair.

Recommendation: accept these as a first candidate only if that recognition delay is acceptable.
Otherwise we should allocate another action bank instead of pretending combos are free.
Are the pairs and delay acceptable, or are there specific actions you do not want behind a combo?

Answer:
With combos LM1+LM2 and LM2+LM3 feel the most natural.
LM3+LM4 is fine too.
Same combos on the top row might be fine but don't want to use hack, cloak, or battery keys in a combo to avoid accidental activation.
Mind control and teleport controls are fine as those require another click for the target and aren't easily activated by accident.

### Q7. Where do the remaining actions fall in your actual usage?

Are **Battery activation**, **Teleporter Send/Recall**, and **Artillery power adjustment** frequent or rare for you?
They were not explicitly ranked in Round 1.
Battery's activation button is a different action from adjusting other systems' power.
There are nine named system-power actions but only eight central positions, so artillery's placement matters.

Recommendation: keep Battery within central access unless you say it is rare.
Only use bottom/outside positions for Send/Recall or Artillery if you classify them as rare.
If all are frequent, give them a central-bank or combo path rather than dropping them.

Answer:
Battery activation is somewhat common.
Teleport controls are quite rare.
Artillery power adjustment is super rare.


### Q8. How do you use momentary autofire reversal?

Do you usually select a weapon first, then hold autofire reversal while targeting with the trackpad?
Or must you comfortably keep it held while pressing any of the four weapon keys?

Recommendation: the proposed direct `LT1` modifier works naturally for selecting first and then targeting.
But `LT1` and weapon 4 on `LM1` occupy the same column, so holding one while pressing the other needs another finger.
If selecting weapons while holding reversal is important, we should choose a different access gesture before approving the map.

Answer:
First select weapon, then hold mo autofire.

### Q9. Which assignments should the optional menu constants represent?

There is no verified set of default keycodes for these separate menu actions in the evidence collected.
Your earlier testing established that Store has no default shortcut.
We should not define fake defaults or make all menu constants aliases for Ship menu `U`.

Would you accept these as **proposed custom game bindings**, documented as requiring matching assignments in FTL before use?

| Constant | Proposed key |
| --- | --- |
| `FTL_STORE` | `O` |
| `FTL_INVENTORY` | `I` |
| `FTL_CREW_MENU` | `E` |

Recommendation: define them only as explicitly approved custom assignments, leave them unbound in this first layout, and keep the working Ship menu key.
No game settings would be changed by this repository change.
If you have existing preferred assignments, list those instead; if an action is not bindable in your game, leave that constant unresolved rather than assigning an unrelated key.

Answer: sgtm

A side note: there are also (de)power system 1-8 keys which aren't bound by default.
I guess they work in the order the systems show up in the hud.
Wonder if using those would be convenient or confusing...

## Round 2 conclusions

The 600 ms timed-mode contract and custom menu constants are accepted.
Hack, Cloak, and Battery activation keys cannot be combo constituents, so the earlier top-row door pairs are rejected.
The home-row adjacent pairs are comfortable; teleporting and artillery are rare, Battery is somewhat common, and momentary autofire is held after weapon selection.

The numbered-system idea remains unverified, including HUD order and weapon/drone handling.
The candidate retains fixed named systems for this first trial; the plan records the benefits and risks of a later ordinal experiment without assuming that the game behaves as guessed.

## Round 3

### Q10. Do you approve this complete candidate, or what would you change?

Historical candidate; see the [current revision status](ftl-comfort-layout.md#layout-revision-after-q10).
The original proposal below is retained with your feedback.
The central positions are:

```text
                 LT4       LT3       LT2       LT1
Combat           Cloak     Hack      Mind      mo Autofire
Systems +/-      Cloak +/- Hack +/-  Mind +/-  Battery
Weapons -        None      None      None      None

                 LM4       LM3       LM2       LM1
Combat           Weapon 1  Weapon 2  Weapon 3  Weapon 4
Systems +/-      Shields   Engines   Med/Clone O2
Weapons -        Weapon 1- Weapon 2- Weapon 3- Weapon 4-
```

Proposed combat-only combos:

- `LM1+LM2`: Open doors.
- `LM2+LM3`: Close doors.
- `LM3+LM4`: Crew home.
- `LT1+LT2`: Jump, combining mo Autofire and Mind rather than any Hack/Cloak/Battery key.

The last pair is new: it adds the proposed 30 ms recognition delay to mo Autofire and Mind.
A near miss can arm Mind or hold Alt; a recognized pair requests Jump.
The home-row pairs can fire instead of weapon selection if you roll adjacent weapons within the combo window.

Thumbs and the important trade-offs:

1. `LH1` always cancels timed power state and pauses/unpauses once.
2. From combat, tap `LH2` for Systems− or `LH0` for Systems+.
3. In Systems−, tap `LH2` again for Weapons−; from Weapons−, `LH2` returns to Systems−.
Thus weapon depowering takes two selector taps from combat, but retains all four home-row positions and needs no held thumb.
There is no separate double-tap timing window: each press acts immediately, and the mode's 600 ms idle expiry determines which state you are in.
4. `LH0` always enters or refreshes Systems+ from any FTL state.
5. Battery is a direct central key in the systems map; it first clears the mode and Shift, then sends `B` and returns to combat.
6. Teleporter power moves to `LT0`, outside the central area, and Artillery power to `LB1`.
This assumes teleport power can be treated as rare along with Send/Recall; say if that is wrong.
7. LB0 double-tap exits FTL from every FTL layer, including combat, without holding a thumb.
The existing 200 ms protection remains; one tap sends nothing.
8. Keep fixed named system controls for this trial, not the unverified numbered-system shortcuts.
The menu constants remain defined but unbound, as approved.
9. A right-hand keyboard press cancels timed power state before using its existing binding.
Trackpad clicks do not cancel or extend the mode and can remain shifted until timeout or explicit cancellation.

Recommendation: try this map if the extra tap for weapon depowering and the safe-constituent Jump combo feel acceptable.
It uses three FTL maps, keeps Battery out of combos, avoids simultaneous-thumb requirements, and preserves direct weapon positions.
If you prefer numbered systems before deciding the map, or want a different trade-off, say so rather than approving this candidate.

Approval settles the layout, not the helper's correctness.
The first `/implement` step remains the already agreed timed-helper verification; a failed check stops the change without silently adding a firmware module or weakening the behavior.

Answer:
1. Not a fan of Pause doing both actions at the same time. It will cause accidental unpauses.
3. Can we make LH2 so that tapping once activates Sys-, twice activates Sys+, and holding activates weapons-?
   In that case I guess drone depowering should go to the top row to LH4-LH2.
The rest of the questions are either moot before we get the answer to the question above or were answered directly under the layout previews.

## Layout previews for Q10

Historical proposal, now rejected in part by the comments below.
Preserved for context; these are neither the current firmware nor an approved layout.
Only the left half is shown; the right half keeps its existing bindings.
Blank keys do nothing.
The three thumb keys are `LH2`, `LH1`, and `LH0`, left to right.

### Combat

```text
┌──────────────┬──────────────┬──────────────┬──────────────┬──────────────┬──────────────┐
│     Send     │    Cloak     │     Hack     │     Mind     │ mo Autofire  │    Recall    │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│   Settings   │   Weapon 1   │   Weapon 2   │   Weapon 3   │   Weapon 4   │ tog Autofire │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│   Drone 1    │   Drone 2    │   Drone 3    │  Ship menu   │              │   Exit 2x    │
└──────────────┴──────────────┴──────────────┼──────────────┼──────────────┼──────────────┤
                                             │   Systems−   │    Pause     │   Systems+   │
                                             └──────────────┴──────────────┴──────────────┘
```

The combat combos use these pairs of visible keys:

```text
Weapon 1 + Weapon 2   → Crew home
Weapon 2 + Weapon 3   → Close doors
Weapon 3 + Weapon 4   → Open doors
Mind + mo Autofire   → Jump
```

Exit does not need to be on combat layer.
Send and recall are in nasty places.
Settings was fine where it was.
Don't put anything else on the leftmost column.
Try to maintain the cloak mind hack order.
Swap the open and close doors combos.
Bottom row is easier to reach than leftmost and rightmost columns.

### Systems+

Tap the right thumb, `LH0`, to enter.
The `+` keys add system power.

```text
┌──────────────┬──────────────┬──────────────┬──────────────┬──────────────┬──────────────┐
│              │   Cloak +    │    Hack +    │    Mind +    │   Battery    │  Teleport +  │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │  Shields +   │  Engines +   │ Med/Clone +  │     O2 +     │              │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │              │              │              │ Artillery +  │   Exit 2x    │
└──────────────┴──────────────┴──────────────┼──────────────┼──────────────┼──────────────┤
                                             │   Systems−   │    Pause     │   Systems+   │
                                             └──────────────┴──────────────┴──────────────┘
```
Here again retain the cloak mind hack order.
Teleport is more common than artillery.

### Systems−

From combat, tap the left thumb, `LH2`, once.
The `−` keys remove system power.
Notice that the left thumb now selects Weapons−.

```text
┌──────────────┬──────────────┬──────────────┬──────────────┬──────────────┬──────────────┐
│              │   Cloak −    │    Hack −    │    Mind −    │   Battery    │  Teleport −  │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │  Shields −   │  Engines −   │ Med/Clone −  │     O2 −     │              │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │              │              │              │ Artillery −  │   Exit 2x    │
└──────────────┴──────────────┴──────────────┼──────────────┼──────────────┼──────────────┤
                                             │   Weapons−   │    Pause     │   Systems+   │
                                             └──────────────┴──────────────┴──────────────┘
```

Battery activates and returns to combat in either systems view.

### Weapons−

From Systems−, tap the left thumb, `LH2`, again.
The left thumb then takes you back to Systems−.

```text
┌──────────────┬──────────────┬──────────────┬──────────────┬──────────────┬──────────────┐
│              │              │              │              │              │              │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │  Weapon 1−   │  Weapon 2−   │  Weapon 3−   │  Weapon 4−   │              │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│   Drone 1−   │   Drone 2−   │   Drone 3−   │              │              │   Exit 2x    │
└──────────────┴──────────────┴──────────────┼──────────────┼──────────────┼──────────────┤
                                             │   Systems−   │    Pause     │   Systems+   │
                                             └──────────────┴──────────────┴──────────────┘
```

In all power modes, Pause also returns to combat; otherwise they expire after 600 ms idle.
No selector needs to be held.

Q10 feedback supersedes this proposal.
The next step is resolving the requested thumb selector before drawing a revised map.

## Round 4

### Q11. Can we split the selector across the two thumbs instead?

The requested single-tap/double-tap/hold key is expressible with native ZMK behaviors, but neither straightforward nesting is safe enough to recommend for fast weapon chords.

- With tap-dance outside hold-tap, an early finger press while LH2 is held can reach the combat layer before Weapons− activates.
- Reversing the nesting fixes a fresh hold, but a preceding unresolved tap can activate Systems− after Weapons− starts.

These findings come from source inspection, not a hardware test.
The [plan records the evidence](ftl-comfort-layout.md#selector-feasibility-and-proposed-alternative).
I would not make correct depowering depend on waiting out those cases.

Recommendation: remove double-tap from power selection and use this thumb cluster in every FTL layer:

```text
         LH2                   LH1                  LH0
┌─────────────────────┬─────────────────────┬─────────────────────┐
│    tap Systems−     │        Pause        │    tap Systems+     │
│    hold Weapons−    │                     │     hold Cancel     │
└─────────────────────┴─────────────────────┴─────────────────────┘
```

Proposed details:

- Start with a 200 ms hold threshold for each selector.
System taps act on release, without waiting for a double-tap window.
- For Systems− or Systems+, tap and release the thumb before pressing an adjustment key.
If another key goes down while the thumb is still held, the hold action wins: Weapons− for LH2, Cancel for LH0.
- Weapons− lasts only while LH2 is held.
Release it before pressing Pause, so no two-thumb press is needed.
- Holding LH0 cancels the power mode and its owned Shift without sending Space or another game key.
Do not roll a system-power tap into a finger press while still holding LH0, because that means Cancel instead.
- LH1 sends Pause only and does not cancel a timed system mode.
That mode can still expire after 600 ms idle.
- Selecting a mode replaces the previous mode; releasing Weapons− or cancelling returns to combat, never to an earlier power mode.
Repeated taps of the same system selector refresh that mode.
- I read your top-row drone positions `LH4–LH2` as `LT4–LT2`.
Say if you meant different positions.

This uses ordinary hold-taps, not either nested tap-dance construction.
It still needs the already required timed-layer and cleanup checks before implementation can proceed.

Would you accept this thumb arrangement and its tap-release/hold distinction?
If keeping all three selection gestures on LH2 is essential, say so; that branch stays blocked rather than silently gaining custom firmware code.
We will redraw the full layouts after settling this choice.

Answer:

Make LH0 double tap cancel on all layers if that's feasible. If not then this works.
Pause should also cancel any layers and go to combat. It should not be the only way to cancel though.


## Round 5

### Q12. Does this revised complete layout work for you?

Historical proposal, superseded by the comments below and Round 6.

Your Q11 answer restores Pause-and-cancel as long as cancel-only also exists.
LH0 can use a plain two-entry tap-dance, avoiding the nested tap/hold constructions investigated earlier.
Its full interaction with timed layers, modifiers, and combos still needs the runtime gate.
If the LH0 gesture fails validation, use your accepted tap-Systems+/hold-Cancel fallback instead.

Only the left half is shown; right-half bindings remain transparent and unchanged.
Blank left keys are `&none`, not transparency into normal typing.
The thumb cluster is `LH2 LH1 LH0`, left to right.
`Sys− / W−` means tap for Systems−, hold for Weapons−.
`Sys+/Cancel` means single tap for Systems+, double tap for cancel.
`Pause/Combat` clears power state and sends Space once.

### Combat

```text
┌──────────────┬──────────────┬──────────────┬──────────────┬──────────────┬──────────────┐
│   Settings   │    Cloak     │     Mind     │     Hack     │ mo Autofire  │   Drone 3    │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │   Weapon 1   │   Weapon 2   │   Weapon 3   │   Weapon 4   │ tog Autofire │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │     Send     │    Recall    │  Ship menu   │   Drone 1    │   Drone 2    │
└──────────────┴──────────────┴──────────────┼──────────────┼──────────────┼──────────────┤
                                             │  Sys− / W−   │ Pause/Combat │ Sys+/Cancel  │
                                             └──────────────┴──────────────┴──────────────┘
```

That drone layout is atrocious.
Autofire should be in LT4. Tapping would toggle it, holding should be momentary override.
Send/recall could use the same. Tap for recall, hold for send.
Then Drones 1-3 could be in LB4-2.

### Systems+

```text
┌──────────────┬──────────────┬──────────────┬──────────────┬──────────────┬──────────────┐
│              │   Cloak +    │    Mind +    │    Hack +    │   Battery    │              │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │  Shields +   │  Engines +   │ Med/Clone +  │     O2 +     │              │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │              │              │ Artillery +  │  Teleport +  │   Exit 2x    │
└──────────────┴──────────────┴──────────────┼──────────────┼──────────────┼──────────────┤
                                             │  Sys− / W−   │ Pause/Combat │ Sys+/Cancel  │
                                             └──────────────┴──────────────┴──────────────┘
```

Put battery to LT4 and shift rest to right.
### Systems−

```text
┌──────────────┬──────────────┬──────────────┬──────────────┬──────────────┬──────────────┐
│              │   Cloak −    │    Mind −    │    Hack −    │   Battery    │              │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │  Shields −   │  Engines −   │ Med/Clone −  │     O2 −     │              │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │              │              │ Artillery −  │  Teleport −  │   Exit 2x    │
└──────────────┴──────────────┴──────────────┼──────────────┼──────────────┼──────────────┤
                                             │  Sys− / W−   │ Pause/Combat │ Sys+/Cancel  │
                                             └──────────────┴──────────────┴──────────────┘
```

Same here.

### Weapons−

```text
┌──────────────┬──────────────┬──────────────┬──────────────┬──────────────┬──────────────┐
│              │   Drone 1−   │   Drone 2−   │   Drone 3−   │              │              │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │  Weapon 1−   │  Weapon 2−   │  Weapon 3−   │  Weapon 4−   │              │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │              │              │              │              │   Exit 2x    │
└──────────────┴──────────────┴──────────────┼──────────────┼──────────────┼──────────────┤
                                             │  Sys− / W−   │ Pause/Combat │ Sys+/Cancel  │
                                             └──────────────┴──────────────┴──────────────┘
```
Put the following actions to following locations on this layer:
- Drones to the bottom row as they are in combat.
- Save crew to LB1
- Select all crew (Q) on LM0
- Shop, inventory, crew manifest to LT4, LM4, LB4

Combat combos, with the door actions swapped as requested:

```text
Weapon 1 + Weapon 2  → Crew home
Weapon 2 + Weapon 3  → Open doors
Weapon 3 + Weapon 4  → Close doors
Mind + mo Autofire  → Jump
```

The new Jump pair is `LT3+LT1`, which skips over Hack at LT2.
Hack does not participate.
This is not an adjacent pair; please confirm that the reach works rather than approving it just because the two actions are safe constituents.
An accidental recognized chord can Jump; a near miss can arm Mind or hold Alt.
The proposed 30 ms combo window also delays participating single keys.

The remaining choices and behavior:

- Send/Recall are now LB4/LB3, Settings is back at LT5, and the rest of the leftmost column is empty.
The inner column still carries rare Drone 3, toggled autofire, and Drone 2.
Is that acceptable, or would you move one of those?
- Teleporter power is LB1, with Artillery at LB2 instead of assigning the easier reach to Artillery.
Drone depowering is LT4/LT3/LT2.
- LH2 tap enters Systems− on release; hold selects Weapons−.
Start with a 200 ms hold threshold, with an early finger press selecting the hold action.
Release LH2 before pressing Pause or LH0; no two-thumb press is required.
- LH0 single tap selects Systems+ after a proposed 200 ms window, or earlier when another key interrupts.
A second downstroke inside that window, without an intervening key, cancels to combat without sending Space.
Two slower taps are not a cancel gesture.
This applies to all FTL layers, not normal typing.
- Pause clears power state before one Space.
Battery clears system mode before B and returns to combat.
Neither must leave mode-owned Shift behind.
- System modes expire after 600 ms idle; left-hand activity refreshes them, but trackpad activity does not.
Right-hand keyboard input cancels power mode before its normal binding continues.
- Releasing Weapons− returns to combat, never to an earlier system mode.
Exit 2x is on LB0 in power maps only; it keeps the existing 200 ms protection.

Recommendation: try this corrected map with those timing defaults if the non-adjacent Jump pair and rare inner-column keys are comfortable.
If not, name the specific changes rather than approving the whole layout.
You can comment directly beneath any diagram as before.
The [plan](ftl-comfort-layout.md#layout-revision-after-q10) contains the complete transition and cleanup contract.

Answer:
Added comments under the layout previews.
Maybe we should have one more layer with LH1 hold?

## Round 6

Q12 requested changes, not approval.
I have recorded grouped drones on LB4/LB3/LB2, LT4 combined autofire, combined Recall/Send, and Battery followed by Cloak/Mind/Hack on both system maps.
The proposed new crew/menu actions require resolving the conflicts below.
The shortcut lookup recovered your original Save crew Return assignment and corroborated Select all crew Q.
Vanilla settings records expose separate Store, Inventory, and Crew-manifest bindings.
O/I/E remain the accepted menu assignments, but published settings use O for Options and E for drone power, so your game configuration needs matching assignments and conflict checks before use.
See the [evidence in the plan](ftl-comfort-layout.md#crew-and-menu-shortcut-evidence).

### Q13. Use LH1 hold for a separate, unshifted Utilities layer?

Historical proposal; rejected by the answer below because Pause must fire on downstroke.

On Weapons−, Inventory at LM4 collides with Weapon 1−, and Crew manifest at LB4 collides with Drone 1−.
Weapons− also holds Shift, which we should not blindly send with menu or crew shortcuts.

Recommendation: keep weapons/drones in Weapons− and put your requested non-power actions in Utilities:

```text
┌──────────────┬──────────────┬──────────────┬──────────────┬──────────────┬──────────────┐
│              │     Shop     │              │              │              │              │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │  Inventory   │              │              │              │   All crew   │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │  Crew menu   │              │              │  Save crew?  │              │
└──────────────┴──────────────┴──────────────┼──────────────┼──────────────┼──────────────┤
                                             │  Sys− / W−   │ Pause/Utils  │ Sys+/Cancel  │
                                             └──────────────┴──────────────┴──────────────┘
```

The thumb cluster is LH2 / LH1 / LH0.
`Pause/Utils` means tap Pause-and-cancel, hold Utilities.
Other thumb roles stay as already proposed; release LH1 before using another thumb.
Blank positions do nothing.
The question mark on Save crew refers to Q15's protection choice; the keycode is your original Return assignment.
This is a placement sketch, not another complete candidate.

The cost is Pause timing:

- A short LH1 press pauses and clears other modes **on release**, not on press.
- Holding LH1 for a proposed 200 ms enters Utilities without sending Space.
Releasing it returns to combat, also without Space.
- Use tap-preferred recognition so a quick Pause-to-finger roll followed by thumb release remains Pause then the combat action.
If you keep LH1 held through the threshold, that finger input can instead become a utility action.
- Entering Utilities clears any old power mode and its owned Shift.
No previous mode returns when you release the thumb.
- You cannot pause while keeping Utilities held; release LH1, then tap it to pause.

Do you accept that Pause trade-off and moving the crew/menu actions to this separate layer?
If Pause must still fire on the downstroke, keep LH1 Pause-only and we will choose another utility-layer entry.

Answer:
Pause should trigger on downstroke.
Utils layer still needs some planning.
The proposed places for shop inventory crew menu were supposed to be on L5s, not L4s like I said.
But with a separate utils layer the placing could be something else.
I think it should also have shortcuts for picking crew members (F1-8).
Also + and - for adjusting text size.

### Q14. Are these tap/hold timings acceptable for Autofire and Recall/Send?

Recommendation: start with ordinary tap-preferred 200 ms hold-taps:

- LT4 tap: toggle autofire once with Left Ctrl on release.
- LT4 hold: after 200 ms, hold Left Alt until release.
- Recall/Send, proposed on LB1: a short tap sends R once; holding through 200 ms sends T once, with no repeat while the key remains held.

The trackpad cannot resolve the keyboard's hold decision.
For momentary autofire, you would need to wait for Alt to activate before clicking.
A quick press/click/release under 200 ms still counts as a toggle tap.
This changes the previous immediate momentary-autofire key.

Is that acceptable, or is immediate Alt on press important enough to choose different semantics?

Answer:
SGTM, though 200ms sounds a bit long.

### Q15. Should Save crew require a double-tap?

Your new request supersedes the earlier decision to omit Save crew.
The action overwrites saved crew positions, which was the reason for omitting it.

Recommendation: Utilities LB1 single tap does nothing; double-tap within 200 ms saves once.
That protects it even if you accidentally reach the key while using the utility layer.
Use a plain single tap instead if holding the utility layer is enough protection for you.

Save crew will use your original Return assignment, not the published vanilla Slash default.
Which protection do you prefer?

Answer:
Double tap sgtm

Also: we should revert all keys to defaults. This layout is not going to be backwards compatible with a regular keyboard anymore anyway.

## Round 7

Your latest answers keep Pause on downstroke, accept merged tap/hold gestures, approve double-tap Save crew, and switch FTL outputs to vanilla defaults.
The old custom Return-save/Tab-home and O/E menu proposals no longer govern the design.
The default table is now in the plan: Jump J, autofire toggle V, Crew home Return, Save crew Slash, crew F1–F8, and the other corroborated vanilla outputs.
The momentary autofire modifier is the remaining platform-specific uncertainty, addressed by Q19.
For Autofire and Recall/Send, I propose trying 150 ms rather than 200 ms; Save/Exit protection stays at 200 ms.

### Q16. Can combat LB0 be a direct momentary Utilities key?

Historical proposal; the answer below rejects non-thumb layer holds.

Pause stays dedicated to LH1.
The power thumbs are already assigned, so I recommend a plain held key on combat LB0 for Utilities.
It replaces Ship menu there; Ship menu moves inside Utilities.
This needs no tap/hold decision and does not overload Pause or add nested gestures.

A provisional placement sketch, with your corrected L5 menu column:

```text
┌──────────────┬──────────────┬──────────────┬──────────────┬──────────────┬──────────────┐
│     Shop     │ Crew 1 / F1  │ Crew 2 / F2  │ Crew 3 / F3  │ Crew 4 / F4  │  Ship menu   │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│  Inventory   │ Crew 5 / F5  │ Crew 6 / F6  │ Crew 7 / F7  │ Crew 8 / F8  │   All crew   │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│  Crew menu   │              │   Text −?    │   Text +?    │   Save 2x    │  hold Utils  │
└──────────────┴──────────────┴──────────────┼──────────────┼──────────────┼──────────────┤
                                             │  Sys− / W−   │ Pause/Combat │ Sys+/Cancel  │
                                             └──────────────┴──────────────┴──────────────┘
```

- The eight central top/home keys select crew 1–8.
- All crew remains LM0; Save 2x remains LB1.
- Shop, Inventory, Crew menu occupy LT5/LM5/LB5.
Shop/Crew-menu outputs depend on Q17.
- The text-size positions are only placeholders until Q18.
- Hold combat LB0, use utility keys, release LB0 to return to combat.
You must be comfortable selecting crew while a finger holds LB0.
- Power-map LB0 remains Exit 2x, so first cancel power mode or Pause back to combat before entering Utilities.
- Pause remains available on downstroke; it also cancels Utilities.
A later release of the held Utilities key must not restore a mode.

If that finger hold is awkward, an alternative is **tap LB0 for timed Utilities**, initially the same 600 ms idle interval.
It frees every finger, but trackpad activity will not extend it.
After expiry, the next crew-position key would use its combat action instead.
I prefer the held version to avoid that surprise while selecting and moving crew.

Which access do you prefer, or would another physical key be more comfortable?

Answer:
Having to hold anything but thumb keys for layer switch sucks.

### Q17. What should happen to actions with no vanilla default shortcut?

The vanilla settings records leave Store and Crew manifest unbound.
Using defaults everywhere cannot also give those actions working dedicated keys without exceptions.

Recommendation: use vanilla defaults wherever assigned, and allow custom assignments only for otherwise unbound actions.
Choose the actual unused keys after the default audit, rather than retaining conflicting O/E by habit.
You would configure those exceptions in FTL yourself; this planning task does not edit the game's settings.

Alternatively, use strict defaults and leave Store/Crew-manifest keys inactive, keeping trackpad access.
Do you want the small exceptions or strict defaults?

Answer: small exceptions.

### Q18. What should the text-size + / − keys control?

The lookup confirmed native FTL event-text resizing.
Its defaults are counterintuitive: **minus enlarges**, **equals shrinks**.
Neither uses Ctrl or Shift, and these are not whole-interface or browser zoom commands.

Recommendation: label the utility keys by effect, with Text + sending MINUS and Text − sending EQUAL.
Is native event-text resizing what you meant?
If you meant another application or whole-interface zoom, name that target instead.

Answer: sgtm.

### Q19. Which platform's default Autofire Modifier should we target?

The common PC default is Left Ctrl, but the collected vanilla settings disagree: one uses Left Alt, with Left Ctrl assigned to right-click emulation.
The modifier is configurable, not a hardwired universal shortcut.
The Mac factory value has not been established by the available evidence.

Do you play FTL on macOS, Windows, or Linux, and which version if known?
If you want one explicit cross-platform reference instead of that platform's factory values, say so.

Recommendation: target the defaults of the platform you actually play on.
Do not silently replace Alt with Ctrl on macOS until the Autofire Modifier and right-click bindings are verified.
No game reset is needed just to answer this platform question.

Answer:
Playing on Windows, sometime in the future maybe on Linux.

## Round 8

Recorded: no finger-held layers, small custom menu exceptions, native event-text resizing, and Windows defaults.
The proposed momentary autofire output is now Left Ctrl, not Alt.
The source check confirms that an undecided merged finger gesture delays Pause; Q23 must settle this before full-map approval.

### Q20. Use LH0 hold for Utilities and move Cancel to LB0?

Recommendation: give each outer thumb a system tap and a momentary hold.
LH1 stays dedicated to Pause.

```text
         LH2                   LH1                  LH0
┌─────────────────────┬─────────────────────┬─────────────────────┐
│    tap Systems−     │    Pause/Combat     │    tap Systems+     │
│    hold Weapons−    │     downstroke      │   hold Utilities    │
└─────────────────────┴─────────────────────┴─────────────────────┘
```

- Both outer thumbs use ordinary 200 ms hold-preferred recognition, not nested tap-dances.
For a system adjustment, tap and release before pressing the finger key.
Pressing a finger while LH0 stays down selects Utilities immediately; LH2 similarly selects Weapons−.
- This removes LH0 double-tap Cancel.
LB0 becomes cancel-only on combat and Utilities, firing on press with no Space.
- On power maps, LB0 becomes **tap Cancel / double-tap Exit**.
The single action waits up to 200 ms from the first downstroke, or resolves when another key interrupts.
A second press inside the window exits instead of running the single action.
There is still no exit on combat.
- Entering Utilities clears the prior power mode and owned Shift.
Releasing LH0 returns to combat, with no previous-mode restoration.
- Release LH0 before pressing Pause or LH2.
No two thumb keys need to be held together.
- Cancelling a held mode must make its later release harmless, even if another mode has since started.

Proposed Utilities, with all finger keys free:

```text
┌──────────────┬──────────────┬──────────────┬──────────────┬──────────────┬──────────────┐
│  Shop / F9   │ Crew 1 / F1  │ Crew 2 / F2  │ Crew 3 / F3  │ Crew 4 / F4  │  Ship menu   │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│  Inventory   │ Crew 5 / F5  │ Crew 6 / F6  │ Crew 7 / F7  │ Crew 8 / F8  │   All crew   │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│  Crew / F10  │              │ Text smaller │ Text larger  │   Save 2x    │    Cancel    │
└──────────────┴──────────────┴──────────────┼──────────────┼──────────────┼──────────────┤
                                             │  Sys− / W−   │ Pause/Combat │ Sys+ / Utils │
                                             └──────────────┴──────────────┴──────────────┘
```

Text smaller sends Equals; Text larger sends Minus.
Shop and Crew menu use the proposed exceptions in Q22.
This is still a placement proposal, not final map approval.

Do you accept the LH0 change and LB0 cancel/exit behavior?
If retaining double-tap Cancel on LH0 matters more, the remaining alternative is a tapped timed Utilities entry, not a finger hold.

Answer:
LGTM.
So how would cancel work on sys- / w- / sys+ layers?

### Q21. Is LT4 + LT2 comfortable for Jump?

Recommendation: use the combat Autofire key plus Mind.
They skip over Cloak without including it.

```text
         LT4           LT3           LT2           LT1
       Autofire        Cloak         Mind          Hack
          └───────────── Jump ─────────┘
```

This keeps Jump in the central area and leaves the three comfortable home-row pairs for Crew home, Open doors, and Close doors.
Use the proposed 30 ms combat-only combo window.
A recognized chord sends J and suppresses both constituents.
A near miss can arm Mind or invoke Autofire instead, including a toggle if the merged tap is retained.
The single-key timing and pending-gesture Pause interaction still need verification.

Is this non-adjacent pair comfortable, or would you rather put Jump on a dedicated rare/outside position?
Do not include Cloak, Hack, or Battery in a replacement combo.

Answer:
That does not look comfy. And jump is so common that it definitely should be comfy.

### Q22. Use F9 for Shop and F10 for Crew manifest?

Recommendation: these are the two custom exceptions.
Keep Inventory on default I and all other game outputs on the Windows reference defaults.

Neither F9 nor F10 is assigned in either complete vanilla settings record examined.
They avoid the O/Options and E/drone-power collisions.
You would assign Store to F9 and Crew manifest to F10 in FTL; this task does not change game settings.

Plain F9/F10 do not collide with Steam's default Shift+Tab overlay or F12 screenshot shortcuts.
Windows conventionally uses F10 for application menus, so verify its behavior inside FTL and any personal desktop shortcuts.
Utilities must emit these unshifted.
Future Linux desktop shortcuts need the same check.

Accept those two assignments, or name preferred alternatives?

Answer:
Those work fine.

### Q23. Which requirement wins when a finger tap/hold is still undecided?

The source check found a conflict I missed in the earlier proposal.
Making LH1 a direct key is not enough: ZMK's pending tap-preferred finger gesture captures its downstroke before Pause's binding can run.
There is no installed hold-tap setting that exempts Pause while preserving the proposed tap/hold rules.

With a 150 ms threshold, press Autofire at 0 ms, then Pause at 30 ms while keeping Autofire down.
Pause waits until about 150 ms, after the autofire hold decision.
The same problem occurs with Recall/Send.

Choose the trade-off explicitly:

1. **Keep the merges; accept the remaining decision delay before Pause.**
LH1 has no gesture of its own, so it is immediate when no other hold-tap is pending.
During an undecided merged press it can wait until that key's release or the 150 ms threshold.
2. **Keep the merges; another keyboard press selects hold immediately.**
This is hold-preferred, not the previously proposed behavior.
Pressing Pause during a short Autofire press selects Ctrl rather than toggle V.
Pressing it during a short Recall/Send press selects Send T rather than Recall R.
The hold action runs before Pause, and Ctrl may accompany Space.
3. **Keep downstroke Pause as the priority; split these actions again.**
Use direct momentary autofire plus a separate toggle, and separate Recall and Send.
We must place the extra keys in the next complete map rather than quietly dropping either action.

Recommendation: option 3 if downstroke Pause is non-negotiable.
I would not choose option 2 for teleporting, because a Pause press can turn an intended Recall into Send.
Option 1 is viable only if you explicitly accept the delay.
Splitting removes these two delay sources; the complete event path still needs its existing verification gate.

Which option do you want?
This question concerns held finger gestures, not holding a finger to switch layers.

Answer:
Please elaborate. What exactly is the problem here? I'm not following.
Pause should aways trigger with a downstroke.

## Round 9

Q20's thumb arrangement and Q22's F9/F10 assignments are accepted.
The non-adjacent Jump chord is rejected.
Q23 asks for an explanation, not a change to the Pause requirement.

### How Cancel works on the power maps

LB0 is the rightmost bottom-row finger key.

| Mode | What you do | What happens |
| --- | --- | --- |
| Systems− | Tap LB0 once | Cancel the timed mode, release its held Shift, return to combat; no Space |
| Systems+ | Tap LB0 once | Cancel the timed mode and return to combat; no Space |
| Weapons− | Release LH2 | Cancel the held mode, release its Shift, return to combat |
| Weapons− while keeping LH2 held | Tap LB0 once | Same cancel as Systems−; releasing LH2 afterward must do nothing extra |

In all three power maps, LB0 has two possible actions.
A single tap cancels after its 200 ms double-tap window, or earlier if another key interrupts.
A second press inside that window exits FTL instead.
Thus LB0 cancellation is not immediate on these maps; releasing the Weapons− thumb is the faster way out of that held mode.
No cancel-only route sends Space or changes whether the game is paused.

On combat and Utilities, LB0 has no Exit action and cancels on press without that double-tap wait.
Pressing Pause is always a different request: cancel the mode and send Space once.
Release a held layer thumb before pressing Pause, as agreed.
All cleanup and delayed-release behavior still need the planned runtime checks.

Comment: I think the last item on that list is unnecessary. Other than that sgtm.

### Q24. Is a queued downstroke acceptable, or must Pause bypass another key's decision?

I conflated two different requirements in the previous explanation.
None of these proposals makes Pause wait for **its own release**.
The problem is that **another key** can delay delivery of Pause's press.

The combined Autofire key cannot immediately know which action you intend.
It waits to see whether you release it for a toggle or keep holding it for momentary autofire.
During that undecided interval, the proposed ZMK behavior also queues other keyboard events.

Example, using the proposed 150 ms threshold:

| Time | Physical action | What reaches the game |
| --- | --- | --- |
| 0 ms | Press Autofire and keep it held | No autofire output yet; deciding tap or hold |
| 30 ms | Press Pause and keep it held | Its press is queued |
| About 150 ms | Both keys are still held | Autofire becomes held Ctrl; then the queued Space press is delivered |
| 300 ms | Release Pause | Space release is delivered |

Notice that Pause fires while you are still holding its key.
Its release did not trigger it.
But the game received its downstroke about 120 ms late.

If you had quickly pressed and released Pause at 30/60 ms, both events could arrive together around 150 ms.
It would then appear to pause after you lifted your finger, because the keyboard had queued the entire tap.
The proposed Recall/Send key causes the same delay while undecided.
With no other undecided gesture, Pause's own binding runs on downstroke without a tap/hold decision.

Which do you mean?

- **A. Pause must be press-triggered, but this short queueing delay during another gesture is acceptable.**
Keep the merged finger gestures, with 150 ms as the proposed starting threshold.
- **B. Pause must not wait for that other gesture either.**
The proposed tap-preferred merges cannot meet this.
Recommendation for that strict requirement: split Autofire toggle/hold and Recall/Send into direct controls, then review their placements.

Based on your repeated downstroke requirement, I would keep B as the requirement unless you explicitly choose A.
There is no silent switch to hold-preferred; that would change Recall into Send when Pause interrupts it.
This clarification concerns event delivery, not a proposal to bind Space to Pause's release.

Answer: Can we just make pressing pause trigger both pause and AF toggle immediately if Pause is pressed while AF is still waiting to see what should happen?

### Q25. Give Jump the middle home-row pair and Crew home a short diagonal?

Recommendation: reserve Weapon 2 + Weapon 3 for Jump.
That is LM3+LM2, one of the pairs you said feels most natural.
Keep all three adjacent home-row pairs useful, and try a neighboring-column diagonal for Crew home:

```text
       LT4            LT3            LT2            LT1
┌──────────────┬──────────────┬──────────────┬──────────────┐
│   Autofire   │    Cloak     │     Mind     │     Hack     │
├──────────────┼──────────────┼──────────────┼──────────────┤
│   Weapon 1   │   Weapon 2   │   Weapon 3   │   Weapon 4   │
└──────────────┴──────────────┴──────────────┴──────────────┘
       LM4            LM3            LM2            LM1
```

| Combat chord | Action |
| --- | --- |
| Weapon 1 + Weapon 2, LM4+LM3 | Open doors |
| Weapon 2 + Weapon 3, LM3+LM2 | Jump |
| Weapon 3 + Weapon 4, LM2+LM1 | Close doors |
| Mind + Weapon 4, LT2+LM1 | Crew home |

The Crew-home chord uses Mind above Weapon 3 and Weapon 4 one column to its right.
It needs two fingers on neighboring columns, not a same-finger vertical press.
No Cloak, Hack, or Battery activation key participates.
It is a proposed reach, not a claim that it will feel comfortable.

The cost is moving Crew home off its old home-row pair and moving Open doors to that pair.
Recognized pairs suppress their individual actions.
A near miss on Crew home can arm Mind or select Weapon 4; weapon-key rolls can still trigger the home-row combos.
The proposed combat-only window remains 30 ms.

I also checked a three-key Crew-home chord sharing the Jump pair.
ZMK supports it, but a late third key or an unrelated key release can commit Jump instead.
I would avoid that extra failure mode rather than make you learn a tighter three-finger gesture.

Do these four chords look comfortable, especially Mind + Weapon 4?
If not, tell me which reach fails; I will keep Jump on a comfortable home-row pair rather than move it to an outside key.

Answer:

W1+W2: open doors
W2+W3: close doors
W3+W4: jump

Not sure where crew home should go. Please show me all the layers as they would be after these decisions so I can figure out what to do about it.

## Round 10

Here are all five FTL views with your latest Open / Close / Jump assignments.
Systems+ and Systems− are two states of the same shared system layer, not separate copies.
The right half stays unchanged and is omitted.
Blank left keys do nothing.

Crew home is intentionally **unassigned** in these previews.
Nothing has replaced it silently.
The Autofire and Recall/Send labels retain your requested merges while we settle their interruption rules below.
These are planning previews, not current firmware or approval to implement.

Thumbs in every view, left to right:
- LH2: tap Systems−, hold Weapons−.
- LH1: Pause-and-cancel, with no hold action.
- LH0: tap Systems+, hold Utilities.

Release one layer thumb before pressing another thumb.
System taps require thumb release before the adjustment key; an early finger press selects the thumb's hold action.
The ordinary thumb threshold remains 200 ms.

### Combat

```text
┌──────────────┬──────────────┬──────────────┬──────────────┬──────────────┬──────────────┐
│   Settings   │   Autofire   │    Cloak     │     Mind     │     Hack     │              │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │   Weapon 1   │   Weapon 2   │   Weapon 3   │   Weapon 4   │              │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │   Drone 1    │   Drone 2    │   Drone 3    │Recall / Send │    Cancel    │
└──────────────┴──────────────┴──────────────┼──────────────┼──────────────┼──────────────┤
                                             │  Sys− / W−   │ Pause/Combat │ Sys+ / Utils │
                                             └──────────────┴──────────────┴──────────────┘
```

Your selected combat-only combos:

```text
Weapon 1 + Weapon 2  → Open doors
Weapon 2 + Weapon 3  → Close doors
Weapon 3 + Weapon 4  → Jump
```

Autofire at LT4 is tap-toggle / hold-momentary, sending V / Left Ctrl.
Recall/Send at LB1 is tap Recall R / hold one-shot Send T.
The proposed finger threshold is 150 ms; interruption behavior is Q27.
Settings sends Escape.
No Crew-home combo is assigned.
Combat LT0 and LM0 are vacant, but they are outside your preferred central area.

Comments:
Can we make Settings also cancel?
Put crew home to LM0 on this layer only.

### Systems+

```text
┌──────────────┬──────────────┬──────────────┬──────────────┬──────────────┬──────────────┐
│              │   Battery    │   Cloak +    │    Mind +    │    Hack +    │              │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │  Shields +   │  Engines +   │ Med/Clone +  │     O2 +     │              │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │              │              │ Artillery +  │  Teleport +  │Cancel/Exit2x │
└──────────────┴──────────────┴──────────────┼──────────────┼──────────────┼──────────────┤
                                             │  Sys− / W−   │ Pause/Combat │ Sys+ / Utils │
                                             └──────────────┴──────────────┴──────────────┘
```

Tap and release LH0 to enter.
The named system keys add power.
Battery at LT4 ends the mode before sending B.
LB0 tap cancels after the 200 ms dance window or an interrupting key; double-tap inside that window exits FTL.
The mode otherwise ends after 600 ms idle, with the agreed held-key and refresh protections.

Comments:
Move Cancel to LT5, leave exit where it is.
Make LM0 Power all weapons (W).

### Systems−

```text
┌──────────────┬──────────────┬──────────────┬──────────────┬──────────────┬──────────────┐
│              │   Battery    │   Cloak −    │    Mind −    │    Hack −    │              │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │  Shields −   │  Engines −   │ Med/Clone −  │     O2 −     │              │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │              │              │ Artillery −  │  Teleport −  │Cancel/Exit2x │
└──────────────┴──────────────┴──────────────┼──────────────┼──────────────┼──────────────┤
                                             │  Sys− / W−   │ Pause/Combat │ Sys+ / Utils │
                                             └──────────────┴──────────────┴──────────────┘
```

Tap and release LH2 to enter.
The system bindings are the same keys as Systems+, with mode-owned Shift held across adjustments.
Battery and Cancel/Exit must release that Shift before their next action.
LB0 has the same tap-Cancel / double-Exit behavior as Systems+.
The same 600 ms idle contract applies.

Comments:
Move Cancel to LT5, leave exit where it is.

### Weapons−

```text
┌──────────────┬──────────────┬──────────────┬──────────────┬──────────────┬──────────────┐
│              │              │              │              │              │              │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │  Weapon 1−   │  Weapon 2−   │  Weapon 3−   │  Weapon 4−   │              │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │   Drone 1−   │   Drone 2−   │   Drone 3−   │              │   Exit 2x    │
└──────────────┴──────────────┴──────────────┼──────────────┼──────────────┼──────────────┤
                                             │  Sys− / W−   │ Pause/Combat │ Sys+ / Utils │
                                             └──────────────┴──────────────┴──────────────┘
```

Hold LH2 to enter; release LH2 to return to combat and release owned Shift.
Weapons and drones use plain numbers beneath that held Shift.

I read your comment about the unnecessary last item as removing **LB0 single-tap Cancel while LH2 remains held**.
Accordingly, this preview retains only protected Exit 2x on LB0: one tap does nothing, double-tap within 200 ms exits.
This is a provisional interpretation of your comment, not removal of the runtime checks.
If you meant a different item, say so beneath this diagram.
Ordinary cancellation here is simply releasing LH2.

Comments:
Make LM0 depower all weapons and LB1 depower all drones.

### Utilities

```text
┌──────────────┬──────────────┬──────────────┬──────────────┬──────────────┬──────────────┐
│  Shop / F9   │ Crew 1 / F1  │ Crew 2 / F2  │ Crew 3 / F3  │ Crew 4 / F4  │  Ship menu   │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│  Inventory   │ Crew 5 / F5  │ Crew 6 / F6  │ Crew 7 / F7  │ Crew 8 / F8  │   All crew   │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│  Crew / F10  │              │ Text smaller │ Text larger  │   Save 2x    │    Cancel    │
└──────────────┴──────────────┴──────────────┼──────────────┼──────────────┼──────────────┤
                                             │  Sys− / W−   │ Pause/Combat │ Sys+ / Utils │
                                             └──────────────┴──────────────┴──────────────┘
```

Hold LH0 to enter; release LH0 to return to combat.
Entry clears mode-owned depower Shift before any utility output.
All crew sends Q; Save 2x sends Slash once on the protected double-tap.
Text smaller sends Equals; Text larger sends Minus.
Shop F9 and Crew manifest F10 are the accepted custom exceptions; Inventory remains I and Ship menu U.
LB4 is vacant.
Release LH0 before pressing Pause.

Comments:
Make LM0 Shop and LB0 Shop. Make LT5 Cancel.

### Q26. Where would you put Crew home in these maps?

Please mark your preferred key or chord using the previews above.
Crew home sends Return and is different from both Save crew and teleporter Recall.

The direct central combat positions are occupied, and all three adjacent home-row pairs now have your chosen actions.
You can replace a displayed action or choose a new chord; name what should move if you replace something.

Recommendation for the smallest change: Utilities LB4 is free and keeps every existing assignment, but costs a thumb hold and a bottom-row press.
That is a trade-off against your preference for central frequent actions, not an assumption that it is comfortable enough.
I have left it blank so you can decide rather than hiding that trade-off.
The earlier Mind + Weapon 4 diagonal is also not assigned or treated as approved.

Answer:

### Q27. May another keyboard press finish a pending gesture as its tap?

Your proposed Pause behavior is feasible in the installed behavior's decision logic.
The earlier limitation was about keeping the gesture undecided while letting Pause bypass it.
You now propose ending that decision as a tap, which gives us another option.

With hold-preferred recognition and positional hold conditions, Pause can force the pending Autofire gesture to tap instead of hold.
That commits one V toggle and lets the Pause press continue without waiting for the rest of the 150 ms decision interval.
Continuing to hold Autofire afterward must not turn that same press into Ctrl or produce another toggle on release.
Use a one-shot tap action, not a V key held until the Autofire finger lifts.

The constraint is that other keyboard presses also resolve the gesture; they cannot keep the old tap-preferred buffering while Pause alone resolves.
We must choose what those other presses do.

Recommendation: **any other keyboard downstroke while Autofire is undecided commits its tap**.
This includes Pause, weapon keys, layer thumbs, and right-half keyboard keys.
If no keyboard key interrupts, short release toggles and reaching 150 ms selects held Ctrl as before.
Trackpad clicks do not count as keyboard interruptions.
After Ctrl has already activated, Pause does not retrospectively toggle Autofire; Ctrl remains held until the Autofire key is released.

For Recall/Send, I recommend the same early-tap rule: another keyboard downstroke, including Pause, recalls once rather than sending.
An uninterrupted hold still sends once at the threshold.
This extension to Recall/Send is a proposal, not implied approval from your Autofire request.

An alternative can make Pause force tap while other keyboard presses force hold, but that would make early non-Pause interruption of Recall/Send send crew.
I would avoid that surprise.

These are source-supported decision paths, not a runtime pass.
Zero-delay macros still use the behavior queue; neither exact simultaneity nor bypassing existing queued work is promised.
The final check must cover one toggle, one Pause, no later Ctrl from that forced tap, balanced releases, and both sides of the hold deadline.

May we use the recommended early-tap rule for:
- Autofire?
- Recall/Send?

Also correct the Weapons− cancellation interpretation above if needed.

Answer:
Both.

## Round 11

Crew home is now on combat LM0 only.
Q27 accepts early-tap interruption for both Autofire and Recall/Send.
The Cancel and bank-control requests are reflected below.
Two placements still need clarification in Q28–Q29; these previews are not full-map approval.

The thumb cluster is unchanged.
LH2 taps Systems− and holds Weapons−; LH1 pauses and cancels; LH0 taps Systems+ and holds Utilities.
Use the agreed thumb-release-before-finger sequence for system taps.
No simultaneous thumb holds are required.
The right half is unchanged and omitted; blank left keys do nothing.

### Combat

```text
┌──────────────┬──────────────┬──────────────┬──────────────┬──────────────┬──────────────┐
│ Settings+Can │   Autofire   │    Cloak     │     Mind     │     Hack     │              │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │   Weapon 1   │   Weapon 2   │   Weapon 3   │   Weapon 4   │  Crew home   │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │   Drone 1    │   Drone 2    │   Drone 3    │ Recall/Send  │    Cancel    │
└──────────────┴──────────────┴──────────────┼──────────────┼──────────────┼──────────────┤
                                             │  Sys− / W−   │ Pause/Combat │ Sys+ / Utils │
                                             └──────────────┴──────────────┴──────────────┘
```

LT5 clears extra FTL mode state and owned depower Shift, then sends Escape on the same press.
It does not leave FTL or send Space.
LB0 keeps cancel-only because you asked Settings to **also** cancel.
Crew home at LM0 sends Return on this layer only.

```text
Weapon 1 + Weapon 2  → Open doors
Weapon 2 + Weapon 3  → Close doors
Weapon 3 + Weapon 4  → Jump
```

For both merged finger gestures, an early keyboard downstroke now commits the tap once.
Autofire taps V; Recall taps R.
An uninterrupted hold at the proposed 150 ms threshold instead holds Ctrl or sends T once.
A forced tap never later turns into a hold or repeats on physical release.
The existing queue and runtime verification requirements remain.

Comments:
Can the Settings/cancel first cancel pending mode state and only send esc if there wasn't one pending? 
Or is it even needed since all non-held modes have cancel in the same position?
If so then just keep it as plain settings.

### Systems+

```text
┌──────────────┬──────────────┬──────────────┬──────────────┬──────────────┬──────────────┐
│    Cancel    │   Battery    │   Cloak +    │    Mind +    │    Hack +    │              │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │  Shields +   │  Engines +   │ Med/Clone +  │     O2 +     │  Weapons +   │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │              │              │ Artillery +  │  Teleport +  │   Exit 2x    │
└──────────────┴──────────────┴──────────────┼──────────────┼──────────────┼──────────────┤
                                             │  Sys− / W−   │ Pause/Combat │ Sys+ / Utils │
                                             └──────────────┴──────────────┴──────────────┘
```

Cancel is now a direct LT5 action with no double-tap recognition wait.
It clears mode state and returns to combat without Escape or Space.
LB0 is Exit 2x only: single tap does nothing, double-tap within 200 ms exits.
LM0 sends native W for the weapon system.
The exact per-press meaning of your word "all" is being checked; no repeated-key or full-bank macro is assumed.

Comments:
No need to check, I already did check it.

### Systems−

```text
┌──────────────┬──────────────┬──────────────┬──────────────┬──────────────┬──────────────┐
│    Cancel    │   Battery    │   Cloak −    │    Mind −    │    Hack −    │              │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │  Shields −   │  Engines −   │ Med/Clone −  │     O2 −     │  Weapons −?  │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │              │              │ Artillery −  │  Teleport −  │   Exit 2x    │
└──────────────┴──────────────┴──────────────┼──────────────┼──────────────┼──────────────┤
                                             │  Sys− / W−   │ Pause/Combat │ Sys+ / Utils │
                                             └──────────────┴──────────────┴──────────────┘
```

LT5 cancels and releases owned Shift without Escape or Space.
LB0 remains protected Exit 2x, with no single-tap Cancel.
The question mark at LM0 is Q29: sharing the system map would make the new W binding send Shift+W here.
Nothing has silently assigned that extra minus-view action.

Comments:
I think weapons- isn't needed on this layer.

### Weapons−

```text
┌──────────────┬──────────────┬──────────────┬──────────────┬──────────────┬──────────────┐
│              │              │              │              │              │              │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │  Weapon 1−   │  Weapon 2−   │  Weapon 3−   │  Weapon 4−   │  Weapons −   │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │   Drone 1−   │   Drone 2−   │   Drone 3−   │   Drones −   │   Exit 2x    │
└──────────────┴──────────────┴──────────────┼──────────────┼──────────────┼──────────────┤
                                             │  Sys− / W−   │ Pause/Combat │ Sys+ / Utils │
                                             └──────────────┴──────────────┴──────────────┘
```

Hold LH2 for this view; release it to return to combat and release owned Shift.
LM0 sends W under that held Shift for the weapon system.
LB1 sends E under held Shift for the drone system.
These are bank shortcuts, separate from the individual weapon/drone number keys.
The exact native per-press effect is under investigation rather than promising one press empties every slot.
LB0 remains Exit 2x only; LT5 remains empty on this held view.

Comments:
No need to check weapons and drones shortcuts, I already did that.

### Utilities

```text
┌──────────────┬──────────────┬──────────────┬──────────────┬──────────────┬──────────────┐
│    Cancel    │ Crew 1 / F1  │ Crew 2 / F2  │ Crew 3 / F3  │ Crew 4 / F4  │  Ship menu   │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│  Inventory   │ Crew 5 / F5  │ Crew 6 / F6  │ Crew 7 / F7  │ Crew 8 / F8  │  Shop / F9   │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│  Crew / F10  │  All crew?   │ Text smaller │ Text larger  │   Save 2x    │  Shop / F9   │
└──────────────┴──────────────┴──────────────┼──────────────┼──────────────┼──────────────┤
                                             │  Sys− / W−   │ Pause/Combat │ Sys+ / Utils │
                                             └──────────────┴──────────────┴──────────────┘
```

LT5 now cancels Utilities without Escape or Space.
The two Shop keys follow your wording literally: both LM0 and LB0 send F9.
Ship menu remains LT0 and sends U.
This displaces All crew from LM0.
The question-marked LB4 placement for All crew Q is a recommendation, pending Q28.
Crew home is not copied into Utilities.
The other menu, crew, text-size, and protected Save bindings remain unchanged.

Comments:
LM0 should be inventory.
All crew is fine in that location.
Crew menu does not need a separate key, I never use it.

### Q28. Two Shop keys, or Ship plus Shop, and where should All crew move?

Your annotation says "LM0 Shop and LB0 Shop".
The diagram therefore shows both as Shop, without guessing that one was a typo.

Did you mean two Store/F9 keys, or did you intend one of those to be Ship menu/U?
If Ship menu moves from LT0, should that old position become empty?

All crew Q also needs a new position because LM0 is now occupied.
Recommendation: put All crew on the free Utilities LB4, keeping Crew home on combat LM0 as requested.
Please confirm the two menu positions and the All-crew move.

Answer:

### Q29. Should Systems− also get the matching weapon-system shortcut on LM0?

Systems+ and Systems− share one physical map; the minus mode adds held Shift.
Your Systems+ LM0 W addition therefore naturally yields Shift+W at Systems− LM0 too.
You explicitly requested the minus bank shortcut in Weapons−, but did not say whether it also belongs in Systems−.

Recommendation: keep LM0 aligned across both system states, with W in Systems+ and Shift+W in Systems−.
That gives system power and depower the same location without changing Crew home on combat.
The minus preview marks this with a question mark until you choose.

Do you want that matching Systems− binding, or should LM0 remain blank there?

Answer:
It doesn't need to be in sys layer but if removing it from there would be harder than just leaving it there then it's fine to have it there.

## Round 12: final candidate

Your comments settle the remaining choices:
- Combat Settings stays plain Escape.
LT5 already means Cancel on the active system and utility views, so no conditional Settings macro is needed.
- Inventory moves to utility LM0; the old LM5 copy is cleared.
Shop stays LB0, All crew is LB4, and Ship menu stays LT0.
- Crew manifest loses its dedicated key; LB5 is empty and no F10 game assignment is required.
- Keep W at LM0 in the shared system map.
This also gives Shift+W in Systems−, using the simpler option you explicitly allowed instead of adding special suppression.
- You have verified the native weapon/drone bank shortcuts.
The extra lookup is stopped; your check is the evidence for the intended game behavior.
The firmware will send those native shortcuts, not a repeated-key macro.

This is the final candidate for approval, not an implementation or a claim that the runtime gate has passed.
All earlier proposals and your annotations remain above as history.
The right half stays unchanged and is omitted here.
Blank left keys do nothing.

### Combat

```text
┌──────────────┬──────────────┬──────────────┬──────────────┬──────────────┬──────────────┐
│   Settings   │   Autofire   │    Cloak     │     Mind     │     Hack     │              │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │   Weapon 1   │   Weapon 2   │   Weapon 3   │   Weapon 4   │  Crew home   │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │   Drone 1    │   Drone 2    │   Drone 3    │ Recall/Send  │              │
└──────────────┴──────────────┴──────────────┼──────────────┼──────────────┼──────────────┤
                                             │  Sys− / W−   │ Pause/Combat │ Sys+ / Utils │
                                             └──────────────┴──────────────┴──────────────┘
```

Settings sends Escape.
Combat LB0 is empty, applying your final approval comment below.
Crew home sends Return, on combat LM0 only.
Autofire toggles with V or holds Left Ctrl; Recall/Send uses one R or one T.
The direct weapon and drone outputs remain 1–4 and 5–7.

```text
Weapon 1 + Weapon 2  → Open doors, Z
Weapon 2 + Weapon 3  → Close doors, X
Weapon 3 + Weapon 4  → Jump, J
```

These combos use the 30 ms combat-only window with no prior-idle requirement.
A near miss can select a weapon instead; an adjacent weapon roll can trigger the combo.
No Cloak, Hack, or Battery activation key participates.

Comments:
Drop the unnecessary cancel from this layer.

### Systems+

```text
┌──────────────┬──────────────┬──────────────┬──────────────┬──────────────┬──────────────┐
│    Cancel    │   Battery    │   Cloak +    │    Mind +    │    Hack +    │              │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │  Shields +   │  Engines +   │ Med/Clone +  │     O2 +     │  Weapons +   │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │              │              │ Artillery +  │  Teleport +  │   Exit 2x    │
└──────────────┴──────────────┴──────────────┼──────────────┼──────────────┼──────────────┤
                                             │  Sys− / W−   │ Pause/Combat │ Sys+ / Utils │
                                             └──────────────┴──────────────┴──────────────┘
```

LT5 cancels without Escape or Space.
LM0 sends your verified native W shortcut.
Battery cancels the mode before B.
LB0 is protected Exit 2x only.

Comments:

### Systems−

```text
┌──────────────┬──────────────┬──────────────┬──────────────┬──────────────┬──────────────┐
│    Cancel    │   Battery    │   Cloak −    │    Mind −    │    Hack −    │              │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │  Shields −   │  Engines −   │ Med/Clone −  │     O2 −     │  Weapons −   │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │              │              │ Artillery −  │  Teleport −  │   Exit 2x    │
└──────────────┴──────────────┴──────────────┼──────────────┼──────────────┼──────────────┤
                                             │  Sys− / W−   │ Pause/Combat │ Sys+ / Utils │
                                             └──────────────┴──────────────┴──────────────┘
```

This is the same physical system map, with owned Shift held across adjustments.
LM0 therefore sends Shift+W.
Keeping it avoids adding a special case just to make this one position blank.
LT5 cancels and releases owned Shift; LB0 remains Exit 2x only.

Comments:

### Weapons−

```text
┌──────────────┬──────────────┬──────────────┬──────────────┬──────────────┬──────────────┐
│              │              │              │              │              │              │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │  Weapon 1−   │  Weapon 2−   │  Weapon 3−   │  Weapon 4−   │  Weapons −   │
├──────────────┼──────────────┼──────────────┼──────────────┼──────────────┼──────────────┤
│              │   Drone 1−   │   Drone 2−   │   Drone 3−   │   Drones −   │   Exit 2x    │
└──────────────┴──────────────┴──────────────┼──────────────┼──────────────┼──────────────┤
                                             │  Sys− / W−   │ Pause/Combat │ Sys+ / Utils │
                                             └──────────────┴──────────────┴──────────────┘
```

Hold LH2; release it to cancel.
Individual slots send numbers under held Shift.
LM0 sends Shift+W and LB1 sends Shift+E, using the native shortcuts you verified.
No automatic repeat sequence is added.
LT5 is empty; LB0 remains Exit 2x only.

Comments:

### Utilities

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

Hold LH0; release it to return to combat.
LT5 also cancels, without Escape or Space.
Crew selection is F1–F8.
Ship menu is U, Inventory I, All crew Q, and Shop F9.
Save 2x sends Slash once; a single tap does nothing.
Text smaller sends Equals; Text larger sends Minus.
Crew manifest has no dedicated key.

F9 for Store is the only custom game assignment required by this layout.
Normal typing, OS/output modes, connections, and the right half remain unchanged.

Comments:

### Timing and transitions

Initial timing values:
- Thumb tap/hold: 200 ms, hold-preferred.
Release the thumb before a finger for a system tap; pressing a finger while it remains down chooses the hold action.
- Autofire and Recall/Send: 150 ms, with the accepted early-tap interruption rule below.
- System-mode idle interval: 600 ms, refreshed by left-half keyboard activity.
Accepted limitation after Q30: releasing one finger can allow expiry while another remains held.
- Save and Exit double-taps: 200 ms from the first downstroke, with no intervening key.
- Combat combos: 30 ms.

No simultaneous thumb presses are required.
Release a held layer thumb before Pause or the other selector.

| Trigger | Result |
| --- | --- |
| LH2 short tap, released before a finger key | Replace the old extra mode with Systems−; hold owned Shift; request a 600 ms interval, subject to the accepted stale-deadline limit |
| LH2 held for 200 ms, or a finger pressed while LH2 remains down | Replace the old extra mode with held Weapons−; hold owned Shift |
| Release LH2 after its Weapons− hold | Return to combat and release that mode's Shift; do not restore an older mode |
| LH0 short tap, released before a finger key | Replace the old extra mode with Systems+; remove old mode-owned Shift; request a 600 ms interval, subject to the accepted stale-deadline limit |
| LH0 held for 200 ms, or a finger pressed while LH0 remains down | Replace the old extra mode with held Utilities; remove old mode-owned Shift |
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

#### Merged finger gestures

For both Autofire and Recall/Send:
- A short release or another keyboard downstroke while undecided commits the tap once.
That is V for Autofire or R for Recall.
- An uninterrupted hold reaching the 150 ms decision selects the hold action.
Autofire holds Ctrl until physical release; Send emits T once without keeping it down.
- A tap decided early cannot later turn into hold, repeat, or emit a second tap on release.
- Pause during an undecided gesture commits that tap, then proceeds with Pause without waiting out the remaining gesture interval.
- Once the hold action has already activated, another key does not retrospectively turn it into a tap.
Already-held Ctrl remains held until the Autofire key is released.
- Trackpad clicks do not resolve a keyboard gesture.
Wait for Ctrl to activate before targeting with it.

This is not a promise that macros bypass queued work or that all host events are atomic.
The actual scheduler/queue checks remain mandatory.
Releases must match their original presses even when the visible layer changes.

### Q30. Do you approve this complete layout and transition contract?

Recommendation: approve this candidate as the specification for a later `/implement` request.
The native game shortcuts you checked are accepted; the remaining tests concern firmware behavior, timers, modifiers, gestures, and queue ordering.

Approval does **not** authorize implementation, builds, flashing, or game-setting changes.
After approval, a separate `/implement` request begins with the actual installed-helper gate.
If that gate fails or requires custom C, a module change, or an upgrade, work stops rather than silently weakening the contract.
Planning and any later approved implementation stay in the same owning jj change.

Approve, or annotate any remaining correction beneath its diagram or here.

Answer:
Approved with the one comment on combat layer.

## Implementation gate result

The user subsequently requested "Done. Go ahead and /implement".
Approval removes only the dedicated combat LB0 Cancel; LH1 remains Pause-and-cancel.
The Round 12 preview and transition row above reflect that correction.
Earlier rounds remain historical proposals.

The installed helper's native timeout test passed.
A new test using the actual ZMK/Zephyr runtime failed: releasing one ignored finger lets the timeout end tri-state and release Shift while another ignored finger remains held.
This violates the approved held-key rule.
See the [runtime gate result](ftl-comfort-layout.md#runtime-gate-result) for the fixture, evidence, and remaining checks.

Implementation stopped as agreed.
No firmware layout, module source, or game setting changed.
Neither implementation review ran; this is a blocked gate, not a completed implementation.
At that point, resuming needed an approved helper repair/replacement or a revised mode contract, followed by the full gate.

## Round 13: stale timer deadlines

You renewed implementation authorization with: "the plan. The drawback described is not ideal but acceptable."
That accepts the previously explained overlapping-held-finger expiry.
The current timing summary above now includes that exception.
Your layout approval and combat LB0 removal remain unchanged.

The next actual ZMK runtime test found two different failures, both without overlapping keys:
- Re-tapping the mode selector can leave the original deadline in force instead of starting a fresh 600 ms interval.
- Cancelling and re-entering can reuse the old timer, ending the newly entered mode early.

The fixture uses explicit Shift press/release macros, not the previous toggle marker.
In both cases, a probe key pressed less than 600 ms after the new selector release arrives after Shift has already been released.
The [plan's resumed gate record](ftl-comfort-layout.md#resumed-gate-stale-deadlines) contains the evidence and reproduction path.

### Q31. Keep fresh selector/re-entry deadlines, or accept these shortened lifetimes too?

Recommendation: keep the fresh-deadline requirement and authorize a helper fix before implementing the layout.
Otherwise, ordinary reselection can unexpectedly drop depowering before the next adjustment.
A helper fix would expand the current configuration-only scope and needs your explicit approval; no such patch has been made.

Alternatively, you can explicitly accept these two additional timer limitations.
That would allow the remaining checks to continue, not automatically waive modifier ownership, cleanup, or other untested behavior.

Which route do you want?

Answer:
Leave a todo about making a proper fix and just go ahead with this limited implementation now.

## Implementation record

Q31 accepts the two stale-deadline cases as well as the earlier held-finger expiry limitation.
The current transition table reflects those exceptions; no further layout approval is inferred or needed.
The implementation leaves a source TODO for the proper timer/held-key fix and documents the limits in `ftl.md`.
The bounded native layout probe matches 90 explicitly expected HID events, including mode cleanup and late thumb releases.
See the [implementation record in the plan](ftl-comfort-layout.md#q31-implementation) for scope and local evidence.
Final builds and both reviews are mandatory completion gates, reported separately; no game settings are changed and nothing is flashed.

## Round 14: busy-queue transitions

The limited layout is implemented and both halves build.
Q31's timer TODO remains; no helper source or dependency changed.
The required reviews found three different failures, and a new native runtime test reproduced them with a 300 ms behavior-queue backlog:

- Tap-release Systems+, then press a finger before queued activation: the finger can send a combat action instead of the selected system action.
- Press a right-hand key during depowering: it can reach the host before owned Shift is released, so ordinary typing is shifted.
- Interrupt a pending held-mode activation with a right-hand key: the mode and its Shift can still activate afterward.

These are not merely delayed host events or the accepted timer faults.
The earlier 90-event layout, 30-event queue, and 30-event combo tests pass, but they did not cover these interleavings.
See the [review gate record](ftl-comfort-layout.md#review-gate-busy-queue-transitions) for the failed fixture and reviewed snapshot.
Both review passes completed with findings; neither is clean.
The prototype remains local in the same owning change, and nothing was flashed.

### Q32. Authorize a behavior/helper fix, or explicitly exclude busy-queue operation?

Recommendation: authorize a tracked behavior/helper fix, including C if needed, within this same owning change.
No configuration-only correction has been established.
Keep the approved layout and ordinary typing requirements, then rerun the failing cases, builds, and both reviews.
This is not permission to upgrade dependencies, flash, publish, or create another prerequisite change.

Alternatively, explicitly accept busy-queue transitions/right-hand typing as unsupported until a proper fix.
That accepts the observed wrong-map actions, Shift leaking into right-hand input, and late mode activation while queued work is pending.
It is a broader limitation than Q31, not an enforced safeguard or a claim that the firmware can detect a safe time to type.
The limitation would need prominent documentation and a TODO before finishing that version.

You can also leave the prototype blocked and retain the currently flashed baseline.
Which route do you want?

Answer:
Right hand stuff isn't a problem, not using the right half while playing.
The described queued activation failure is completely theoretical while playing ftl.

## Q32 resolution

The user confirms that right-half typing is not part of FTL play and rejects the injected backlog as a practical gameplay blocker.
The follow-up source audit found zero programmed delay in every FTL macro and tri-state end action.
Holding a layer thumb and waiting for tap/hold or double-tap recognition do not block the macro queue.
The three stress-test failures remain reproducible with an unrelated delayed macro, but no FTL-only trigger has been demonstrated.
This narrows the supported play scope; it does not authorize helper/C changes or assert arbitrary-backlog safety.

Proceed with the approved left-hand controls and trackpad, retaining the accepted timer limitations and TODO.
Remove the duplicate V/R/T one-shot macros by reusing `ftl_key`, then rerun affected checks and both reviews.
The final report records their results; no new layout decision or question is needed.

## Post-play follow-up: accidental held-layer selection

The user reports wrong first actions when selecting Systems±, while repeated adjustments work after a successful first press.
After an overlap reproduction, they confirm: "Yup, that seems to be exactly what's happening. I'm getting plenty of hold layer actions when trying to activate a tap layer actions."

The fix changes only the two thumb recognizers to balanced, still at 200 ms.
A roll with thumb release before finger release selects the system tap; holding through 200 ms or through a completed finger tap still selects the held view.
The first finger action may wait for this decision.
This supersedes the earlier hold-preferred timing descriptions, not the maps, accepted timer limitations, or the separate Autofire/Recall-Send rules.
See the [fix record](ftl-comfort-layout.md#post-play-thumb-roll-fix) and current `ftl.md` for the updated rule.
