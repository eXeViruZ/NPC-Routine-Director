# NPC Routine Director — Documentation Index

**Product:** NPC Routine Director · **Version:** 1.0.0 · **Unreal Engine:** 5.8 · **Plugin Type:** Editor + Runtime (C++ code plugin)

This documentation takes you from installing NPC Routine Director to your first NPC with a working routine, and then serves as the reference for schedules, Routine Spots, time and catch-up, save/load, World Partition, the editor tools and the runtime API. Pages 01 to 10 follow the standard documentation structure; pages 11 to 13 cover the editor tools, the shipped demo and the project settings in more detail.

Back to the [README](../README.md).

## Documents

| No. | Page | Content |
|---|---|---|
| 01 | [Installation](01_Installation.md) | Requirements, installing from Fab, enabling the plugin, Content Browser visibility, checking the installation, updating and removing |
| 02 | [Quick Start](02_Quick_Start.md) | Create the assets, place a Routine Spot, author a routine, add it to a Character, build the World Manifest and play |
| 03 | [Schedule Studio and Schedule Entries](03_Schedule_Studio.md) | The Schedule Studio window, tabs, mouse and keyboard controls, schedule entries, fallback, validation codes |
| 04 | [Activities, Calendars and Profiles](04_Activities_Calendars_Profiles.md) | NPC Activity and executors, custom executors, schedule layers and NPC Calendar, NPC Routine Profile and brain modes |
| 05 | [NPC Components and Routine Spots](05_NPC_Components_And_Routine_Spots.md) | NPC Routine component and mobility, NPC Routine Spawner, Routine Spots, the spot picker, slots, navigation |
| 06 | [World Manifest and World Partition](06_World_Manifest_And_World_Partition.md) | Building the World Manifest, automatic cooking, Check Packaging Readiness, unloaded cells, Level Instances |
| 07 | [Time, Simulation and External Clocks](07_Time_Simulation_And_External_Clocks.md) | Game clock and time nodes, your own clock as a time source, Apply External Time Jump, World Facts, how current an NPC is |
| 08 | [Save/Load and Runtime API](08_Save_Load_And_Runtime_API.md) | Export State / Import State, status and explain nodes, interruptions, control lease, Behavior Tree, StateTree and custom brains, C++ access |
| 09 | [Troubleshooting and Known Limitations](09_Troubleshooting_And_Limitations.md) | Symptoms and checks, product limitations of 1.0.0, what to include in a bug report |
| 10 | [Changelog](10_Changelog.md) | Customer changelog of version 1.0.0 |
| 11 | [Editor Tools and NPC Inspector](11_Editor_Tools_And_NPC_Inspector.md) | Toolbar entry and menus, NPC Inspector, Gameplay Debugger, in-world labels, console commands |
| 12 | [Demo Walkthrough](12_Demo_Walkthrough.md) | The Routine Village demo: keys, the ten examples, demo limits, using the demo content, credits |
| 13 | [Project Settings Reference](13_Project_Settings_Reference.md) | Every setting under **Project Settings → Plugins → NPC Routine Director** that this documentation names, with defaults |

## I need to...

| I need to... | Go to |
|---|---|
| Install and enable the plugin | [01 — Installation](01_Installation.md) |
| See the plugin's demo content in the Content Browser | [01 — Installation](01_Installation.md#show-the-plugin-content-in-the-content-browser) |
| Get my first NPC walking to its workplace | [02 — Quick Start](02_Quick_Start.md) |
| Edit a schedule on the timeline | [03 — Schedule Studio and Schedule Entries](03_Schedule_Studio.md) |
| Understand a validation code (`HINRD.SCH.0xx`, `HINRD.CAL.0xx`, `HINRD.PRF.0xx`) | [03 — Validation](03_Schedule_Studio.md#validation) |
| Choose or write an activity executor | [04 — Activities](04_Activities_Calendars_Profiles.md#activities) |
| Use day types such as a market day or a festival | [04 — Layers and NPC Calendar](04_Activities_Calendars_Profiles.md#schedule-layers-and-the-npc-calendar) |
| Decide between `RoutineDriven`, `BrainHosted` and `ObserveOnly` | [04 — Brain modes](04_Activities_Calendars_Profiles.md#brain-modes) |
| Set up NPCs that travel across World Partition cells | [05 — Mobility](05_NPC_Components_And_Routine_Spots.md#mobility-homebound-or-directormanaged) |
| Configure Routine Spots, capacity and slots | [05 — Routine Spots](05_NPC_Components_And_Routine_Spots.md#routine-spots) |
| Fix NPCs that cannot reach a place | [05 — Navigation](05_NPC_Components_And_Routine_Spots.md#navigation) |
| Build the World Manifest and prepare packaging | [06 — World Manifest and World Partition](06_World_Manifest_And_World_Partition.md) |
| Skip time or connect my own day/night system | [07 — Time, Simulation and External Clocks](07_Time_Simulation_And_External_Clocks.md) |
| Let schedules react to weather or events (World Facts) | [07 — World Facts](07_Time_Simulation_And_External_Clocks.md#world-facts) |
| Save and load NPC state | [08 — Save and load](08_Save_Load_And_Runtime_API.md#save-and-load) |
| Show what an NPC is doing in my own UI | [08 — Status and explain nodes](08_Save_Load_And_Runtime_API.md#status-and-explain-nodes) |
| Interrupt an NPC or take control of its pawn | [08 — Interruptions and control lease](08_Save_Load_And_Runtime_API.md#interruptions) |
| Connect a Behavior Tree, StateTree or custom brain | [08 — AI integration](08_Save_Load_And_Runtime_API.md#ai-integration) |
| Fix a problem | [09 — Troubleshooting](09_Troubleshooting_And_Limitations.md#troubleshooting) |
| Understand product boundaries | [09 — Known Limitations](09_Troubleshooting_And_Limitations.md#known-limitations) |
| Review version changes | [10 — Changelog](10_Changelog.md) |
| Inspect NPCs while playing | [11 — Editor Tools and NPC Inspector](11_Editor_Tools_And_NPC_Inspector.md) |
| Try the demo village | [12 — Demo Walkthrough](12_Demo_Walkthrough.md) |
| Look up a project setting and its default | [13 — Project Settings Reference](13_Project_Settings_Reference.md) |

## Product and Compatibility Overview

| Item | Value |
|---|---|
| Product | NPC Routine Director by Hanke Interactive |
| Version | 1.0.0 |
| Unreal Engine | 5.8 (tested with Unreal Engine 5.8.4) |
| Plugin type | Editor + Runtime C++ code plugin: `HINRDCore` (runtime) and `HINRDEditor` (editor) |
| Required engine plugins | None; the modules depend on engine modules only |
| Supported platforms | Win64, Linux, Mac |
| Network | Single-player (standalone) |
| Plugin folder | `HINPCRoutineDirector` (asset paths start with `/HINPCRoutineDirector/`) |
| Blueprint and C++ | Blueprint nodes and C++ API; Blueprint-only projects use the compiled binaries that Fab delivers |
| Navigation | A NavMesh where NPCs walk |
| Included demo | Routine Village, a World Partition map with ten villagers |

## Support

- **Discord (primary channel for support, bug reports and feature requests):** <https://discord.gg/vgpmnN6nCR>
- **Email:** [Tom.Hanke.Official@web.de](mailto:Tom.Hanke.Official@web.de)
- **Hanke Interactive on Fab:** <https://www.fab.com/sellers/Hanke%20Interactive>

What to include in a report is listed in [09 — Support information for bug reports](09_Troubleshooting_And_Limitations.md#support-information-for-bug-reports).

---

[README](../README.md) · Next: [01 — Installation](01_Installation.md) →
