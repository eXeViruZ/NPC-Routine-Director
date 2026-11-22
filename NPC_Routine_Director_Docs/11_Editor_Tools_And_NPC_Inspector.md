# Editor Tools and NPC Inspector

This page describes where NPC Routine Director appears in the editor – the Level Editor toolbar entry, the main menu entries and asset creation – and the diagnostic tools: the **NPC Inspector**, the Gameplay Debugger category, in-world labels and console commands.

## Level Editor Toolbar

The Level Editor toolbar has one **NPC Routine Director** button (a figure with a route to a target marker) and its arrow menu next to the Play controls.

### Main Button

Opens the Schedule Studio. If a Schedule Studio is already open, the most recently used one is brought to the front; no second window is opened. If none is open, the menu below opens so you can pick a schedule.

### Arrow Menu

| Entry | Action |
|---|---|
| **Schedule Studio** | Lists your NPC Schedule assets (up to 20, by name); "(open)" marks schedules that are already open. With no schedule in the project, the menu says so and how to create one |
| **NPC Inspector** | Opens or focuses the Inspector tab |
| **Build World Manifest** | Builds the World Manifest of the open level ([06](06_World_Manifest_And_World_Partition.md#build-the-world-manifest)); disabled while Play In Editor runs |
| **Check Packaging Readiness** | The read-only packaging check ([06](06_World_Manifest_And_World_Partition.md#check-packaging-readiness)) |

## Main Menu Entries

The main menu entries run the same actions as the toolbar menu:

| Menu | Entries |
|---|---|
| **Tools → NPC Routine Director** | **NPC Inspector**, **Build World Manifest** |
| **Window → NPC Routine Director** | **NPC Inspector** |
| **Tools → Debug** | **NPC Inspector** |

**Check Packaging Readiness** is in the toolbar menu (console `HINRD.CheckPackaging`).

## Opening the Schedule Studio and Creating Assets

- The Schedule Studio opens from the toolbar or by double-clicking an **NPC Schedule** ([03 — Schedule Studio](03_Schedule_Studio.md)).
- Asset creation is in the Content Browser: **Add → Artificial Intelligence → NPC Routine**.

## NPC Inspector

Open the **NPC Inspector** from the toolbar menu, **Tools → NPC Routine Director**, **Window → NPC Routine Director** or **Tools → Debug**. It works while Play In Editor runs.

| Area | Content |
|---|---|
| World line | Game time, NPC count, problems |
| NPC list | Columns NPC / State / Activity / Status; filters **Embodied** / **Virtual** / **Problems only**; search by name, `#tag` or activity; Esc clears the search |
| Summary | Readable summary of the selected NPC: profile, state, now, destination, position with certainty, next |
| Developer detail | The resolver's **Why**, the next 24 h, trace, execution log, diagnostics |
| Figures | Health and catch-up figures |
| Time buttons | **+1 h** / **+1 d** |

- NPCs are shown as `<Profile> #a1b2c3` (stable short tag of the id).
- Positions that are not exact are labelled as such; unresolved records show no current location.
- Records that are behind the clock are shown with "(catching up)" ([07](07_Time_Simulation_And_External_Clocks.md#how-to-see-it)).
- With an external time source, skip time on your own clock; the Inspector's time buttons refuse ([07](07_Time_Simulation_And_External_Clocks.md#recommendations)).

## Development Tools

The following tools are available in development builds, not in Shipping builds.

### Gameplay Debugger

Category **HINRD** (it starts disabled; turn it on in the Gameplay Debugger). It shows the record state without a "catching up" marker ([Known Limitations](09_Troubleshooting_And_Limitations.md#known-limitations)).

### In-World Labels

| Console variable | Effect |
|---|---|
| `hinrd.Labels 1` | Full labels |
| `hinrd.Labels 2` | Compact labels |
| `hinrd.Labels.MaxDistance` | Label distance limit (default 3000 cm) |
| `hinrd.Labels.Max` | Maximum number of labels (default 16) |
| `hinrd.Labels.Stack` | Label stacking |

### Console Commands

| Command | Purpose |
|---|---|
| `hinrd.Dump` | Dump of the NPC records |
| `hinrd.Inspect <#tag or id prefix>` | Inspector detail for one NPC |
| `hinrd.Health` | Health check |
| `hinrd.Spots.Validate [agent radius cm, default 42]` | Spot slot check in Play In Editor ([05](05_NPC_Components_And_Routine_Spots.md#check-spot-slots-in-play-in-editor)) |
| `hinrd.Time.Skip <seconds>` | Skips game time ([07](07_Time_Simulation_And_External_Clocks.md#game-clock-and-time-nodes)) |

Editor console commands:

| Command | Purpose |
|---|---|
| `HINRD.BuildManifest <path>` | Builds a World Manifest (advanced use; [06](06_World_Manifest_And_World_Partition.md#build-the-world-manifest)) |
| `HINRD.CheckPackaging` | Same as **Check Packaging Readiness**; editor only, read-only ([06](06_World_Manifest_And_World_Partition.md#check-packaging-readiness)) |

---

← [10 — Changelog](10_Changelog.md) · [Documentation Index](INDEX.md) · [12 — Demo Walkthrough](12_Demo_Walkthrough.md) →
