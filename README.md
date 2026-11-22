# NPC Routine Director

NPC Routine Director gives every NPC in your Unreal Engine 5.8 game a persistent daily and weekly routine that keeps running while the NPC is far away, in an unloaded cell, or across save and load. A virtual simulation decides where each NPC is and what it does; when the player comes near, the NPC is embodied as a pawn that walks there and performs the activity. Distance-based virtualization uses World Partition streaming sources; in maps without World Partition every NPC whose position is ready is embodied. Time can be skipped by hours, days or weeks; when an NPC is evaluated, it is exactly where its schedule says, never at a guessed place.

Version: 1.0.0 · Unreal Engine: 5.8 · Plugin Type: Editor + Runtime (C++ code plugin) · Required Engine Plugins: none · Supported Platforms: Win64, Linux, Mac · Network: single-player (standalone)

## Key Features

- **Schedule Studio** – edit NPC Schedule assets on a timeline, with a searchable entries list, details, validation and a seven-day preview. Every edit is one undo step.
- **Routines for any AI-controlled pawn** – the **NPC Routine** component, **Routine Spots** with capacity, automatic slots and stable ids, and the **NPC Routine Spawner** for spawned NPCs.
- **Exact off-screen simulation** – every NPC record is exact at its own last evaluated time and is brought exactly to the clock when it is needed, with a per-frame catch-up budget.
- **Time control** – **Advance Time** and **Set Game Time** (forward only), time helper nodes, the built-in clock or your own clock as an external time source, and **Apply External Time Jump** for intentional jumps.
- **Save and load** – **Export State** and **Import State** with a versioned save envelope that you store in your own `USaveGame`.
- **World Partition and packaging** – NPCs in unloaded cells continue virtually; one World Manifest per map, cooked automatically with its map; **Check Packaging Readiness** before you package.
- **Gameplay integration** – World Facts, **Get NPC Routine Status** and **Explain NPC Routine** for your own UI, interruptions, a control lease, Behavior Tree nodes, and Blueprint functions you can call from your own StateTree tasks or custom brains (no StateTree nodes are included).
- **Diagnostics and demo** – the **NPC Inspector** for Play In Editor, in-world labels, console commands and a playable World Partition demo village.

## Requirements

- Unreal Engine 5.8.
- No other plugin: the modules `HINRDCore` (runtime) and `HINRDEditor` (editor) depend on engine modules only.
- Single-player (standalone) games.
- A NavMesh where NPCs walk.
- Characters or pawns possessed by an AI controller.
- Blueprint-only projects use the compiled binaries that Fab delivers; C++ projects can also build the plugin from its `Source` folder.

## Quick Start

1. Install the plugin from your Fab library for Unreal Engine 5.8, enable it in **Edit → Plugins** and restart the editor.
2. In the Content Browser, create an **NPC Activity**, an **NPC Schedule** and an **NPC Routine Profile** (**Add → Artificial Intelligence → NPC Routine**).
3. Add a **Routine Spot** component to an actor and add a schedule entry for that spot in the **Schedule Studio**.
4. Add the **NPC Routine** component to an AI-controlled Character and set its **Profile**.
5. Save the level, run **Build World Manifest** from the **NPC Routine Director** toolbar menu and press **Play**.

The full walkthrough with the expected result is in [02 — Quick Start](NPC_Routine_Director_Docs/02_Quick_Start.md).

## Documentation

| Page | What you find there |
|---|---|
| [Documentation Index](NPC_Routine_Director_Docs/INDEX.md) | All pages and an "I need to..." table |
| [01 — Installation](NPC_Routine_Director_Docs/01_Installation.md) | Requirements, installing from Fab, enabling, updating, removing |
| [02 — Quick Start](NPC_Routine_Director_Docs/02_Quick_Start.md) | Your first NPC with a working routine in about 10 minutes |
| [09 — Troubleshooting](NPC_Routine_Director_Docs/09_Troubleshooting_And_Limitations.md#troubleshooting) | Symptoms and what to check |
| [09 — Known Limitations](NPC_Routine_Director_Docs/09_Troubleshooting_And_Limitations.md#known-limitations) | Product boundaries of version 1.0.0 |
| [10 — Changelog](NPC_Routine_Director_Docs/10_Changelog.md) | Version history |

## Good to Know

- **Single-player only:** multiplayer replication of NPC state is not part of 1.0.0.
- **Smart Objects and StateTree** are connected through interfaces and Blueprint nodes; there is no native Smart Object or StateTree adapter in 1.0.0.
- **Navigation:** walking NPCs need a NavMesh covering every spot and route. NPCs have no local avoidance between each other.
- **Packaging:** World Manifests are cooked with their maps without any setup; the shipped demo can be kept out of your packaged game with **Directories to never cook**.
- **Development tools:** the in-world labels, the Gameplay Debugger category and the console commands are not available in Shipping builds.

## Support

- **Discord (primary channel for support, bug reports and feature requests):** <https://discord.gg/vgpmnN6nCR>
- **Email:** [Tom.Hanke.Official@web.de](mailto:Tom.Hanke.Official@web.de)
- **Hanke Interactive on Fab:** <https://www.fab.com/sellers/Hanke%20Interactive>

When you report a problem, include the Unreal Engine version, the plugin version (1.0.0), the Output Log lines of `LogHINRD` and, if possible, the NPC Inspector's diagnostics for the affected NPC.

## License and Copyright

This repository contains documentation only. NPC Routine Director itself is licensed through Fab under the Fab Standard License. The demo art in the plugin is by Kenney (www.kenney.nl) and released under CC0; see the [credits in the Demo Walkthrough](NPC_Routine_Director_Docs/12_Demo_Walkthrough.md#credits-and-third-party-notices).

Copyright (c) 2026 Hanke Unreal Tools. All Rights Reserved.
