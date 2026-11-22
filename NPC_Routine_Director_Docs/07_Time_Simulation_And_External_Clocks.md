# Time, Simulation and External Clocks

This page explains the NPC Routine game clock, how to skip time, how to drive the clock from your own day/night system, how World Facts let schedules react to your game, and how current an off-screen NPC is at any moment (lazy-exact simulation).

## Where the Time, World Fact and Save Nodes Are

The time, World Fact and save nodes belong to the NPC Routine simulation, the Game Instance subsystem `UHINRDSimulationSubsystem`.

1. In a Blueprint graph, right-click and search for `HINRDSimulationSubsystem`.
2. Place **Get HINRDSimulationSubsystem** (listed under **GameInstance Subsystems**).
3. Drag from its blue output pin and search for the node by name, for example **Advance Time**.

The nodes are listed under **NPC Routine → Time**, **NPC Routine → World Facts** and **NPC Routine → Save**.

In C++:

```cpp
UHINRDSimulationSubsystem* Sim = GetGameInstance()->GetSubsystem<UHINRDSimulationSubsystem>();
```

## Time Model

- Time is counted in whole game seconds since **Day 0 00:00:00**.
- Day 0's weekday is the project setting **Day 0 Weekday**.
- The valid range is Day 0 to Day 10,000.
- Time never runs backward: a target before the clock is refused and nothing changes.

## Game Clock and Time Nodes

The built-in clock runs at `Game Seconds Per Real Second` (project setting, default 60).

| Node | Purpose |
|---|---|
| **Advance Time (Seconds, bBlocking)** | Skips time forward by a number of game seconds |
| **Set Game Time (Target, bBlocking)** | Sets an absolute time, forward only ("sleep until Day 3 07:00") |
| **Get Game Time** | Current game time |
| `Get Clock` | Current clock value |
| `Set Time Scale` | Clock speed; 0 = paused |

Console command for testing:

```text
hinrd.Time.Skip <seconds>
```

With `bBlocking` = false, the records catch up within the per-frame budget; with `bBlocking` = true, before the call returns.

### Time Helpers

Blueprint category **NPC Routine|Time**:

| Node | Purpose |
|---|---|
| **Make Routine Game Time** | Builds a game time |
| **Break Routine Game Time** | Splits a game time |
| **Format Routine Game Time** | Readable text such as "Thu Day 3 07:30" |
| **Get Seconds Until Time Of Day** | Game seconds until a time of day |
| **Game Time + Seconds** | Adds seconds to a game time |

## Your Own Day/Night System (External Time Source)

If your game already has a clock, let it drive the NPC clock:

1. Implement the interface **NPC Routine Time Source** (`IHINRDTimeSource::GetRoutineGameSeconds`, game seconds since Day 0) on the object that owns your clock.
2. Call **Set External Time Source** on the simulation with that object.

The simulation then reads your clock once per tick instead of its own.

- **Your value must never decrease.** A smaller value is rejected and the clock waits (status **BehindClock**) until your clock passes it again.
- **Ordinary progress** of your clock (every frame, any step size) needs nothing else: the NPC clock follows and the records catch up within the frame budget.
- **Intentional jumps** of your clock (sleep, fast travel, "skip to morning") need a call to **Apply External Time Jump** right after your clock jumped ([below](#intentional-jumps-of-your-own-clock-apply-external-time-jump)).
- **Source destroyed:** if the source object is destroyed (for example with its level), the clock waits (**SourceLost**). Set a new source after map travel, or call **Clear External Time Source** to return to the built-in clock.

### Recommendations

- A source owned by the Game Instance (your GameInstance class or a Game Instance subsystem) keeps driving the clock through every map travel without any call.
- A source owned by a level actor is destroyed with that level. Calling **Set External Time Source** again with the same object (for example from every level's BeginPlay) is safe.
- Map travel never resets the simulation, the clock or the records.
- **Get Time Source Status** shows mode, state, last value and rejections.
- With an external source, skip time on your own clock; the Inspector's time buttons refuse.
- The source is not saved. Restore your clock from your save game before or after **Import State**; until it reaches the loaded time, the NPC clock waits.

### Intentional Jumps of Your Own Clock: Apply External Time Jump

Call **Apply External Time Jump (bBlocking)** on the simulation **in the same frame, after** your clock has jumped forward:

1. Advance your own clock (for example `GameSeconds = GameSeconds + 8 * 3600`).
2. Call **Apply External Time Jump**: `bBlocking` = false catches up within the frame budget; `bBlocking` = true catches up before the call returns, for example behind a loading screen.

The simulation reads your clock once and treats the difference exactly like **Advance Time**: the records catch up exactly, and NPCs that have an actor are re-placed from their records instead of finishing their old walk ([How Current Is an NPC?](#how-current-is-an-npc-lazy-exact-simulation)). Your source stays set; nothing else changes.

The node returns a result. Every result except `Applied` changes nothing.

| Result | Meaning |
|---|---|
| `Applied` | The NPC clock now equals your clock; the jump is a time skip |
| `NoChange` | Your clock equals the NPC clock: advance your clock first, then call. If your clock jumped in an earlier frame, the NPC clock already followed it as ordinary progress (the jump can no longer be applied as a skip) |
| `NoExternalSource` | No external time source is set: use **Advance Time** / **Set Game Time** |
| `SourceUnavailable` | Your time source object no longer exists (its level was unloaded): set it again |
| `InvalidSourceValue` | Your clock reports a value outside Day 0 to Day 10,000 |
| `BackwardRejected` | Your clock reports a time before the NPC clock (time never runs backward) |
| `NotAuthoritative` | No simulation, or a network client (single-player only) |

Every refusal is also listed as an `ExternalTimeJumpRefused` diagnostic; an applied jump as `ExternalTimeJumpApplied`.

C++: `UHINRDSimulationSubsystem::ApplyExternalTimeJump(bool bBlocking)` returns `EHINRDExternalTimeJumpResult` ([08 — C++ access](08_Save_Load_And_Runtime_API.md#c-access)).

## Catch-Up Settings

- Catch-up is budgeted per frame (`Catch Up Frame Budget Ms`, default 2 ms).
- Approximation is off and needs two keys: the project setting and a per-profile opt-in. Approximated records are marked.
- History retention: `History Retention Days` (default 14).

All settings: [13 — Project Settings Reference](13_Project_Settings_Reference.md).

## World Facts

**World Facts** are integer values keyed by a Gameplay Tag, either global or per map ("World Scoped"). Schedule entries can require them in **Fact Conditions**, for example `Weather.Raining == 1`. Facts are journaled: NPCs that are unloaded, or that catch up after a time skip, see every change at the time it happened.

| Node | Behavior |
|---|---|
| **Set World Fact (Fact, Value, World Scoped)** | Effective now. If NPCs were already evaluated at this exact game second, the change takes effect one game second later (`bDeferred`, see `EffectiveTime`) so no NPC's past is rewritten; a **Get World Fact** in the same frame still returns the previous value |
| **Set World Fact At (Fact, Value, Effective Time, World Scoped)** | Schedules a future change (a festival from Day 3 08:00). Times before the clock are refused (`RejectedPast`); history is append-only |
| **Get World Fact (Fact, World Scoped)** | Returns a status and the value |

Status values of **Get World Fact**:

| Status | Meaning |
|---|---|
| `Set` | The fact was set |
| `NotSet` | Never set; conditions compare against 0 |
| `HistoryUnavailable` | The value is not available from the history |
| `InvalidFact` | The fact is not valid |
| `NoSimulation` | No simulation exists |

Facts are part of **Export State** / **Import State** ([08 — Save and Load](08_Save_Load_And_Runtime_API.md#save-and-load)).

To read an NPC's current and next activity for gameplay, use **Get NPC Routine Status** and **Explain NPC Routine** ([08 — Status and Explain Nodes](08_Save_Load_And_Runtime_API.md#status-and-explain-nodes)).

## How Current Is an NPC? (Lazy-Exact Simulation)

NPC Routine Director does **not** move every NPC to the current clock every frame. Every NPC record is **exact at its own last evaluated time**. When a record is moved forward, every schedule boundary, World Fact change and arrival in between is processed exactly and in order; the gap is never filled with a guess. This keeps large numbers of off-screen NPCs cheap.

### Terms

| Term | Meaning |
|---|---|
| Current game clock | **Get Game Time** / `Get Clock` |
| Last evaluated NPC time | The record's `Cursor` (**Get Record View**). State, activity, destination and position of the record are the exact values at this time. It is never later than the clock |
| Virtual (off-screen) NPC | A record without a pawn; its last evaluated time may be behind the clock |
| Embodied NPC | Before a pawn is placed or resumes its routine, its record is brought exactly to the clock. If that cannot be proven (Unresolved record), the NPC is not placed (`MaterializationDeferred`); nothing is guessed |

### Embodied NPCs and Time Skips

After **Advance Time** / **Set Game Time** moves the clock forward, every embodied NPC is brought to its record before it acts again:

- its pre-skip movement is cancelled,
- it is re-placed where the record says (at a Routine Spot on its own slot; if that is not possible, it is not shown until it is: `PlacementDeferred`),
- its position from before the skip is never written back into the record.

An NPC whose movement your game holds at that moment (control lease, a foreign move, an interruption that is still running) stays with its holder.

### Jumps of Your Own Clock

Call **Apply External Time Jump** in the frame in which your clock jumped: embodied NPCs then behave exactly as after **Set Game Time** (above).

Without the call, a forward jump counts as ordinary clock flow: the records catch up, but embodied NPCs are not re-placed. An NPC that still walks its earlier route keeps doing so; if it is released or its cell unloads before it arrives, its actor's position becomes its recorded position again and it continues from there. There is no automatic jump detection (no threshold): only the call marks a jump.

An equivalent manual sequence in one frame is **Clear External Time Source**, **Set Game Time**, your clock, **Set External Time Source**; **Apply External Time Jump** is the recommended way.

### Saved State

**Export State** stores each record at its own last evaluated time, the clock, the history the records still need, and a running catch-up job. **Import State** restores exactly that.

### When Records Are Brought Forward

| Trigger | What happens |
|---|---|
| A pawn is placed (player comes near, a cell loads, after Import State) or resumes | That NPC's record is brought exactly to the clock first |
| **Advance Time** / **Set Game Time** | A budgeted catch-up job brings all records to the new clock (`bBlocking` = true: before the call returns) |
| **Apply External Time Jump** | Like Advance Time: a catch-up job brings all records to the new clock (`bBlocking` = true: before the call returns) |
| A forward jump of your external clock without Apply External Time Jump | Ordinary clock flow: the background pass below (a jump of 1 game hour or more starts the job in the same frame) |
| Background pass | While no job runs, a job starts once the clock is at least 1 game hour past the last pass; a new game and every Import State reset the last pass |
| A save taken while a catch-up job was running | Import State resumes that job |
| Interrupt Virtual, Set World Fact | The record is advanced first, or the fact change is deferred by one game second; no past is rewritten |

So a virtual NPC is usually at most about one game hour behind the clock (longer only while a large catch-up job runs).

### How to See It

**Get NPC Routine Status** reports `bCatchingUp` = true whenever the record is behind the clock. Its "current" fields then describe the NPC at its last evaluated time, while **NEXT** and **Explain NPC Routine** refer to the current clock. The NPC Inspector and the in-world labels show "(catching up)".

### When Gameplay Needs the Exact State at the Clock

For a quest check or a dialogue line:

- **Blueprint:** **Advance Time (Seconds = 0, bBlocking = true)** brings every record to the clock without moving the clock.
- **C++:** `UHINRDSimulationSubsystem::EnsureCurrent(NpcId)` for one NPC, or `RequestPriority(NpcId)` so the next budgeted job evaluates that NPC first ([08 — C++ access](08_Save_Load_And_Runtime_API.md#c-access)).
- **Get NPC Routine Status** only reads; it never advances a record.

### What You Can Rely On

- A save/load loses or invents no history. If single records in an envelope are damaged, **Import State** still succeeds, drops those records and lists them in the report (`OkWithRejectedRecords`); check the report.
- A record never moves backward.
- Once a record is brought to the clock, its state is identical to an uninterrupted exact simulation, also after a save taken during a running catch-up job.
- A record behind the clock is always reported as catching up.

Not promised: that every NPC is at the current clock every frame, or that NPCs which were behind when you saved are current right after **Import State**.

---

← [06 — World Manifest and World Partition](06_World_Manifest_And_World_Partition.md) · [Documentation Index](INDEX.md) · [08 — Save/Load and Runtime API](08_Save_Load_And_Runtime_API.md) →
