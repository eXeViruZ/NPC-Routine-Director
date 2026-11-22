# Installation

This page explains what NPC Routine Director needs, how to install it from Fab, how to enable it in a project, how to see its content in the Content Browser and how to check that everything is in place. It also covers updating and removing the plugin.

## Requirements

| Requirement | Details |
|---|---|
| Unreal Engine | 5.8 |
| Supported platforms | Win64, Linux, Mac |
| Other plugins | None. The modules `HINRDCore` (runtime) and `HINRDEditor` (editor tools) depend on engine modules only. |
| Game type | Single-player games (standalone). Multiplayer replication is not part of 1.0.0 ([Known Limitations](09_Troubleshooting_And_Limitations.md#known-limitations)). |
| Navigation | A NavMesh where NPCs walk ([Navigation](05_NPC_Components_And_Routine_Spots.md#navigation)). |
| NPCs | Characters or pawns possessed by an AI controller. |

## What Fab Delivers

Fab delivers the plugin compiled for the Unreal Engine version you select; the delivered plugin contains a `Binaries` folder. The source archive that is uploaded to Fab does not contain `Binaries`: Fab builds them for each engine version.

## Install from Fab (Recommended)

1. Open the Epic Games Launcher.
2. In your Fab library, install NPC Routine Director for Unreal Engine 5.8 into your engine installation.

The plugin is installed as an **Engine plugin** below `<UE 5.8>/Engine/Plugins/Marketplace`. It is available to every project of that engine; you enable it per project.

## Enable the Plugin in Your Project

1. Open your project.
2. Choose **Edit → Plugins**.
3. Search for **NPC Routine Director**. It is listed under **Installed**.
4. Tick its checkbox.
5. Restart the editor when asked.

## Check the Installation

After the restart, these entries confirm that the plugin is active:

| Where | What you see |
|---|---|
| Level Editor toolbar | One **NPC Routine Director** button (a figure with a route to a target marker) and its arrow menu next to the Play controls |
| Main menu | **Tools → NPC Routine Director** with **NPC Inspector** and **Build World Manifest**; **Window → NPC Routine Director** with **NPC Inspector** |
| Content Browser | **Add → Artificial Intelligence → NPC Routine** with **NPC Activity**, **NPC Schedule**, **NPC Routine Profile** and **NPC Calendar** |
| Project Settings | **Project Settings → Plugins → NPC Routine Director** ([13 — Project Settings Reference](13_Project_Settings_Reference.md)) |

The toolbar entry and the menus are described in [11 — Editor Tools and NPC Inspector](11_Editor_Tools_And_NPC_Inspector.md).

## Show the Plugin Content in the Content Browser

The plugin's content (the demo) is shown when **Engine Content** and **Plugin Content** are enabled in the Content Browser settings (gear icon).

- For an Engine plugin, the content is under **Engine → Plugins → NPC Routine Director**.
- Asset paths start with `/HINPCRoutineDirector/`.
- The demo map is under **Engine → Plugins → NPC Routine Director → Demo → Maps** ([12 — Demo Walkthrough](12_Demo_Walkthrough.md)).

## Blueprint-Only and C++ Projects

- **Blueprint-only projects** use the compiled binaries that Fab delivers.
- **C++ projects** can also rebuild the plugin from its `Source` folder together with the project.
- A plugin folder without `Binaries` (for example a raw source archive) must be compiled before a Blueprint-only project can use it.

## Alternative: Install as a Project Plugin

1. Close the editor.
2. Copy the complete, compiled `HINPCRoutineDirector` folder into `<YourProject>/Plugins/`.
3. Open the project and enable the plugin as described in [Enable the Plugin in Your Project](#enable-the-plugin-in-your-project).

## Update the Plugin

- **Fab installation:** install the newer version for Unreal Engine 5.8 from your Fab library in the Epic Games Launcher, the same way as in [Install from Fab](#install-from-fab-recommended).
- **Project plugin:** close the editor and replace the `HINPCRoutineDirector` folder in `<YourProject>/Plugins/` with the complete, compiled folder of the new version.
- **Saved games:** saves store the plugin's state in a versioned save envelope. An incompatible envelope is refused with a report and changes nothing ([Save and Load](08_Save_Load_And_Runtime_API.md#save-and-load)).
- Read the [Changelog](10_Changelog.md) for what changed in a version.

## Remove the Plugin

- **From a project:** choose **Edit → Plugins**, untick **NPC Routine Director** and restart the editor.
- **Project plugin:** additionally close the editor and delete the `HINPCRoutineDirector` folder from `<YourProject>/Plugins/`.
- **Keep only the demo out of your game:** you do not need to remove the plugin for that. Add `/HINPCRoutineDirector/Demo` to **Project Settings → Packaging → Directories to never cook** ([12 — Demo Walkthrough](12_Demo_Walkthrough.md#using-the-demo-content-in-your-project)).

## Typical Installation Problems

| Problem | Solution |
|---|---|
| The plugin's content is not visible in the Content Browser | Enable **Engine Content** and **Plugin Content** in the Content Browser settings (gear icon). |
| A Blueprint-only project cannot use the plugin folder | The folder has no `Binaries` (for example a raw source archive). Install the plugin from Fab, or compile it first. |
| The NPC Routine Director toolbar entry is missing | Enable the plugin in **Edit → Plugins** and restart the editor. |

More symptoms and checks are in [09 — Troubleshooting](09_Troubleshooting_And_Limitations.md#troubleshooting).

---

← [Documentation Index](INDEX.md) · [02 — Quick Start](02_Quick_Start.md) →
