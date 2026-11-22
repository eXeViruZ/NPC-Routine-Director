# Activities, Calendars and Profiles

This page is the reference for the data assets behind a routine: the **NPC Activity** (what an NPC does), schedule layers with the **NPC Calendar** (which days are special), and the **NPC Routine Profile** (which schedule and which brain mode an NPC uses). All of them are created in the Content Browser under **Add → Artificial Intelligence → NPC Routine**.

## Activities

An **NPC Activity** asset describes one thing an NPC does, for example `Act_Work`. Schedule entries (**Activity**) and the schedule's **Fallback Activity** refer to activities.

| Setting | Meaning |
|---|---|
| **Display Name** | Optional readable name ("Work") that the Schedule Studio, the NPC Inspector and the gameplay status show |
| **Executor Template** | The executor that performs the activity. Leave it empty for "stand and work", or choose a built-in executor (below) |
| **Timeout Sec** | Embodied timeout in world seconds (0 = none). Blueprint executors always have one: 0 uses the project setting **Default Blueprint Activity Timeout** (600 s) |
| **On Failure** | `SkipToNextEntry`, `RetryOnce` or `UseFallback` |
| **Resume Policy** | `Restart`, `Replan`, `Skip` or `Unresolved`: what happens when a running activity cannot be resumed exactly (after stream-out or a load); time-derived and checkpointable executors resume exactly and ignore it |
| **Virtual** | How the virtual simulation treats the activity |

After a load, embodied NPCs are re-placed from the loaded records; activity progress is restored where the executor supports it, according to the **Resume Policy** ([08 — Save and Load](08_Save_Load_And_Runtime_API.md#save-and-load)).

### Built-in Executors

Choose one of these as **Executor Template**: **Wait**, **Move To**, **Play Montage**, **Patrol**, **Wander**, **Interact**, **Run Behavior Tree**, **Sequence**.

- **Move To** references a Routine Spot in **Spot Id**.
- **Patrol** references Routine Spots in **Patrol Spots**.
- Both are set with the Routine Spot picker.

The shipped demo uses the built-in **Patrol** and **Wander** executors for `Act_NRD_Patrol` and `Act_NRD_Stroll` ([12 — Demo Walkthrough](12_Demo_Walkthrough.md#the-ten-examples)). The Routine Spot picker is described in [05 — Referencing a spot](05_NPC_Components_And_Routine_Spots.md#referencing-a-spot-the-routine-spot-picker).

### Custom Executors

For your own activity logic, subclass `UHINRDActivityExecutor` in Blueprint or C++ and use it in your NPC Activity assets.

| Group | Names |
|---|---|
| Events you implement | `CanStart`, `OnStart`, `OnTick`, `OnPause`, `OnResume`, `OnCancel`, `OnMoveFinished` |
| Finishing | Call `Finish(Result)` |
| Movement | `MoveTo(Goal, AcceptanceRadius)` / `StopMove` |
| Progress | `GetProgressCapability`, `CaptureProgress`, `RestoreProgress`, `RequestCheckpoint` |

Example from the shipped demo: `BP_NRD_DemoActivity_PlayLoop` is a Blueprint subclass of the plugin's Activity Executor. On Start / On Resume it shows a looping animation and a label on the villager, On Pause / On Cancel clears them, and it reports its progress as Time Derived.

## Schedule Layers and the NPC Calendar

A schedule can contain optional **Layers**: extra entry sets with **Active Day Types** and **Priority Bias**.

Day types come from an optional **NPC Calendar** asset, for day types such as a market day or a festival. A calendar's rules can be:

- weekdays,
- periodic,
- ranges,
- explicit days.

Calendars are validated like schedules; their codes start with `HINRD.CAL.0xx` ([03 — Validation](03_Schedule_Studio.md#validation)).

## NPC Routine Profile

An **NPC Routine Profile** connects an NPC to its routine. The **NPC Routine** component and the **NPC Routine Spawner** reference a profile ([05 — NPC Components and Routine Spots](05_NPC_Components_And_Routine_Spots.md)).

| Setting | Meaning |
|---|---|
| **Schedule** | The **NPC Schedule** the NPC follows |
| **Spawn Class** | Your NPC class; the class the director spawns as the NPC's embodiment |
| **Brain Mode** | How the routine and your AI share the pawn (below) |
| Profile slots | Slot locations of the profile. A Routine Spot in a profile slot is chosen with the Routine Spot picker. The Schedule Studio **Preview** uses the selected profile for slot locations |
| Virtual travel speed | Speed of the NPC in the virtual simulation |
| Approximation opt-in | The per-profile key of the two-key approximation rule; approximation is off by default ([07 — Catch-up settings](07_Time_Simulation_And_External_Clocks.md#catch-up-settings)) |

Profiles are validated with codes `HINRD.PRF.0xx` ([03 — Validation](03_Schedule_Studio.md#validation)).

### Brain Modes

| Brain Mode | Behavior |
|---|---|
| `RoutineDriven` | The routine moves the pawn and pauses your behavior tree while it does |
| `BrainHosted` | Your Behavior Tree or StateTree runs and calls the routine through nodes ([08 — AI Integration](08_Save_Load_And_Runtime_API.md#ai-integration)) |
| `ObserveOnly` | The routine never moves the pawn; your own AI keeps control |

---

← [03 — Schedule Studio and Schedule Entries](03_Schedule_Studio.md) · [Documentation Index](INDEX.md) · [05 — NPC Components and Routine Spots](05_NPC_Components_And_Routine_Spots.md) →
