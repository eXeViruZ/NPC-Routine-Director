# World Manifest and World Partition

Every map with NPC Routine content needs one **World Manifest**. It contains the map's Routine Spots, placed NPCs, spawner NPCs and data layers. This page explains how to build it, how it gets into a packaged game and how NPCs behave in World Partition.

## Build the World Manifest

1. Save the level. The builder refuses unsaved levels.
2. Stop Play In Editor if it runs. The builder refuses Play In Editor sessions, and the menu entry is disabled while Play In Editor runs.
3. Choose **NPC Routine Director** toolbar menu **→ Build World Manifest**, or **Tools → NPC Routine Director → Build World Manifest**.
4. Read the dialog. It shows the exact target – the map's existing manifest, else `M_<YourMap>` next to the map – and afterwards the real result (spots, NPCs, issues).

Rebuild the manifest after adding, moving or deleting spots or NPCs.

For advanced use, the console command `HINRD.BuildManifest <path>` builds a manifest as well.

### Builder Rules

- One manifest per map.
- The builder never overwrites another map's manifest or an asset of another type.
- Two manifests for the same map are reported; delete one.

## Packaged Games: Automatic Cooking

A manifest is not referenced by its map, so the plugin adds it to the cook itself: every placed **NPC Routine** component, **Routine Spot** and **NPC Routine Spawner** reports its map's manifest to the cooker as a cook dependency.

- A map's manifest is cooked exactly when the map (or, with World Partition, one of its streaming cells) is cooked.
- No `DefaultGame.ini` edit and no Asset Manager entry are needed, and your project's cook settings are not changed.
- Manifests of maps you do not package are not cooked. The shipped demo's manifest is only cooked when you package the demo map.
- An existing Asset Manager rule for `HINRDWorldManifest` keeps working but is not needed.
- If your project strips asset registry tags from cooked builds, the runtime finds manifests by loading them (correct, slightly slower world start).

### Check Packaging Readiness

Before packaging, run toolbar menu **→ Check Packaging Readiness** once. It is the same as the console command `HINRD.CheckPackaging`, runs in the editor only and is read-only.

| It reports | Examples |
|---|---|
| Every manifest | With its map |
| Errors | Manifest without a map key; several manifests for one map |
| Warnings | Map renamed or deleted; manifest build issues; unsaved manifests |

All lines are written to the Output Log (`LogHINRD`).

### Missing Manifest in Game

If a map has NPC Routine content but no manifest is found, the first NPC Routine object logs one warning (`no World Manifest was found`, diagnostic `ManifestMissing`) instead of failing silently. To fix it, build and save the map's manifest, run **Check Packaging Readiness** and package again.

### Keep the Demo out of Your Game

To keep the shipped demo out of your packaged game, add `/HINPCRoutineDirector/Demo` to **Project Settings → Packaging → Directories to never cook** ([12 — Demo Walkthrough](12_Demo_Walkthrough.md#using-the-demo-content-in-your-project)).

## World Partition

- NPCs in unloaded cells continue virtually.
- When a cell loads, its NPCs are placed at their current record position, not where they were last seen.
- `HomeBound` NPCs must keep their schedule inside their home cell's footprint.
- NPCs that travel between distant areas must be `DirectorManaged` ([05 — Mobility](05_NPC_Components_And_Routine_Spots.md#mobility-homebound-or-directormanaged)).
- An NPC that resumes at a Routine Spot when its cell streams in uses its own reserved slot; if the slot cannot be used, the NPC is placed later (`PlacementDeferred`) ([05 — NPCs That Resume at a Spot](05_NPC_Components_And_Routine_Spots.md#npcs-that-resume-at-a-spot)).

### Navigation in World Partition

Walking NPCs need navigation data where they are embodied. Recommended layout (as in the demo): a static navmesh that is always loaded over the walkable area, with houses, props, spots and NPCs in streamed cells.

World-partitioned (dynamic) navmesh and Data Layer driven NPC setups are not covered by our tests ([Known Limitations](09_Troubleshooting_And_Limitations.md#known-limitations)).

### Level Instances

NPCs inside a Level Instance register when it streams in; their identity comes from the instance.

### Map Travel

Map travel never resets the simulation, the clock or the records ([07 — Time, Simulation and External Clocks](07_Time_Simulation_And_External_Clocks.md)).

---

← [05 — NPC Components and Routine Spots](05_NPC_Components_And_Routine_Spots.md) · [Documentation Index](INDEX.md) · [07 — Time, Simulation and External Clocks](07_Time_Simulation_And_External_Clocks.md) →
