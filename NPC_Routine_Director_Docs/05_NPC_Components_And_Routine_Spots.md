# NPC Components and Routine Spots

This page explains how an NPC gets its routine (the **NPC Routine** component and the **NPC Routine Spawner**), how places are defined (**Routine Spots** with capacity and slots) and what NPCs need for navigation.

## NPC Routine Component

Any `APawn` works. The recommended setup is an `ACharacter` with `CharacterMovement` and an `AIController`.

Add the **NPC Routine** component (`UHINRDRoutineComponent`) to your NPC:

| Property | Meaning |
|---|---|
| **Profile** | The **NPC Routine Profile** with schedule, spawn class and brain mode ([04 — NPC Routine Profile](04_Activities_Calendars_Profiles.md#npc-routine-profile)) |
| **Mobility** | `HomeBound` or `DirectorManaged` (below) |
| **Authored Guid** | Read-only identity of this placed NPC, assigned automatically when the NPC is placed and renewed on duplicate or paste. In the Blueprint editor it shows zeros because a class default has no identity; a GUID stored on the class default or inherited from it is reported as `HINRD.ID.012` / `HINRD.ID.013` |
| **Start Spot Id** | Optional Routine Spot (chosen with the Routine Spot picker) used only when the NPC is registered for the first time outside a World Manifest, for example inside a Level Instance or before the manifest is rebuilt. NPCs listed in the map's World Manifest are registered at world start and begin where they are placed. Leave it empty to start where the actor is placed |

The profile's **Brain Mode** decides whether the routine moves the pawn (`RoutineDriven`), your Behavior Tree or StateTree hosts the routine (`BrainHosted`), or the NPC is only simulated (`ObserveOnly`) ([04 — Brain Modes](04_Activities_Calendars_Profiles.md#brain-modes)).

## Mobility: HomeBound or DirectorManaged

| Mobility | Behavior | Use it for |
|---|---|---|
| `HomeBound` | The placed actor is the embodiment | NPCs that keep their schedule inside their home cell's footprint |
| `DirectorManaged` | The placed actor is only an authoring proxy; the director spawns the embodiment wherever the NPC is | NPCs that travel across World Partition cells or between distant areas (required) |

More about World Partition: [06 — World Manifest and World Partition](06_World_Manifest_And_World_Partition.md#world-partition).

## Animation

The routine moves the pawn through path following. Animation Blueprints that read acceleration need **CharacterMovement → Use Acceleration For Paths** = true (in the **Nav Movement** section), for example the UE 5.8 template `ABP_Unarmed`. Without it, NPCs slide without a walking animation.

## NPC Routine Spawner

For NPCs that are spawned instead of placed, use the **NPC Routine Spawner** actor (`AHINRDSpawner`). It gives each spawned NPC a stable identity.

| Property | Meaning |
|---|---|
| **Profile** | The profile of the spawned NPCs |
| **Count** | Number of NPCs |
| **Spawn Class** | The pawn class to spawn; empty uses the profile's spawn class. Spawned NPCs are always `DirectorManaged` |
| **Radius** | Spawn radius around the spawner |

## Routine Spots

A **Routine Spot** is a place where NPCs perform activities. Add the **Routine Spot** component to an actor (**Add Component → Routine Spot**).

| Property | Meaning |
|---|---|
| **Spot Tags** | Tags of the spot |
| **Capacity** | How many embodied NPCs can use the spot at once; more than 1 creates slots. Virtual NPCs are not limited by capacity |
| **Slot Spacing Override** | Ring spacing of this spot's slots; 0 = the project setting **Spot Slot Spacing** |

The spot's stable id is assigned automatically; you never type it.

- Spot ids must be unique. Duplicated actors get new ids.
- A Spot Id on a Blueprint class default is reported as an error.
- Schedules reference spots, not coordinates.

### Referencing a Spot: the Routine Spot Picker

Wherever a spot is referenced – schedule entry **Location**, profile slots, NPC Routine **Start Spot Id**, **Move To** **Spot Id**, **Patrol** **Patrol Spots** – use the **Routine Spot picker**.

- **What it lists:** the spots of the open level (actor names) and of every built World Manifest (other maps, unloaded World Partition cells). Equal names are shown with map and short id (`#a1b2c3`).
- **use selected:** takes the Routine Spot of the actor selected in the level.
- **browse:** selects and frames the spot in the open level.
- **Unknown spot #...:** a reference that is in neither source shows this warning with the fix; the stored id is never changed silently.

Only the stable id is stored, so renaming the spot's actor does not break references. Deleting the spot does (the warning shows it).

### Capacity and Slots

Capacity > 1 creates slots in rings of six around the spot.

- Ring spacing = **Spot Slot Spacing** (Project Settings, default **180 cm**) or the spot's **Slot Spacing Override**.
- 180 cm suits capsule radii up to about 42 cm.
- NPCs have no local avoidance, so wide characters can block each other at multi-slot spots. For them, use smaller capacities, a larger spacing or separate spots, and check the level as described below.

### Check Spot Slots in Play In Editor

Run this console command in Play In Editor:

```text
hinrd.Spots.Validate [agent radius cm, default 42]
```

In Blueprint, call **Validate Routine Spot Slots** on **Get HINRDDirectorSubsystem** (the NPC Routine world subsystem). The check reports:

| Diagnostic | Meaning |
|---|---|
| `SpotSlotOffNavigation` | A slot is off the navmesh |
| `SpotSlotUnreachable` | A slot has no complete path from the spot |
| `SpotSlotSpacingTight` | The spacing is below two capsule diameters |

### NPCs That Resume at a Spot

An NPC that resumes at a spot (after a load or when its cell streams in) uses its own reserved slot. If that slot cannot be used (off the navmesh, or another pawn stands on it), the NPC is not placed yet and the diagnostic `PlacementDeferred` is reported. Placement is retried; nothing is moved to a random place.

## Navigation

- A NavMesh (NavMeshBoundsVolume + RecastNavMesh) must cover every spot and route. Static or dynamic generation can be used.
- **Path failures:** the NPC retries (`Navigation Attempts`), then uses the schedule's fallback location, then stands **Blocked**. It is not teleported.
- **Kinematic fallback:** an optional straight-line kinematic fallback is available (`Allow Kinematic Fallback`, off by default).
- **Temporary obstructions:** if a route failed only because something blocked the way (other pawns, a cart), the NPC re-plans after `Obstruction Replan Delay Sec` (default 3 s, doubling up to `Obstruction Replan Max Delay Sec`, 30 s), at most `Max Obstruction Replans` times (default 6) per schedule entry occurrence. Then it stays Blocked with the diagnostic `ObstructionReplanExhausted`. Goals without any path never re-plan. Setting `Max Obstruction Replans` to 0 disables obstruction re-planning.
- In World Partition, walking NPCs need navigation data where they are embodied ([06 — Navigation in World Partition](06_World_Manifest_And_World_Partition.md#navigation-in-world-partition)).

All navigation settings with their defaults are listed in [13 — Project Settings Reference](13_Project_Settings_Reference.md#navigation-and-movement).

If an NPC stands **Blocked** or spot slots are never used, see [09 — Troubleshooting](09_Troubleshooting_And_Limitations.md#troubleshooting).

---

← [04 — Activities, Calendars and Profiles](04_Activities_Calendars_Profiles.md) · [Documentation Index](INDEX.md) · [06 — World Manifest and World Partition](06_World_Manifest_And_World_Partition.md) →
