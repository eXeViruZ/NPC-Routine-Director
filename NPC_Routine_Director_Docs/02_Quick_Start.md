# Quick Start

In about 10 minutes you create one NPC with a working routine: at 08:00 game time it walks to its workplace and works there until 17:00. Everything on this page works in a Blueprint-only project; no C++ is needed.

## Before You Start

- NPC Routine Director is installed and enabled ([01 — Installation](01_Installation.md)).
- Your level has a NavMesh (NavMeshBoundsVolume + RecastNavMesh) that covers the place where the NPC will work and the way there.
- You have an NPC: a Character with an AI controller and **Auto Possess AI** set to **Placed in World or Spawned**. An `ACharacter` with `CharacterMovement` and an `AIController` is the recommended setup.

## Steps

1. **Create the activity.** In the Content Browser, choose **Add** (or right-click) **→ Artificial Intelligence → NPC Routine → NPC Activity** and name it, for example, `Act_Work`. Optionally set **Display Name** to "Work": the Schedule Studio, the NPC Inspector and the gameplay status show this name. Leave **Executor Template** empty for "stand and work", or choose a built-in executor ([04 — Activities](04_Activities_Calendars_Profiles.md#activities)).
2. **Create the schedule.** In the same menu, create an **NPC Schedule**, for example `Sch_Villager`. It opens in the **Schedule Studio** ([03 — Schedule Studio](03_Schedule_Studio.md)).
3. **Create the profile.** In the same menu, create an **NPC Routine Profile**, for example `Prof_Villager`. Set **Schedule** to your schedule and **Spawn Class** to your NPC class. An **NPC Calendar** for day types (market day, festival) is optional and not needed here.
4. **Place a Routine Spot.** Select the actor where the NPC should work, choose **Add Component → Routine Spot** and set **Capacity**. The spot's stable id is assigned automatically; you never type it.
5. **Create the routine entry.** In the Schedule Studio, drag on the timeline from 08:00 to 17:00, or click **Add Entry**.
6. **Set activity and location.** In **Details**, set **Activity** = `Act_Work`, **Location → Kind** = **Spot**, and pick your spot in **Spot Id**. The picker lists the Routine Spots of the open level and of every built World Manifest by name. With the spot's actor selected in the level you can also click **use selected**. Save the schedule.
7. **Add the routine to the Character.** Add the **NPC Routine** component to your NPC and set **Profile** to `Prof_Villager`.
8. **Build the World Manifest.** Save the level, then choose **NPC Routine Director** toolbar menu **→ Build World Manifest** (or **Tools → NPC Routine Director → Build World Manifest**). The dialog shows the exact target (the map's existing manifest, else `M_<YourMap>` next to the map) and afterwards the real result (spots, NPCs, issues).
9. **Play.** Press Play.
10. **Inspect the NPC.** Open toolbar menu **→ NPC Inspector** (also in the **Tools** and **Window** menus) and select the NPC to see what it does now, where it goes, what comes next and why.

## Expected Result

At 08:00 game time the NPC walks to the Routine Spot and performs `Act_Work` there. The NPC Inspector shows its current activity, destination, next activity and the reason for the current decision. The Inspector's **+1 h** / **+1 d** buttons skip game time if you want to see the next entry sooner ([11 — NPC Inspector](11_Editor_Tools_And_NPC_Inspector.md#npc-inspector)).

Rebuild the World Manifest after adding, moving or deleting spots or NPCs.

## If Nothing Happens

| Check | Where |
|---|---|
| Was the World Manifest built for this map? | [06 — World Manifest](06_World_Manifest_And_World_Partition.md) |
| Does the profile have a schedule, and does an AI controller possess the pawn? | Profile asset, NPC class |
| Does the NPC slide without a walking animation? | Set **CharacterMovement → Use Acceleration For Paths** = true ([05 — Animation](05_NPC_Components_And_Routine_Spots.md#animation)) |
| Console `hinrd.Health` | [11 — Console commands](11_Editor_Tools_And_NPC_Inspector.md#console-commands) |

All symptoms are listed in [09 — Troubleshooting](09_Troubleshooting_And_Limitations.md#troubleshooting).

## Next Steps

- **Save/load and time skip:** store **Export State** in your own SaveGame and call **Import State** after loading ([08 — Save and Load](08_Save_Load_And_Runtime_API.md#save-and-load)). Skip time with **Advance Time** / **Set Game Time** ([07 — Time](07_Time_Simulation_And_External_Clocks.md)) or the Inspector's **+1 h / +1 d**.
- **Before packaging:** run toolbar menu **→ Check Packaging Readiness** once ([06 — Check Packaging Readiness](06_World_Manifest_And_World_Partition.md#check-packaging-readiness)).
- **Learn the daily workflow:** [03 — Schedule Studio and Schedule Entries](03_Schedule_Studio.md).
- **See a complete example:** the shipped Routine Village demo ([12 — Demo Walkthrough](12_Demo_Walkthrough.md)).

---

← [01 — Installation](01_Installation.md) · [Documentation Index](INDEX.md) · [03 — Schedule Studio and Schedule Entries](03_Schedule_Studio.md) →
