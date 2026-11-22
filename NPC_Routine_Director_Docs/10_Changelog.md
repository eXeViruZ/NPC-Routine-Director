# Changelog

This page lists the public version history of NPC Routine Director. The newest version is at the top.

## NPC Routine Director v1.0.0

### Release Summary

Initial release. NPC Routine Director gives NPCs weekly routines that keep running while they are off-screen, catch up exactly after time skips and continue after a save is loaded, with World Partition support, a visual Schedule Studio and an NPC Inspector. For single-player games in Unreal Engine 5.8.

### Added

- Schedule Studio for NPC Schedule assets: timeline editing, searchable entries list, details, validation and a seven-day preview; every edit is one undo step
- NPC Activity, NPC Schedule, NPC Routine Profile and NPC Calendar assets (Content Browser > Artificial Intelligence > NPC Routine) with weekday masks, priorities, entries that pass midnight, fallback activity and location, day-type layers and World Fact conditions
- NPC Routine component for any AI-controlled pawn, with HomeBound and DirectorManaged mobility and the brain modes RoutineDriven, BrainHosted and ObserveOnly
- Routine Spots with capacity, automatic slots and stable ids, plus a spot picker for the open level and every built World Manifest
- NPC Routine Spawner for spawned NPCs with stable identities
- Built-in activity executors: Wait, Move To, Play Montage, Patrol, Wander, Interact, Run Behavior Tree and Sequence; custom executors in Blueprint or C++
- Virtual simulation of off-screen NPCs: each record is exact at its last evaluated time and is brought exactly to the clock when needed, with a configurable per-frame catch-up budget
- Time control: Advance Time and Set Game Time (forward only), time helper nodes, the built-in clock or your own clock as an external time source, and Apply External Time Jump for intentional jumps
- World Facts: Gameplay Tag keyed values, global or per map, with recorded history and scheduled future changes
- Save and load: Export State and Import State with a versioned save envelope for your own SaveGame; optional A/B save-slot helper in C++
- World Manifest per map, built from the toolbar, cooked automatically with its map; Check Packaging Readiness before packaging
- World Partition support: NPCs in unloaded cells continue virtually and are placed at their current record position when their cell loads; NPCs inside Level Instances are supported
- Interruptions with resume policies, a control lease with exactly one movement owner, Behavior Tree task, service and decorator, Blueprint functions for StateTree tasks or custom brains, and interfaces for custom brains, Smart Object reservations and time sources
- NPC Inspector for Play In Editor, plus a Gameplay Debugger category, in-world labels and console commands in development builds; Get NPC Routine Status and Explain NPC Routine nodes for your own UI
- Level Editor toolbar entry with Schedule Studio, NPC Inspector, Build World Manifest and Check Packaging Readiness
- Routine Village demo: a World Partition map with ten villagers, a farm district, time-skip keys, save and load and a NOW / NEXT / WHY panel, built only with the public API (demo art by Kenney, CC0)

### Compatibility

- Unreal Engine 5.8 (tested with Unreal Engine 5.8.4)
- C++ code plugin with a runtime module and an editor module; no other plugins required
- Supported platforms: Win64, Linux, Mac
- Single-player (standalone) games

### Documentation

- Full documentation, a demo walkthrough and third-party notices are included in the plugin's Docs folder

---

← [09 — Troubleshooting and Known Limitations](09_Troubleshooting_And_Limitations.md) · [Documentation Index](INDEX.md) · [11 — Editor Tools and NPC Inspector](11_Editor_Tools_And_NPC_Inspector.md) →
