# Project Settings Reference

NPC Routine Director's project settings are under **Project Settings → Plugins → NPC Routine Director**. This reference covers the settings that the other pages of this documentation refer to, grouped by topic, with their default values. The **Category** column names the group in which the setting appears on the settings page.

## Time

| Setting | Category | Default | Purpose | See |
|---|---|---|---|---|
| `Day 0 Weekday` | Time | Monday | Weekday of game Day 0 | [07 — Time Model](07_Time_Simulation_And_External_Clocks.md#time-model) |
| `Game Seconds Per Real Second` | Time | 60 | Speed of the built-in game clock | [07 — Game Clock](07_Time_Simulation_And_External_Clocks.md#game-clock-and-time-nodes) |

At runtime, **Set Time Scale** changes the clock speed (0 = paused).

## Catch-Up and History (Performance Settings)

| Setting | Category | Default | Purpose | See |
|---|---|---|---|---|
| `Catch Up Frame Budget Ms` | Catch-Up | 2 ms | Per-frame time budget of the budgeted catch-up | [07 — Catch-Up Settings](07_Time_Simulation_And_External_Clocks.md#catch-up-settings) |
| `Catch Up Boundary Budget Per Frame` | Catch-Up | 2000 | Per-frame boundary budget of the budgeted catch-up | [07 — When Records Are Brought Forward](07_Time_Simulation_And_External_Clocks.md#when-records-are-brought-forward) |
| `Max Boundaries Per Record Per Job` | Catch-Up | 2000000 | Maximum number of boundaries one record processes in one catch-up job | [07 — How Current Is an NPC?](07_Time_Simulation_And_External_Clocks.md#how-current-is-an-npc-lazy-exact-simulation) |
| **Allow Approximate Catch-Up (two-key opt-in, not recommended)** | Catch-Up | Off | First key of the two-key approximation rule; the second key is the per-profile opt-in. Approximated records are marked | [07 — Catch-Up Settings](07_Time_Simulation_And_External_Clocks.md#catch-up-settings) |
| `History Retention Days` | History | 14 | History retention in game days | [07 — Catch-Up Settings](07_Time_Simulation_And_External_Clocks.md#catch-up-settings) |
| `History Soft Budget Events` | History | 200000 | Soft history budget, in events | – |
| `History Hard Budget Events` | History | 1000000 | Hard history budget, in events | – |

## Navigation and Movement

| Setting | Category | Default | Purpose | See |
|---|---|---|---|---|
| `Navigation Attempts` | Embodied Execution | 3 | Path attempts before the fallback chain (fallback location, then Blocked) | [05 — Navigation](05_NPC_Components_And_Routine_Spots.md#navigation) |
| `Allow Kinematic Fallback` | Embodied Execution | Off | Optional straight-line kinematic fallback | [05 — Navigation](05_NPC_Components_And_Routine_Spots.md#navigation) |
| `Obstruction Replan Delay Sec` | Embodied Execution | 3 s | Delay before re-planning a route that was blocked by an obstruction; doubles per re-plan | [05 — Navigation](05_NPC_Components_And_Routine_Spots.md#navigation) |
| `Obstruction Replan Max Delay Sec` | Embodied Execution | 30 s | Upper limit of the doubling delay | [05 — Navigation](05_NPC_Components_And_Routine_Spots.md#navigation) |
| `Max Obstruction Replans` | Embodied Execution | 6 | Re-plans per schedule entry occurrence; 0 disables obstruction re-planning | [05 — Navigation](05_NPC_Components_And_Routine_Spots.md#navigation) |
| `External Move Policy` | Embodied Execution | `Yield` | What the routine does with foreign moves while it owns movement: `Yield` or `Warn` | [08 — Control Lease](08_Save_Load_And_Runtime_API.md#control-lease) |

## Routine Spots

| Setting | Category | Default | Purpose | See |
|---|---|---|---|---|
| `Spot Slot Spacing` | Embodied Execution | 180 cm | Ring spacing of the slots of a Routine Spot with Capacity > 1; suits capsule radii up to about 42 cm. A spot's **Slot Spacing Override** (0 = this setting) replaces it per spot | [05 — Capacity and Slots](05_NPC_Components_And_Routine_Spots.md#capacity-and-slots) |

## Label Limits (Console Variables)

The in-world label limits are console variables, not entries on the Project Settings page. They are available in development builds, not in Shipping builds.

| Console variable | Default | Purpose |
|---|---|---|
| `hinrd.Labels.MaxDistance` | 3000 cm | Label distance limit |
| `hinrd.Labels.Max` | 16 | Maximum number of labels |
| `hinrd.Labels.Stack` | 1 | Label stacking; 0 = no stacking |

See [11 — In-World Labels](11_Editor_Tools_And_NPC_Inspector.md#in-world-labels).

---

← [12 — Demo Walkthrough](12_Demo_Walkthrough.md) · [Documentation Index](INDEX.md) · [README](../README.md)
