# Save/Load and Runtime API

This page covers what your game code does with NPC Routine Director at runtime: saving and loading NPC state, reading what an NPC is doing, interrupting NPCs, taking control of a pawn, connecting Behavior Trees, StateTree or custom brains, and calling all of this from C++.

## Save and Load

Saving and loading use two nodes on the NPC Routine simulation (**Get HINRDSimulationSubsystem**, see [07 — Where the nodes are](07_Time_Simulation_And_External_Clocks.md#where-the-time-world-fact-and-save-nodes-are)):

| Node | Purpose |
|---|---|
| **Export State (Envelope)** | Writes the NPC Routine state (records, clock, the history the records still need, a running catch-up job) into an `NPC Routine Save Envelope` struct |
| **Import State (Envelope, Report)** | Restores the state from an envelope; the report describes the result |

Store the `NPC Routine Save Envelope` struct in your own `USaveGame`.

### Save

1. Get the simulation with **Get HINRDSimulationSubsystem**.
2. Call **Export State**.
3. Only if **Export State** succeeded, store the envelope in your SaveGame and write your save.

The shipped demo's F5 graph shows this order ([12 — Demo Walkthrough](12_Demo_Walkthrough.md#using-the-demo-content-in-your-project)).

### Load

1. Load your SaveGame.
2. Call **Import State** with the stored envelope.
3. An incompatible envelope is refused with a report and changes nothing. If single records are damaged, the import succeeds, drops those records and lists them in the report (`OkWithRejectedRecords`).

### What a Load Restores

- **Export State** stores each record at its own last evaluated time, the clock, the history the records still need, and a running catch-up job. **Import State** restores exactly that ([07 — How Current Is an NPC?](07_Time_Simulation_And_External_Clocks.md#how-current-is-an-npc-lazy-exact-simulation)).
- World Facts are part of the saved state.
- After a load, embodied NPCs are re-placed from the loaded records; activity progress is restored where the executor supports it (**Resume Policy**, [04 — Activities](04_Activities_Calendars_Profiles.md#activities)).
- A load restores each record at the time it was last evaluated before the save. If your code needs NPC state at the clock right after **Import State**, call **Advance Time (0, bBlocking = true)** first.
- An external time source is not saved. Restore your own clock from your save game before or after **Import State**; until it reaches the loaded time, the NPC clock waits ([07 — External Time Source](07_Time_Simulation_And_External_Clocks.md#your-own-daynight-system-external-time-source)).

### Optional A/B Save-Slot Helper (C++)

C++ projects can use the optional A/B slot helper `HINRD::SaveSlots::Save` / `HINRD::SaveSlots::Load` (torn-write safe); see [C++ Access](#c-access).

## Status and Explain Nodes

These nodes (category **NPC Routine|Status**) read an NPC's routine for your own UI, dialogue or quests.

| Node | Returns |
|---|---|
| **Get NPC Routine Status (NPC)** | The current schedule entry and activity with readable names, the occurrence start and end, the destination (and Routine Spot), position certainty, interruption, and the **next** activity with its start time |
| **Explain NPC Routine** | The resolver's explanation: candidate entries and why one was chosen |
| **Get NPC Id Of Actor** | The NPC id of an actor with an NPC Routine component |

Good to know:

- **"Next" is a prediction.** It uses the World Facts known now (including scheduled future changes), assumes no interruption and looks 24 game hours ahead.
- **Problem records:** for Unresolved, Invalid or Dormant NPCs the status says so (`bHasProblem`, `ProblemReason`) and leaves activity and destination empty.
- **Catching up:** `bCatchingUp` = true whenever the record is behind the clock. The "current" fields then describe the NPC at its last evaluated time, while **NEXT** and **Explain NPC Routine** refer to the current clock ([07 — How to See It](07_Time_Simulation_And_External_Clocks.md#how-to-see-it)).
- **Get NPC Routine Status** only reads; it never advances a record.

The demo's NOW / NEXT / WHY panel is built with **Get NPC Routine Status** ([12 — Demo Walkthrough](12_Demo_Walkthrough.md#the-ten-examples)).

## Interruptions

Interrupt an NPC with **Interrupt NPC** (by NPC id; virtual NPCs need a duration) or with the NPC Routine component's **Interrupt**. Release it with **Release NPC Interrupt**.

| Request field | Meaning |
|---|---|
| **Reason** | Identifies the interruption |
| **Priority** | Priority of the interruption |
| **Policy** | `Resume`, `Restart`, `Skip` or `Replan` |
| **Duration Sec** | Game seconds; 0 = until released. **Persist When Virtual** needs a duration above 0 |
| **Persist When Virtual** | Whether the interruption persists when the NPC is virtual |

When interruptions overlap, the most disruptive policy wins.

Example from the demo: the event `InterruptSelected` in `BP_NRD_DemoPlayerController` calls **Interrupt NPC** with a reason, priority 10, policy Resume and a duration of 3600 game seconds. Afterwards the villager continues with whatever its schedule says at that time.

## Control Lease

When another system takes the pawn, it uses a control lease:

- **Acquire NPC Control** / **Release NPC Control**. A higher priority preempts; the preempted lease is reported through `On Control Lease Revoked`.
- There is exactly one movement owner at any time (**Get NPC Movement Owner**).
- **Foreign moves** while the routine owns movement are handled by the project setting `External Move Policy`: `Yield` (default) or `Warn` ([13 — Project Settings Reference](13_Project_Settings_Reference.md#navigation-and-movement)).

## AI Integration

### Behavior Tree

| Node | Type |
|---|---|
| **Run Routine Activity (NPC Routine)** | Task |
| **Publish Routine Intent (NPC Routine)** | Service |
| **Routine State (NPC Routine)** | Decorator |

### StateTree or Other Brains

With **Brain Mode** = `BrainHosted` ([04 — Brain Modes](04_Activities_Calendars_Profiles.md#brain-modes)), your brain calls the routine through these nodes:

- **Begin Hosted Routine Activity**
- **End Hosted Routine Activity**
- **Is Hosted Routine Activity Done**
- **Get Routine Intent**

Custom brains implement the interface `HINRD Brain Adapter`.

### Smart Objects

Implement the interface `HINRD Reservation Provider` in your own actor and register it with **Register Reservation Provider**.

### No Native Adapters in 1.0.0

There is no native Smart Object or StateTree adapter in 1.0.0; both are connected through the interfaces and Blueprint nodes above. The Location kind **Smart Object** is reserved: it is hidden from new authoring; existing content that uses it loads, is performed where the NPC stands, and is reported by validation (`HINRD.SCH.015` / `HINRD.PRF.005` warnings).

## C++ Access

The classes below are part of the runtime module `HINRDCore`. The simulation is a Game Instance subsystem:

```cpp
#include "Runtime/HINRDSimulationSubsystem.h"

UHINRDSimulationSubsystem* Sim = GetGameInstance()->GetSubsystem<UHINRDSimulationSubsystem>();
```

### Save and Load

```cpp
// bool UHINRDSimulationSubsystem::ExportState(FHINRDSaveEnvelope& OutEnvelope);
// bool UHINRDSimulationSubsystem::ImportState(const FHINRDSaveEnvelope& Envelope, FString& OutReport);

FHINRDSaveEnvelope Envelope;
if (Sim->ExportState(Envelope))
{
    // Store Envelope in your own USaveGame, then write the save.
}

FString Report;
if (!Sim->ImportState(Envelope, Report))
{
    // Refused: nothing changed. Report says why.
}
```

### Exact State at the Clock

```cpp
#include "Runtime/HINRDGameplayLibrary.h"

const FHINRDNpcId NpcId = UHINRDGameplayLibrary::GetNpcIdOfActor(NpcActor);

Sim->EnsureCurrent(NpcId);    // bring this NPC's record exactly to the clock
Sim->RequestPriority(NpcId);  // or: the next budgeted job evaluates this NPC first
Sim->AdvanceTime(0, true);    // or: every record to the clock, without moving the clock
```

### Readable Status

```cpp
// static bool UHINRDGameplayLibrary::GetNpcRoutineStatus(const UObject* WorldContext, FHINRDNpcId Npc, FHINRDRoutineStatus& Status);

FHINRDRoutineStatus Status;
if (UHINRDGameplayLibrary::GetNpcRoutineStatus(this, NpcId, Status) && Status.bCatchingUp)
{
    // The "current" fields describe the NPC at its last evaluated time.
}
```

### Intentional Jump of Your Own Clock

```cpp
// Your own clock has just jumped forward in this frame.
const EHINRDExternalTimeJumpResult Result = Sim->ApplyExternalTimeJump(/*bBlocking*/ false);
if (Result != EHINRDExternalTimeJumpResult::Applied)
{
    // Every result except Applied changes nothing.
}
```

### A/B Save-Slot Helper

Declarations in `HINRDSaveSlots.h` (namespace `HINRD::SaveSlots`):

```cpp
bool Save(const FString& BasePath, const TArray<uint8>& Payload, uint64* OutGeneration = nullptr, FString* OutPath = nullptr);
FLoadResult Load(const FString& BasePath);
```

### Blueprint Nodes and Their C++ Functions

| Blueprint node | C++ |
|---|---|
| **Advance Time**, **Set Game Time**, **Get Game Time**, `Get Clock`, `Set Time Scale` | `UHINRDSimulationSubsystem::AdvanceTime`, `SetGameTime`, `GetGameTime`, `GetClock`, `SetTimeScale` |
| **Set External Time Source**, **Clear External Time Source**, **Get Time Source Status**, **Apply External Time Jump** | `UHINRDSimulationSubsystem::SetExternalTimeSource`, `ClearExternalTimeSource`, `GetTimeSourceStatus`, `ApplyExternalTimeJump` |
| **Set World Fact**, **Set World Fact At**, **Get World Fact** | `UHINRDSimulationSubsystem::SetWorldFact`, `SetWorldFactAt`, `GetWorldFact` |
| **Export State**, **Import State**, **Get Record View** | `UHINRDSimulationSubsystem::ExportState`, `ImportState`, `GetRecordView` |
| **Get NPC Routine Status**, **Explain NPC Routine**, **Get NPC Id Of Actor** | `UHINRDGameplayLibrary::GetNpcRoutineStatus`, `ExplainNpcRoutine`, `GetNpcIdOfActor` |
| **Interrupt NPC**, **Release NPC Interrupt** | `UHINRDRoutineLibrary::InterruptNpc`, `ReleaseNpcInterrupt` |
| **Acquire NPC Control**, **Release NPC Control**, **Get NPC Movement Owner** | `UHINRDRoutineLibrary::AcquireNpcControl`, `ReleaseNpcControl`, `GetNpcMovementOwner` |
| **Begin Hosted Routine Activity**, **End Hosted Routine Activity**, **Is Hosted Routine Activity Done**, **Get Routine Intent** | `UHINRDRoutineLibrary::BeginHostedRoutineActivity`, `EndHostedRoutineActivity`, `IsHostedRoutineActivityDone`, `GetRoutineIntent` |
| **Interrupt** (component) | `UHINRDRoutineComponent::Interrupt` |
| **Register Reservation Provider**, **Validate Routine Spot Slots** | `UHINRDDirectorSubsystem::RegisterReservationProvider`, `ValidateSpotSlots` |
| **NPC Routine Time Source** (interface) | `IHINRDTimeSource::GetRoutineGameSeconds` |

---

← [07 — Time, Simulation and External Clocks](07_Time_Simulation_And_External_Clocks.md) · [Documentation Index](INDEX.md) · [09 — Troubleshooting and Known Limitations](09_Troubleshooting_And_Limitations.md) →
