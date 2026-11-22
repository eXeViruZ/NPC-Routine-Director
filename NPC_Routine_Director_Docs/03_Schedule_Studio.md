# Schedule Studio and Schedule Entries

The **Schedule Studio** is the editor for **NPC Schedule** assets. This page describes how to open it, its tabs, toolbar, mouse and keyboard controls, the fields of a schedule entry and the validation codes.

## Open the Schedule Studio

- Double-click an **NPC Schedule** asset in the Content Browser.
- Use the Level Editor toolbar entry **NPC Routine Director**: the main button brings the most recently used Schedule Studio to the front (no second window is opened); if none is open, the menu opens so you can pick a schedule. The arrow menu lists your NPC Schedule assets under **Schedule Studio** ([11 — Toolbar](11_Editor_Tools_And_NPC_Inspector.md#level-editor-toolbar)).
- Create a schedule in the Content Browser (**Add → Artificial Intelligence → NPC Routine → NPC Schedule**): it opens in the Schedule Studio.

An empty schedule shows how to start.

## Tabs

| Tab | Purpose |
|---|---|
| **Timeline** | Create, move and resize entries with the mouse |
| **Entries** | Sortable, searchable list of all entries |
| **Details** | Fields of the selected entry |
| **Validation** | Issues found by validation |
| **Preview** | 7-day preview with the production resolver; pick a profile for slot locations |

## Studio Toolbar

| Button | Action |
|---|---|
| **Validate** | Runs validation |
| **Add Entry** | Adds an entry |
| **Duplicate** / **Delete** | Available with a selection |
| **Day/Week** | Switches between day view and week view |
| **Next Day** | Day view: shows the next day |
| **Zoom In** / **Zoom Out** | Zooms the timeline |
| **Snap** | Snapping on the timeline |

## Mouse and Keyboard

| Input | Where | Action |
|---|---|---|
| Drag on empty space | Timeline | Create an entry |
| Drag a block | Timeline | Move the entry |
| Drag a block edge | Timeline | Resize the entry |
| Shift+drag | Timeline | Marquee selection |
| Ctrl+mouse wheel | Timeline | Zoom |
| Del | Selection | Delete |
| Ctrl+C / Ctrl+V / Ctrl+D | Selection | Copy / paste / duplicate |
| Arrow keys | Selection | Nudge |
| Shift+arrow keys | Selection | Resize |
| F | Timeline | Focus |
| Ctrl+F | Entries | Focus the search |
| Esc | Entries | Clear the search |

Every edit is one undo step. Very large schedules (around 1,000 entries) take noticeably longer per edit.

## Schedule Entries

Each entry describes when an NPC does what and where.

| Field | Meaning |
|---|---|
| Weekday mask | The days the entry applies to (Mon..Sun) |
| **Start Minute** | Start time of day in minutes, 0-1439 |
| **Duration Minutes** | 1-1440; an entry may pass midnight |
| **Priority** | When entries overlap, the higher priority wins |
| **Activity** | The **NPC Activity** to perform ([04 — Activities](04_Activities_Calendars_Profiles.md#activities)) |
| **Location** | Where the activity takes place; with **Kind** = **Spot** you pick a Routine Spot in **Spot Id** ([05 — Referencing a spot](05_NPC_Components_And_Routine_Spots.md#referencing-a-spot-the-routine-spot-picker)) |
| **Fact Conditions** | Optional: World Facts the entry requires, for example `Weather.Raining == 1` ([07 — World Facts](07_Time_Simulation_And_External_Clocks.md#world-facts)) |
| **Live Conditions** | Optional live conditions |
| **Display Name** | Readable name of the entry |

### Fallback Activity and Fallback Location

The schedule's **Fallback Activity** and **Fallback Location** are used when no entry applies or when an entry cannot be performed.

### Layers

**Layers** are optional extra entry sets with **Active Day Types** and **Priority Bias**. Day types come from an optional **NPC Calendar** ([04 — Schedule Layers and the NPC Calendar](04_Activities_Calendars_Profiles.md#schedule-layers-and-the-npc-calendar)).

### Smart Object Location Kind

The Location kind **Smart Object** is reserved: it is hidden from new authoring. Existing content that uses it loads, is performed where the NPC stands, and is reported by validation (`HINRD.SCH.015` / `HINRD.PRF.005` warnings). See [08 — AI Integration](08_Save_Load_And_Runtime_API.md#ai-integration).

## Validation

Validation runs in the Schedule Studio (**Validate** button, **Validation** tab) and on save when the editor's data validation on save is enabled (engine default). Invalid data is shown, never repaired silently.

| Code family | Checked asset |
|---|---|
| `HINRD.SCH.0xx` | NPC Schedule |
| `HINRD.CAL.0xx` | NPC Calendar |
| `HINRD.PRF.0xx` | NPC Routine Profile |

Codes named in this documentation:

| Code | Meaning |
|---|---|
| `HINRD.SCH.015` | Warning: a schedule entry uses the reserved Location kind **Smart Object** |
| `HINRD.PRF.005` | Warning: a profile slot uses the reserved Location kind **Smart Object** |

## Typical Workflow

1. Create or open an **NPC Schedule**.
2. Drag on the timeline to create entries, or click **Add Entry**.
3. In **Details**, set **Activity** and **Location** for each entry.
4. Set the **Fallback Activity** and **Fallback Location** (used when no entry applies or an entry cannot be performed).
5. Click **Validate** and fix everything listed in the **Validation** tab.
6. Check the result in the **Preview** tab, with a profile selected if the schedule uses slot locations.
7. Save the schedule.

---

← [02 — Quick Start](02_Quick_Start.md) · [Documentation Index](INDEX.md) · [04 — Activities, Calendars and Profiles](04_Activities_Calendars_Profiles.md) →
