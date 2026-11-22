# Demo Walkthrough

NPC Routine Director ships with the **Routine Village** demo in its **Demo** folder (`/HINPCRoutineDirector/Demo`). With the plugin installed as an Engine plugin, the folder is under **Engine → Plugins → NPC Routine Director → Demo** in the Content Browser.

Everything in this folder is **example content**. The demo HUD, the keys and the villager Blueprints are samples that show how a game can use the plugin; the plugin itself provides no game UI.

## Open and Play

1. Enable the plugin (**Edit → Plugins**) and restart the editor ([01 — Installation](01_Installation.md)).
2. Enable **Engine Content** and **Plugin Content** in the Content Browser settings.
3. Open `Demo/Maps/L_NRD_RoutineVillage` (**Engine → Plugins → NPC Routine Director → Demo → Maps**). The full asset path is `/HINPCRoutineDirector/Demo/Maps/L_NRD_RoutineVillage`.
4. Press **Play** (Selected Viewport or Standalone).

The demo starts on Monday 00:00 and immediately catches every villager up to **Monday 07:00** (exact, nothing invented).

## Keys

| Key | Action |
|---|---|
| 1 / 2 / 3 / 4 | Skip 1 h / 3 h / 1 day / 5 days |
| T | Next camera: village overview, market and plaza, tavern street, farm district, workshop |
| Tab | Select the next villager that currently has an actor ("embodied") |
| F | Move the camera to the selected villager |
| I | Interrupt the selected villager for one game hour (it resumes afterwards) |
| F5 / F9 | Save / load (the plugin's save envelope inside a normal `USaveGame`) |
| L | In-world debug labels off / full / compact (development builds only) |
| P | Pause / resume the game clock |
| H | Hide the key list |
| WASD + mouse | Fly (spectator pawn) |

## What You Are Looking At

Ten villagers live in the village. Smith, Apprentice, Baker, Merchant, Innkeeper, Guard, Elder and Child live in the village core; the Farmer and the Farm hand commute to the farm district **600 m east**, which is a separate World Partition area.

Every villager is an ordinary `Character` with the **NPC Routine** component. The plugin owns the villager's logical state (where, what, until when) as a record; the actor only shows it. A villager far away from the camera has no actor at all and is still simulated exactly.

## The Ten Examples

| # | Topic | Where to look |
|---|---|---|
| 1 | Create a weekly routine | `Demo/Schedules/Sch_NRD_*`: entries with start time, duration, weekday mask (weekdays, weekend, Monday-Saturday, daily), activity and location (a Routine Spot). Open one in the Schedule Studio. |
| 2 | Assign a schedule / profile | `Demo/Profiles/Prof_NRD_*`: a profile points to a schedule, a spawn class and a virtual travel speed. The villager's NPC Routine component references its profile. |
| 3 | Set up a Routine Spot | The `Spot_*` actors in the map: a Routine Spot component with a stable id and a capacity (tavern 6, market 4, workshop 3, bench 2). Schedules reference spots, not coordinates. |
| 4 | Connect an existing Character | `Demo/Blueprints/BP_NRD_DemoCharacter`: a plain Character Blueprint with no routine logic; the NPC Routine component on each placed villager drives it through an AI controller and the navigation mesh. Two child Blueprints (`_Farmer`, `_FarmHand`) are the spawn classes of the director-managed commuters. |
| 5 | Execute a custom activity | `Demo/Blueprints/BP_NRD_DemoActivity_PlayLoop`: a Blueprint subclass of the plugin's Activity Executor. On Start / On Resume it shows a looping animation and a label on the villager, On Pause / On Cancel clears them; it reports its progress as Time Derived. Used by `Act_NRD_Work`, `Act_NRD_Trade`, `Act_NRD_FieldWork`, `Act_NRD_Sit`, `Act_NRD_Lunch`, `Act_NRD_Tavern`, `Act_NRD_Sleep`. `Act_NRD_Patrol` and `Act_NRD_Stroll` use the built-in Patrol and Wander executors. |
| 6 | Interruption / replanning | `BP_NRD_DemoPlayerController`, event `InterruptSelected`: `Interrupt NPC` with reason, priority 10, policy Resume and a duration of 3600 game seconds. Afterwards the villager continues with whatever its schedule says at that time. |
| 7 | Advance simulation time | Events `StartDemoClock` (blocking `Advance Time` from midnight to 07:00) and `SkipHours` (non-blocking `Advance Time`; the catch-up is spread over frames). |
| 8 | World Partition unload / reload | Press T until "Farm district": the village cells unload (its villagers lose their actors but keep being simulated), the district cells load and the commuters appear where their records are. Press T again to return. |
| 9 | Save and load NPC state | Events `SaveDemo` / `LoadDemo`: `Export State` writes one opaque, versioned envelope into `BP_NRD_DemoSaveGame`; `Import State` restores every record (an incompatible envelope is refused with a report and changes nothing). |
| 10 | Why does an NPC do this? | Press L for the plugin's in-world labels (current and next activity, reason; development builds), open the **NPC Inspector** in the editor (toolbar menu, Tools or Window menu), or use the Gameplay Debugger category **HINRD**. The HUD's **NOW / NEXT / WHY** panel (select a villager with Tab) shows the current entry, the next predicted activity with its start time and which rule is in effect, from the public Blueprint API (Get NPC Routine Status); a villager that streams out stays listed as virtual. |

Related pages: [03 — Schedule Studio](03_Schedule_Studio.md), [04 — Activities](04_Activities_Calendars_Profiles.md#activities), [05 — Routine Spots](05_NPC_Components_And_Routine_Spots.md#routine-spots), [07 — Time](07_Time_Simulation_And_External_Clocks.md), [08 — Save and Load](08_Save_Load_And_Runtime_API.md#save-and-load), [11 — NPC Inspector](11_Editor_Tools_And_NPC_Inspector.md#npc-inspector).

## Honest Limits of This Demo

- **Animation coverage:** walking, idle, working (interact loop), trading (interact loop), field work (pick-up loop) and sitting (sit loop). **Sleeping has no animation**: a sleeping villager stands idle at its home door; "Sleep" is a logical state. There are no interiors, doors, props in hands or seats; sitting villagers sit on the ground at their spot.
- **Shared spots:** villagers at a shared spot each use their own slot of that spot – also when the village cells stream back in, after loading a save and after a time skip. If no slot can be used (all slots taken, a slot off the navigation mesh, or another character standing on it), the villager is not shown until the slot can be used; the plugin reports this as a `PlacementDeferred` diagnostic.
- **Lighting** is a fixed daytime setup; the game clock does not move the sun.
- The demo **HUD**, keys and Blueprints are examples. Its WHY line is built from the public routine status (active entry, fallback, interruption, unresolved reason, waiting/blocked notes); the resolver's full candidate-by-candidate explanation is in **Explain NPC Routine**, the labels, the Inspector and the Gameplay Debugger. The panel refreshes at most 4 times per second; NEXT is a prediction (current World Facts, no interruption, within 24 game hours).
- Labels, Inspector and Gameplay Debugger are not available in Shipping builds.
- No native Smart Objects or State Tree integration is used or claimed.

## Using the Demo Content in Your Project

- **Dependencies:** the demo depends only on the engine and on the plugin's public API. You can copy any Blueprint as a starting point.
- **Packaging:** if you do not want the demo in your packaged game, add `/HINPCRoutineDirector/Demo` to **Project Settings → Packaging → Directories to never cook**.
- **Only active in its own map:** enabling the plugin does not add the demo's villagers, profiles or Routine Spots to your maps, and your own World Manifests are discovered as before.
- **When the demo map ends** (your game travels to another map, or play stops), the demo's villagers, Routine Spots and profiles are removed again and the demo's clock speed is undone; your own NPC records keep surviving map travel as always. Time that passed while the demo was open is not undone.
- **Saving and loading from a running game:** if the demo map is opened from a running game that already has its own NPCs, the demo's F5/F9 save and load are refused (the demo saves and loads everything, so it would otherwise store or replace your NPCs). Start the demo as its own session to try saving and loading.
- **Save messages:** a refused F5 writes nothing: an earlier demo save stays exactly as it was, and the message says "Save refused". The demo reports "Saved" only after the save slot was actually written ("Save failed" otherwise).
- **The save order to copy:** the demo's F5 graph calls **Export State** first, and stores and writes only a successful envelope ([08 — Save and Load](08_Save_Load_And_Runtime_API.md#save-and-load)).

## Credits and Third-Party Notices

Demo art: Kenney (www.kenney.nl), CC0 – Fantasy Town Kit 2.0, Mini Characters 2.0, Nature Kit 2.1.

The plugin code (`Source/`), the editor icons (`Resources/`) and the documentation are original work of Hanke Interactive. They are licensed to you under the terms of the marketplace you obtained the plugin from (Fab).

The shipped demo (`Content/Demo`, mount point `/HINPCRoutineDirector/Demo`) contains art derived from the following packs by Kenney (www.kenney.nl). All of them are released under **Creative Commons Zero (CC0 1.0 Universal)**, a public-domain dedication: you may use, modify and redistribute them, also commercially, without attribution. CC0 1.0 legal code: <https://creativecommons.org/publicdomain/zero/1.0/>

| Pack | Source | Licence | Used in the demo |
|---|---|---|---|
| Fantasy Town Kit 2.0 | <https://kenney.nl/assets/fantasy-town-kit> | CC0 1.0 | 54 static meshes (`Demo/Environment/Town/Meshes`) and the town colour-map texture |
| Mini Characters 2.0 | <https://kenney.nl/assets/mini-characters> | CC0 1.0 | 8 skeletal meshes and their skeleton (`Demo/Characters`), 9 animations (`Demo/Animations`), the character colour-map texture |
| Nature Kit (licence file 2.1) | <https://kenney.nl/assets/nature-kit> | CC0 1.0 | 21 static meshes (`Demo/Environment/Nature/Meshes`), recoloured by the demo materials |

Pack names and versions follow the Kenney asset pages and the licence files of the archives that were used.

Credit (optional under CC0, given gladly): Demo art by Kenney (www.kenney.nl), CC0.

Everything else in the demo (Blueprints, materials and material instances, activities, schedules, profiles, the map layout and the World Manifest) is original Hanke Interactive content. The demo references a few engine assets (basic shapes, default textures, the volumetric cloud material) without copying them, and engine plugins that are enabled by default in Unreal Engine 5.8. No other third-party content and no Fab marketplace content is included.

---

← [11 — Editor Tools and NPC Inspector](11_Editor_Tools_And_NPC_Inspector.md) · [Documentation Index](INDEX.md) · [13 — Project Settings Reference](13_Project_Settings_Reference.md) →
