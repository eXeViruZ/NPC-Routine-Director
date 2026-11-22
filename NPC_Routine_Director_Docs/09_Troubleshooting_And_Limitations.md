# Troubleshooting and Known Limitations

This page lists typical symptoms with what to check, the product limitations of NPC Routine Director 1.0.0, and the information to include when you ask for support.

## Troubleshooting

Most checks use the **NPC Inspector** and the console commands described in [11 — Editor Tools and NPC Inspector](11_Editor_Tools_And_NPC_Inspector.md).

| Symptom | Check |
|---|---|
| NPC never moves | Is the World Manifest built for this map ([06](06_World_Manifest_And_World_Partition.md#build-the-world-manifest))? Run `hinrd.Health`. Does the profile have a schedule? Does an AI controller possess the pawn? |
| NPC slides without walking animation | Set `Use Acceleration For Paths` (**CharacterMovement → Nav Movement**) ([05 — Animation](05_NPC_Components_And_Routine_Spots.md#animation)) |
| NPC stands "Blocked" | Inspector diagnostics: `NavigationFailed` (no path: fix the navmesh or the spot) or `ObstructionReplanExhausted` (blocked route) ([05 — Navigation](05_NPC_Components_And_Routine_Spots.md#navigation)) |
| Some spot slots are never used | Run `hinrd.Spots.Validate <radius>`; raise **Slot Spacing Override** or lower **Capacity** ([05 — Capacity and Slots](05_NPC_Components_And_Routine_Spots.md#capacity-and-slots)) |
| NPC is not shown at its spot, `PlacementDeferred` | Its slot is blocked or off the navmesh; the NPC is placed when the slot can be used |
| NPC appears at another place after its cell loads | Expected: it is placed at its current record position ([06 — World Partition](06_World_Manifest_And_World_Partition.md#world-partition)) |
| Record "Unresolved" | See the reason in the Inspector. Exact catch-up could not prove the state (approximation is off by default) |
| Status shows an earlier activity / "(catching up)" | Expected for off-screen NPCs ([07](07_Time_Simulation_And_External_Clocks.md#how-current-is-an-npc-lazy-exact-simulation)). Call **Advance Time (0, bBlocking = true)** if you need the state at the clock |
| NPCs do nothing in a packaged game | The log shows `no World Manifest was found`: build and save the map's manifest, run **Check Packaging Readiness** and package again ([06](06_World_Manifest_And_World_Partition.md#check-packaging-readiness)) |
| NPC clock stands still | **Get Time Source Status**: `SourceLost` (your clock object was destroyed with its level: set it again) or `BehindClock` (your clock is earlier than the NPC clock) ([07](07_Time_Simulation_And_External_Clocks.md#your-own-daynight-system-external-time-source)) |
| Whole-state save/load refused in the demo | The demo map was opened from a game that already has its own NPCs; start the demo as its own Play session ([12](12_Demo_Walkthrough.md#using-the-demo-content-in-your-project)) |
| **Apply External Time Jump** returns a result other than `Applied` | Nothing changed; the result names the reason ([07 — result table](07_Time_Simulation_And_External_Clocks.md#intentional-jumps-of-your-own-clock-apply-external-time-jump)) |
| The plugin's content is not visible in the Content Browser | Enable **Engine Content** and **Plugin Content** in the Content Browser settings ([01](01_Installation.md#show-the-plugin-content-in-the-content-browser)) |

### Diagnostics Named in This Documentation

| Diagnostic | Meaning | Page |
|---|---|---|
| `NavigationFailed` | No path to the goal | [05](05_NPC_Components_And_Routine_Spots.md#navigation) |
| `ObstructionReplanExhausted` | The obstruction re-plans of a schedule entry occurrence are used up; the NPC stays Blocked | [05](05_NPC_Components_And_Routine_Spots.md#navigation) |
| `PlacementDeferred` | The NPC's slot cannot be used yet; placement is retried | [05](05_NPC_Components_And_Routine_Spots.md#npcs-that-resume-at-a-spot) |
| `MaterializationDeferred` | The record could not be brought exactly to the clock (Unresolved); the NPC is not placed | [07](07_Time_Simulation_And_External_Clocks.md#terms) |
| `ManifestMissing` | A map has NPC Routine content but no World Manifest was found | [06](06_World_Manifest_And_World_Partition.md#missing-manifest-in-game) |
| `SpotSlotOffNavigation`, `SpotSlotUnreachable`, `SpotSlotSpacingTight` | Results of the spot slot check | [05](05_NPC_Components_And_Routine_Spots.md#check-spot-slots-in-play-in-editor) |
| `ExternalTimeJumpApplied`, `ExternalTimeJumpRefused` | Result of **Apply External Time Jump** | [07](07_Time_Simulation_And_External_Clocks.md#intentional-jumps-of-your-own-clock-apply-external-time-jump) |

## Known Limitations

These are deliberate product boundaries of version 1.0.0.

- **Single-player only:** multiplayer replication of NPC state is not part of 1.0.0.
- **No native Smart Object or StateTree adapters:** interfaces and Blueprint nodes only ([08 — AI Integration](08_Save_Load_And_Runtime_API.md#ai-integration)).
- **No local avoidance between routine NPCs:** wide characters can block each other at multi-slot spots ([05 — Capacity and Slots](05_NPC_Components_And_Routine_Spots.md#capacity-and-slots)).
- **World Partition navigation:** world-partitioned (dynamic) navmesh and Data Layer driven NPC setups are not covered by our tests; the recommended layout is described in [06 — Navigation in World Partition](06_World_Manifest_And_World_Partition.md#navigation-in-world-partition).
- **Off-screen NPCs** are exact at their last evaluated time, not necessarily at the clock; right after **Import State** they are as current as when they were saved ([07](07_Time_Simulation_And_External_Clocks.md#how-current-is-an-npc-lazy-exact-simulation)). The Gameplay Debugger category shows the record state without a "catching up" marker (the NPC Inspector and `hinrd.Labels` show it).
- **External time source:** it is not saved; a source owned by a level must be set again after map travel.
- **Jumps of an external clock:** a forward jump of an external time source is recognised only through **Apply External Time Jump** in the same frame; without the call, NPCs that have an actor are not re-placed. There is no automatic jump detection.
- **Demo time keys:** the demo's time keys use **Advance Time**. If you open the demo from your own game while your external clock drives the NPC clock, those keys move the NPC clock ahead of your clock, which then waits (`BehindClock`). Open the demo in its own Play session.

## Support Information for Bug Reports

Report problems on Discord or by email:

- **Discord (primary channel for support, bug reports and feature requests):** <https://discord.gg/vgpmnN6nCR>
- **Email:** [Tom.Hanke.Official@web.de](mailto:Tom.Hanke.Official@web.de)

Please include:

1. The Unreal Engine version.
2. The plugin version (1.0.0).
3. The Output Log lines of `LogHINRD`.
4. If possible, the NPC Inspector's diagnostics for the affected NPC ([11 — NPC Inspector](11_Editor_Tools_And_NPC_Inspector.md#npc-inspector)).

---

← [08 — Save/Load and Runtime API](08_Save_Load_And_Runtime_API.md) · [Documentation Index](INDEX.md) · [10 — Changelog](10_Changelog.md) →
